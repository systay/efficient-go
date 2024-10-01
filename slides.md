---
theme: default
background: /background.jpg
class: 'text-center'
highlighter: shiki
lineNumbers: false
info: |
  ## Efficient Go
  Chapters 1 & 3: Software Efficiency Matters and Conquering Efficiency
  Presentation slides for developers.
drawings:
  persist: false
css: unocss
---

# Efficient Go
## Chapter 1: Software Efficiency Matters

---

# Performance Unpacked

- Performance = Accuracy * Efficiency * Speed
- Focus on efficiency often leads to better speed
- Efficiency: doing less work, using fewer resources

<!--
What does the word ‘performance’ actually mean?

Often, people think of performance as just speed, but it’s much more than that.

First, there’s accuracy. How many errors are we producing? We can be extremely fast, but if we’re frequently making mistakes, or error out, can we really say we’re performant?

Then there’s efficiency, which is about the ratio of productive output to input energy. How much memory is required for a given input size? How many CPU cycles are consumed per input? It’s important to be clear about what we’re optimizing—whether it’s memory, disk, network, or CPU.

Finally, we have speed, which refers to the wall time taken to complete a task.

These three aspects are interconnected. 
Efficient systems tend to fail less, which means fewer errors and often faster execution. 
So, by focusing on efficiency, we generally improve both accuracy and speed.
-->

---

# Common Misconceptions

1. Optimized code is not readable
2. You Aren't Going to Need It (YAGNI)
3. Hardware is getting faster and cheaper
4. We can scale horizontally instead
5. Time to market is more important

<!--
These are common arguments against optimising code. We'll go into details on each of these
-->

---

# Misconception 1: Optimized Code is Not Readable

- Efficiency and readability can coexist
- Example: Pre-allocating slices in Go

```go
// Less efficient, but readable
slice := []string{}
for i := 0; i < n; i++ {
    slice = append(slice, "item")
}

// More efficient and still readable
slice := make([]string, 0, n)
for i := 0; i < n; i++ {
    slice = append(slice, "item")
}
```

<!--
The difference between these two code blocks may look small, but one is much more efficient than the other.

In the first version, we’re appending to the slice without pre-allocating space. This can cause the slice to reallocate memory multiple times as it grows. In the second version, by pre-allocating the slice to its expected size, we avoid those extra allocations, which improves performance—and it’s still readable.

Another simple but effective technique is storing the result of a method call in a variable, instead of calling the method over and over. It saves on performance and keeps the code clean.
-->

---

# Misconception 2: YAGNI and Efficiency

- YAGNI is valuable, but don't ignore efficiency completely
- Small efficiency habits should be part of development
- Balance between premature optimization and premature pessimization

<!--
YAGNI, or ‘You Aren’t Going to Need It,’ is about avoiding unnecessary work. 
But it doesn’t mean we should ignore efficiency.

YAGNI helps avoid overengineering, but small efficiency habits, like proper memory allocation, 
should be part of everyday coding.

There’s a balance:

	•	Premature optimization wastes time on problems that don’t exist yet.
	•	Premature pessimization leads to inefficient code that’s expensive to fix later.

For example, pre-allocating slices in Go when you know the size is a simple, efficient habit.

The goal is clean, maintainable code that’s reasonably efficient from the start, without getting lost in unnecessary optimization
-->

---

# Misconception 3: Hardware Advancements

- Hardware is improving, but not fast enough
- Software demands grow faster than hardware capabilities
- Efficiency helps with energy consumption and battery life

<!--
We can’t rely on hardware improvements to solve all our problems. 
While hardware is getting stronger, the amount of data and the complexity of calculations are growing even faster.

Writing efficient code doesn’t just improve performance—it also reduces energy consumption. 
Less computation means less heat, less electricity, and ultimately, a smaller environmental impact.
-->

---

# Misconception 4: Horizontal Scaling

- Horizontal scaling introduces complexity
- Premature scalability can be worse than premature optimization

<!--
Scaling up—just getting bigger machines—is often the easiest way to handle growth. 
But eventually, the cost of these large machines becomes prohibitive. 
So, we look to horizontal scaling, like using multiple smaller machines instead of one big one, similar to RAID disks.

The downside is that horizontal scaling adds a lot of complexity, something we’re all familiar with.
Jumping to this too early can be worse than premature optimization.
If we focus on making the code efficient enough to avoid scaling out, we can save both money and complexity.
-->

---
layout: image-right

# the image source
image: /cyberpunk-2077.jpeg
---

# Misconception 5: Time to Market

- Efficiency can be a market differentiator
- Poor performance can damage reputation and financials
- Example: Cyberpunk 2077 launch issues
<!--
Some believe getting to market quickly is more important than writing efficient code. 
But this can be risky. 
If your software is slow, buggy, or resource-intensive, it can hurt both your reputation and your bottom line.

On the other hand, if your product is faster, more reliable, and uses fewer resources than your competitors, that’s a clear market advantage.

A good example is the Cyberpunk 2077 launch. 
It was rushed to market, full of bugs and performance issues, and was even pulled from the PlayStation store.
-->
---

# Key Takeaways

1. Focus on efficiency before speed
2. Efficient code is often faster and more portable
3. Consider efficiency early in the development process
4. Challenge common misconceptions about performance

<!--
Verse 1:
Focus on efficiency,
before you go for speed.
Your code will run more easily,
and give you what you need.
Efficient code hopp fallera,
oh efficient code hopp fallerej,
is fast and light, hopp fallera,
in every way, hopp fallerej.

Verse 2:
Start early when you’re writing,
it’s key to do it right.
Challenge myths about performance,
and bring the truth to light!
Challenge them, hopp fallera,
oh challenge them, hopp fallerej,
and find the way, hopp fallera,
to make it bright, hopp fallerej.
rr-->

---
layout: cover
background: /background.jpg
---

# Chapter 3: Conquering Efficiency

---

# Optimization Types

- Reasonable Optimizations
  - Eliminate "waste" without sacrificing other qualities
  - Part of coding hygiene
- Deliberate Optimizations
  - Require trade-offs
  - Need separate development phase and measurement

<!--
Reasonable optimizations are about eliminating waste without sacrificing anything else. 
These are straightforward, like reusing a calculation instead of repeating it, removing unused debug info in production builds, or pre-sizing slices and maps when we know their size in advance. 
These should always be done and are part of good coding practice—there’s no cost to fixing them.

Deliberate optimizations, on the other hand, require careful decisions. 
They’re about trade-offs, like memory for CPU. 
For example, building a hashmap to speed up comparisons, or trading CPU time for readability, like using yacc vs parser combinators. 
In Vitess, this might mean balancing more plan time against faster runtime
-->

---
layout: image
backgroundSize: contain
image: /impact-of-performance-change.png
---

---

# Resource-Aware Efficiency Requirements (RAER)

- Formal efficiency goals for software
- Includes:
  - Operation description
  - Dataset characteristics
  - Maximum latency
  - Resource consumption limits

Example:
```
Operation: "Fetching alerting rules for one tenant"
Dataset: "100 tenants, 1000 rules each"
Max Latency: "2s for 90th percentile"
Memory Limit: "500 MB"
```

<!--
Just like we have functional requirements, we should have Resource-Aware Efficiency Requirements (RAERs). 
It talks about latency and resource utilisation in a way that is pretty straightforward to test
These could be part of our RFCs, when that makes sense.
-->

---
layout: image-right
backgroundSize: contain
image: /levels.png
---

# Optimization Design Levels

1. System level
2. Algorithm and data structure level
3. Implementation (code) level
4. Operating system level
5. Hardware level
<!--
Optimization can happen at different levels.
At the system level, we are talking about where logic lives and how the different modudules interact.
Example here would be the suggestion from Vicent of rewriting the vttablet so it lives inside of MySQL.

Algorithm and data structure level optimizations are about choosing the right algorithm or data structure for the job.
Example of this could be the value joins optimisation. 

Code level is how well the algorithm is implemented.
I've done a couple of PRs lately where I microbenchmarked the code and from that found some inefficiencies that I could fix.

Operating system level optimizations are about tuning the OS to run the software better. 
They are often out of scope for us. 

Hardware level optimizations are about choosing the right hardware for the job.
I think the Bare Metal project is a good example of this, but I might be wrong.
-->
---
layout: image
backgroundSize: contain
image: /flow.png
---

---

# Key Takeaways

- Focus on efficiency before speed
- Set clear, measurable efficiency goals (RAERs)
- Use a systematic approach to handle efficiency issues
- Understand different optimization levels

---
layout: end
---
# Discussion
