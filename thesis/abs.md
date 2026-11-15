This thesis investigates whether embedded system hardware designed for a 
large commercial heating, ventilation, and air-conditioning (HVAC) controller, 
built around an NXP i.MX 8M Plus system-on-module, can support useful 
machine-learning workloads at the edge. On-device training and inference could 
enable local analysis without relying on cloud processing, but their
computational demands may exceed the practical time and resource limits of an 
embedded controller. The problem addressed is whether the current hardware can 
perform these tasks for relevant workloads, or whether a more capable platform 
is needed.

The controller was tested with regression and classification workloads using
datasets of varying sizes, and with a small MicroGPT model; execution times
were compared with those on a MacBook Pro Laptop. The controller completed the
tested tasks, including training the language model, but execution time rose
substantially as dataset size and model complexity increased, with some
MicroGPT runs taking many hours. The results indicate that the i.MX 8M Plus
can support bounded edge machine-learning workloads, while more demanding or
time-sensitive applications require workload-specific validation. For the
tested Gryphon platform, the findings do not warrant an immediate hardware
redesign when workloads are selected to fit the controller's measured limits.
