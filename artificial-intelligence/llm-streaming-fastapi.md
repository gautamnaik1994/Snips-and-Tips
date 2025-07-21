# LLM streaming FASTAPI

**main.py**

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
from fastapi.staticfiles import StaticFiles  
from langchain.callbacks.streaming_aiter import AsyncIteratorCallbackHandler
from langchain.callbacks.manager import AsyncCallbackManager  
from langchain.chat_models import init_chat_model
import asyncio
import os

GROQ_API_KEY = "key"
os.environ["GROQ_API_KEY"] = GROQ_API_KEY

app = FastAPI()

app.mount("/static", StaticFiles(directory=".", html=True), name="static")


@app.get("/stream")
async def stream_response(query: str):
    callback = AsyncIteratorCallbackHandler()
    manager = AsyncCallbackManager([callback])

    llm = init_chat_model(
        model="groq:llama-3.1-8b-instant",
        streaming=True,
        callback_manager=manager,
        temperature=0
    )

    async def sse_generator():
        task = asyncio.create_task(llm.apredict(query))
        async for token in callback.aiter():
            yield f"data: {token}\n\n"
        yield "data: [DONE]\n\n"
        await task

    return StreamingResponse(sse_generator(), media_type="text/event-stream")

```

**index.html**

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>LLM Streaming Demo</title>
</head>

<body>
    <h1>LLM Streaming Demo</h1>
    <div id="output"></div>
    <script>
        const outputDiv = document.getElementById("output");
        const eventSource = new EventSource("/stream?query=Tell me a story about a brave knight");
        eventSource.onmessage = function (event) {
            console.log("Token:", event.data);
            if (event.data === "[DONE]") {
                eventSource.close();
            } else {
                outputDiv.textContent += event.data;
            }
        };
    </script>
</body>

</html>
```

#### SSE Format: What the Browser Expects

Per the **SSE spec** (WHATWG + HTML Living Standard), each **event** must follow this format:

```
data: message line 1
data: message line 2
event: optional-event-type
id: optional-id
```

To break it down:

* Each event must end with **two newlines** (`\n\n`) — this signals the browser that the message is complete.
* The `data:` prefix is **required** for each line of the message.
* The browser will call `EventSource.onmessage` with **the combined lines of `data:` fields**, joined with `\n`.

### Streaming Gotchas in GenAI Apps

#### 1. **Text-only protocols (SSE)**

* **Gotcha:** SSE only supports UTF-8 text — no binary.
* **Impact:** You must serialize JSON and can't send files, images, or audio directly.
* **Tip:** Use `json.dumps()` carefully and keep lines intact.

***

#### 2. **Partial message boundaries**

* **Gotcha:** SSE messages must end with `\n\n`. If you forget this, the frontend may never receive the message.
* **Impact:** Frontend appears frozen even though server is sending data.
* **Tip:** Always end each chunk with `\n\n`. In Python: `yield f"data: {chunk}\n\n"`

***

#### 3. **Buffering delays**

* **Gotcha:** Server or proxy buffering can delay delivery (Nginx, Gunicorn, Cloudflare).
* **Impact:** Tokens generated are stuck in buffer and not flushed.
* **Fixes:**
  * Use `flush=True` (e.g., `sys.stdout.flush()` or `response.flush()`).
  * Disable buffering at proxy level (e.g., `proxy_buffering off;` in Nginx).
  * Consider `uvicorn --http h11` for lower-level streaming in dev.

***

#### 4. **Browser limits and behavior**

* **Gotcha:** SSE can silently reconnect, replay events, or break if messages are malformed.
* **Impact:** You may accidentally duplicate or drop tokens.
* **Fixes:**
  * Use `id:` in SSE messages for replay safety.
  * Catch and deduplicate based on `event.lastEventId` if needed.

***

#### 5. **Error handling during generation**

* **Gotcha:** If your LLM toolchain fails midway (e.g., OpenAI rate limit or tool error), stream dies without clear signal.
* **Fix:** Send custom `event: error` blocks or a `[ERROR]` marker with SSE.
* **Frontend Tip:** Implement `eventSource.onerror` or a timeout.

***

#### 6. **Token join glitches**

* **Gotcha:** Tokens may be split mid-word or mid-sentence, especially if using a tokenizer.
* **Impact:** Client sees broken words like `"hel"` `"lo"` or `"Th"` `"is"`.
* **Fixes:**
  * Join tokens before display (`+=`), but clean up later.
  * Optionally add a debounce or small buffer to group tokens.

***

#### 7. **LLM context truncation or restart**

* **Gotcha:** If the model restarts due to context overflow, the stream might appear to “repeat” earlier responses.
* **Tip:** Always track and trim the token history sent to the LLM (e.g., sliding window).

***

#### 8. **Streaming across agent steps / tool calls**

* **Gotcha:** LLM stops mid-stream to call a tool, then resumes — you must handle this explicitly.
* **Fix:**
  * Use LangChain's `AsyncIteratorCallbackHandler`.
  * Manually yield tool responses if needed.

***

#### 9. **Async / concurrency pitfalls**

* **Gotcha:** Blocking I/O (e.g., slow tool calls or DB fetches) will stall the stream.
* **Tip:** Use `async def`, `await`, and `asyncio.create_task()` properly in your generators.

***

#### 10. **Frontend rendering latency**

* **Gotcha:** DOM updates for every token cause slow UI or flicker.
* **Fixes:**
  * Use `requestAnimationFrame` or throttled rendering.
  * Buffer tokens and flush to DOM in small batches (e.g., 50ms intervals).

***

#### Bonus: Security + Compliance

| Risk                                       | Fix                                   |
| ------------------------------------------ | ------------------------------------- |
| SSE may leak tokens in error messages      | Sanitize all logs and tool responses  |
| Overstreaming can DDoS your client         | Add rate limits or chunk caps         |
| Don’t stream sensitive info token-by-token | Filter or post-process before sending |

***

### TL;DR Best Practices

| Area           | Practice                                                    |
| -------------- | ----------------------------------------------------------- |
| Backend        | Use `StreamingResponse`, flush properly, break data cleanly |
| Frontend       | Use `EventSource`, `onmessage`, debounce rendering          |
| Error Handling | Send `[DONE]` and `[ERROR]`, reconnect if needed            |
| UX             | Start fast (first token in <500ms), stream naturally        |
| Tool Calls     | Interleave tool results with generation steps               |
