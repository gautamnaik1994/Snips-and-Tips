# FineTuning Data Preprocessing

Below is **a complete, explicit, token-level walkthrough** of what happens to your data point as it moves through **every step** of your preprocessing pipeline.\
This will show **why shifting is required**, **what masking does**, and **how the final `input_ids` and `labels` look**.

***

## ✅ **Your input datapoint**

```python
messages = [
    {"role": "system", "content": "You are a assistant responsible for classifying mental health status."},
    {"role": "user", "content": "I am depressed and want to die"},
    {"role": "assistant", "content": "based on what you described it's depression'"}
]
```

Your training job’s goal:

> **Given the system + user message, predict the assistant message.**

***

## STEP 1 — Chat template formatting

Using:

```python
tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=False)
```

For Gemma-style templates, the output becomes roughly:

```
<bos><start_of_turn>system
You are a assistant responsible for classifying mental health status.<end_of_turn>
<start_of_turn>user
I am depressed and want to die<end_of_turn>
<start_of_turn>model
based on what you described its 'depression'<end_of_turn>
```

Then you remove `<bos>`:

```
<start_of_turn>system
You are...<end_of_turn>
<start_of_turn>user
I am...<end_of_turn>
<start_of_turn>model
based on...<end_of_turn>
```

***

## STEP 2 — Build _prompt only_ version

We take all messages **except the last assistant message**:

```python
prompt_messages = messages[:-1]
```

Template becomes:

```
<start_of_turn>system
You are ...<end_of_turn>
<start_of_turn>user
I am depressed ...<end_of_turn>
<start_of_turn>model
```

Because:

```python
add_generation_prompt=True
```

Gemma inserts a **generation start** after the assistant role.

***

## STEP 3 — Tokenization

Assume the tokenizer produces something like:

```
full_tokenized["input_ids"]:
[101, 4001, 22, ... , 9002, 17, 55, 81, 8821, 902, 5]   # ← includes the assistant answer

prompt_tokenized["input_ids"]:
[101, 4001, 22, ... , 9002, 17]                         # ← stops before assistant content
```

Let’s illustrate with symbolic tokens for clarity:

```
Full input_ids:
[F_sys,   “You”, ..., F_user, “I am…”, F_assist, "based", "on", "what", ... "depression", EOS]

Prompt input_ids:
[F_sys,   “You”, ..., F_user, “I am…”, F_assist]
```

Length examples:

```
full      = 100 tokens  
prompt    = 60 tokens
```

***

## STEP 4 — Build initial labels

Before masking:

```
labels = [
  F_sys, "You", ..., F_assist, "based", "on", ..., "depression", EOS
]
```

***

## STEP 5 — Mask the prompt tokens

You mask the first `prompt_length` tokens using `IGNORE_INDEX` (−100):

```
labels (before shift):
[ -100, -100, -100, ... -100, "based", "on", "what", ..., "depression", EOS]
           ↑                 ↑
  first 60 masked       assistant answer kept
```

So only **assistant message tokens** remain as supervised labels.

***

## STEP 6 — Shift tokens for next-token prediction

This is the important part.

You perform:

```python
input_ids = full_tokenized["input_ids"][:-1]
labels    = labels[1:]
```

Let’s inspect what this does.

***

### 🎯 After shifting:

#### **input\_ids**

Drop the last token:

```
[F_sys, "You", ..., "depression"]
```

#### **labels**

Drop the first token:

```
[-100, -100, ... -100, "based", "on", ..., EOS]
```

Now positions align like this:

```
input_ids[i] → model predicts → labels[i]
```

Example:

| Position | input             | label     | Meaning                                          |
| -------- | ----------------- | --------- | ------------------------------------------------ |
| 0        | `<start_of_turn>` | `IGNORE`  | Don't learn                                      |
| 1        | "You"             | `IGNORE`  | Don't learn                                      |
| ...      | ...               | ...       | ...                                              |
| 59       | `<assistant>`     | `"based"` | Model learns to output start of assistant answer |
| 60       | `"based"`         | `"on"`    | Model learns the next token                      |
| 61       | `"on"`            | `"what"`  | next-token prediction                            |
| …        | …                 | …         | …                                                |
| 90       | `"depression"`    | `EOS`     | complete answer                                  |

### How data looks before and after dropping

Great — let's be **very explicit** about _what exactly gets dropped_ when we do:

```python
input_ids = full_tokenized["input_ids"][:-1]
labels    = labels[1:]
```

This operation drops **one token from `input_ids`** and **one token from `labels`**, but **from opposite ends**.

Below is the clearest possible explanation.

***

## ✅ **What exactly is dropped from `input_ids`?**

#### 👉 The **last token** of the full sequence.

Example full sequence (symbolic tokens):

```
full_tokenized["input_ids"] =
[
   F_sys, "You", ..., "depression", EOS
]
```

`EOS` = end-of-sequence token (or `<end_of_turn>`)

When you do:

```python
input_ids = full_tokenized["input_ids"][:-1]
```

You get:

```
input_ids =
[
   F_sys, "You", ..., "depression"
]
```

#### ✔️ **Dropped token = the last token (usually EOS / end-of-turn).**

***

## ❗ WHY do we drop the last token?

Because for next-token prediction:

> The model outputs a prediction at each position _i_ based on input at position _i_.

But there is **no next token after EOS**.\
So we remove the last input position to keep input and labels aligned.

***

## ✅ **What exactly is dropped from `labels`?**

When you shift labels:

```python
labels = labels[1:]
```

You remove the **first token** from the label sequence.

Before shifting:

```
labels_before_shift =
[
    [-100], [-100], ... [-100],           # masked prompt tokens  
    "based", "on", ..., "depression", EOS
]
```

After shifting:

```
labels_after_shift =
[
    [-100], ... [-100], "based", "on", ..., EOS
]
```

#### ✔️ **Dropped token = the first token (which matches input\_ids\[0])**

The first label corresponds to predicting the “next token” for something that is not supposed to be predicted.

You remove it so that:

```
input_ids[i] aligns with labels[i]
```

***

## 🔍 Summary Table — EXACTLY What Gets Dropped

| Operation        | From Where?       | What Gets Dropped?               | Why?                                           |
| ---------------- | ----------------- | -------------------------------- | ---------------------------------------------- |
| `input_ids[:-1]` | end of input\_ids | the **last token** (usually EOS) | no next token to predict after EOS             |
| `labels[1:]`     | start of labels   | the **first label token**        | label\[0] has no input\_ids\[-1] to align with |

***

## ⚡ Visual Alignment

Before:

```
input_ids: [t0, t1, t2, t3, EOS]
labels:    [-100, -100, "based", ..., EOS]
```

After shifting:

```
input_ids: [t0, t1, t2, t3]        ← last token dropped
labels:    [-100, "based", ...]     ← first label dropped
```

Now:

```
input_ids[i] → predicts → labels[i]
```

Perfect alignment.
