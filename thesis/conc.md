Conclusion
==========

\label{sec:conc}

This study evaluated whether the existing Gryphon controller, based on the
NXP i.MX 8M Plus, can support edge inference, prediction, and on-device
training without a hardware redesign. The experiments show that it can:
the controller completed the tested regression and classification workloads
and trained a small MicroGPT model using a conventional software stack.
Although it was consistently slower than the comparison laptop, its
performance was adequate for the smaller workloads tested.

The results also define the limits of that conclusion. Runtime increased
substantially with dataset size and model complexity, reaching many hours
for the larger MicroGPT configurations. The i.MX 8M Plus is therefore a
viable platform for bounded edge-AI tasks, but these tests do not establish
it as a replacement for more capable hardware in latency-sensitive or
computationally intensive applications. Because this work evaluated a
prototype rather than a production-hardened system, deployment should
include workload-specific latency and system-behavior validation. For the
Gryphon platform, the evidence does not justify an immediate hardware
redesign: useful AI capabilities are feasible on the existing hardware
when the workload is matched to its measured limits.
