# EE6316 | CA1 Recap

Parallelism, quantization, sparsity, memory bandwidth and neuron updates.

Use the accompanying numerical data where provided; otherwise express calculations symbolically.

## Question 1 - Parallelism, quantization and sparsity

A workload has $P$ image patches and $F$ filters. Each patch-filter pair requires a length-$K$ dot product. The SIMD engine has $L$ lanes, with $L<K$, so a dot product takes more than one vector instruction.

(a) Determine the number of dot products, $PF$, and the total scalar multiplications, $PFK$.

(b) For one dot product, find the vector-multiply instruction count $\lceil K/L\rceil$, issued lane-slots, useful lane-slots and lane utilization. Extend the counts to the entire workload and suggest how idle lanes could be used.

$$
\mathrm{Useful\ slots}:\ K\qquad\mathrm{Issued\ slots}:\ L\left\lceil\frac{K}{L}\right\rceil
$$

(c) A pipelined engine delivers its first result at a stated cycle and one result per cycle thereafter. For the dot-product count in part (a), find the completion cycle and the fraction of cycles before the first result. Explain why pipeline fill matters less as the workload grows.

(d) After pruning, a stated fraction of the multiplications in part (a) remain useful; the others multiply by zero. Dense execution uses one energy unit per multiplication. Sparse execution pays only for useful products but adds a stated fixed overhead for indices, metadata and control. Find dense energy, sparse energy, the percentage saved or lost, and the break-even overhead. Explain why zero weights alone do not save energy on dense hardware that cannot skip them.

(e) Quantize the supplied real values to an unsigned $b$-bit code over $[0,X_{\max}]$. Derive the scale below and, for each value, calculate the scaled input, rounded code, stored code, reconstruction and signed error. Classify each nonzero error as rounding or clipping, according to whether saturation changes the rounded code. Give the storage reduction relative to the stated higher-precision format, such as FP16.

$$
s=\frac{X_{\max}}{2^b-1},\qquad q=\mathrm{clip}\!\left(\mathrm{round}\!\left(\frac{x}{s}\right),0,2^b-1\right)
$$

$$
\hat{x}=s\,q,\qquad \varepsilon=\hat{x}-x
$$

| $x$ | $x/s$ | $\mathrm{round}(x/s)$ | $q$ | $\hat{x}$ | $\hat{x}-x$ | Error type |
| --- | --- | --- | --- | --- | --- | --- |
|  |  |  |  |  |  |  |
|  |  |  |  |  |  |  |

Use one row for each input value.

## Bonus - A leaky neuron update

The neuron uses the update rule below. A spike occurs if the pre-reset value reaches $V_{\mathrm{th}}$; the state is then reset immediately to zero. Use the supplied values of $V[t]$, $\alpha$, $I[t]$ and $V_{\mathrm{th}}$.

$$
V[t+1]=\alpha V[t]+I[t],\qquad \mathrm{spike\ if}\quad V[t+1]\geq V_{\mathrm{th}}
$$

(a) Find the pre-reset value, decide whether a spike occurs, and state the final value after any reset.

(b) Give one conceptual reason why event-driven computation can reduce work or energy compared with updating every unit on every clock tick.

## Question 2 - Storage, bandwidth and power

A model has $N$ weights and generates tokens at rate $R$ tokens/s. Initially, its complete weight set is read from off-chip memory once per token. Compare the higher-precision format A with the lower-precision or quantized format B, using each format's bytes per weight, off-chip read energy per weight, and arithmetic energy per weight use.

Let the symbols below denote the storage and energy parameters for each format.

| Quantity | Format A | Format B |
| --- | --- | --- |
| Bytes per weight | $b_A$ | $b_B$ |
| Off-chip read energy per weight | $e_{r,A}$ | $e_{r,B}$ |
| Arithmetic energy per weight use | $e_{a,A}$ | $e_{a,B}$ |

(a) Explain why peak arithmetic throughput alone does not determine real system power or latency. Name one system-level effect omitted from the simplified calculations below.

(b) For each format, calculate total weight storage and the ideal weight-memory bandwidth needed at rate $R$. Use storage in bytes and bandwidth in bytes/s, or convert consistently to the units in the original table.

(c) For each format, combine off-chip read energy and arithmetic energy over $N$ weights to obtain energy per token. Convert it to average power at rate $R$, then find the percentage power reduction from A to B.

(d) Now let a stated fraction $f$ of format-B weights be supplied on-chip for each token. These weights require no off-chip read but still incur arithmetic energy. Recalculate off-chip bandwidth, energy per token and power using the same simplified energy model. Report the additional percentage power reduction relative to uncached format B in part (c).

(e) Using the same on-chip fraction $f$, determine the remaining DRAM bytes per token. Combine this with the stated sustained DRAM bandwidth, in GB/s, to obtain the maximum sustainable token rate under the bandwidth-only model.
