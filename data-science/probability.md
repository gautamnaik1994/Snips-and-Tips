# Probabilty

## Binomial Probability

The binomial distribution is widely applicable in various real-world scenarios where there are a fixed number of independent trials, each with only two possible outcomes (success or failure), and the probability of success remains constant across trials. Here are some examples:

1. **Coin Flipping:**
   - Each flip of a fair coin is a binomial trial.
   - Success (head) or failure (tail) is determined by the outcome of each flip.
   - Probability of getting a head (success) in a single flip is 0.5.
2. **Dice Rolling:**
   - Rolling a fair six-sided die can be considered a binomial trial.
   - Success (rolling a specific number) or failure (rolling any other number) is determined by the outcome of each roll.
3. **Biological Experiments:**
   - In genetic experiments involving the inheritance of traits, outcomes can often be modeled using a binomial distribution.
   - For example, the probability of inheriting a specific genetic trait from a parent.
4. **Quality Control:**
   - In manufacturing, when products are inspected for defects, each product can be a binomial trial.
   - Success is having a defect, and failure is being defect-free.
5. **Survey Responses:**
   - In surveys with binary responses (yes/no questions), each response can be considered a binomial trial.
   - Success might be a positive response, and failure a negative response.
6. **Medical Testing:**
   - Diagnostic tests with binary outcomes (positive/negative) can be modeled using the binomial distribution.
   - The probability of a positive result depends on the sensitivity and specificity of the test.
7. **Political Elections:**
   - Modeling the number of votes for a candidate in a series of independent precincts or districts.
8. **Internet Security:**
   - Modeling the success or failure of an intrusion detection system in identifying malicious activity.
9. **Sports Performance:**
   - Modeling the probability of a basketball player making a free throw in a series of attempts.
10. **Coupon Collection:**
    - The probability of collecting a complete set of coupons from a random distribution.

In each of these examples, the binomial distribution provides a useful framework for analyzing the probability of a specific number of successes in a fixed number of trials with two possible outcomes.

$$P(X = k) = \binom{n}{k} p^k (1 - p)^{n - k}$$

## Question

In a factory, the probability of producing a defective bulb is 0.25. A sample of 40 bulbs is collected.

What is the probability that exactly 10 bulbs are defective?

```python
from scipy.stats import binom
binom.pmf(k=10, n=40, p=0.25) # 0.14
```

Article on different distributions
https://medium.com/mlearning-ai/9-important-data-distributions-real-world-examples-for-each-b804d9d95fe7

https://medium.com/@snehabajaj108/the-poisson-exponential-distribution-using-python-2e9959fdcbc7

https://medium.com/@nitin.data1997/p-value-explained-to-a-10-year-old-kid-bc9649c32dd2

## Rules of Probability

P(A|B) = P(A ∩ B) / P(B)

### Adition Rule

P(A U B) = P(A) + P(B) - P(A ∩ B)

### Multiplication Rule

P(A ∩ B) = P(A|B) . P(B)

If A and B are independent then

P(A ∩ B) = P(A) . P(B)

## Distributions

### Poisson Distribution

The Poisson distribution is a discrete probability distribution that describes
the number of events that occur within a fixed interval of time.

#### Applications:

- Traffic Flow: Modeling the number of cars passing through a toll booth in a given time period.
- Call Centers: Predicting the number of calls a call center receives in a specific time frame.
- Rare Diseases: Estimating the number of rare diseases in a population.
- Natural Disasters: Modeling the occurrences of earthquakes, floods, or other rare events.

### Exponential Distribution

The exponential distribution is a continuous probability distribution that
describes the time between events in a process where events occur continuously
and independently at a constant average rate. It is often used to model the time
until the next occurrence of an event.

The memoryless property makes the exponential distribution suitable for modeling
waiting times. For example, if you have been waiting for a bus for 10 minutes, the
probability that the bus will arrive in the next 5 minutes is the same as if you had
just started waiting.

#### Applications

- Reliability Engineering: Modeling the time until a component or system fails.
- Queuing Theory: Representing the inter-arrival times of customers at a service point.
- Telecommunications: Modeling the time between phone calls or packet arrivals in data networks.

### Geometric Distribution

The geometric distribution is a probability distribution that models the number
of trials needed to achieve the first success in a sequence of independent and
identically distributed Bernoulli trials. In each trial, there are only two
possible outcomes: success (usually denoted as 1) or failure (denoted as 0).

One notable property of the geometric distribution is the memoryless property.
This means that the probability of having to wait n+k trials
(where n is the current number of trials and k is a positive integer)
for the first success, given that n trials have already passed without success,
is the same as the probability of having to wait k trials from the beginning.

