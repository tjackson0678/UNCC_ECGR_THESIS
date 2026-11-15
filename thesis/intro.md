# Introduction
\label{ch:intro}

For decades, embedded systems operated quietly in the background,
engineered primarily for low-power control tasks, deterministic
timing, and simple signal processing rather than heavy computation
\cite{wolf2016computers}. From household appliances
to basic industrial sensors, these specialized devices were
designed to handle lightweight operations reliably without
needing massive processing power. However, the recent explosion
of interest in artificial intelligence has fundamentally shifted
this paradigm, driving a major push to move complex machine
learning tasks out of the cloud and directly onto edge devices
\cite{zhou2019edge, ray2022review}. As a result, the boundary
between traditional microcontrollers and high-performance
computing has blurred, forcing embedded systems designers to
adapt to the unique computational and memory demands of modern
AI workloads.

While edge inference has largely matured into a commercially
viable domain through optimized runtime frameworks such as
TensorFlow Lite \cite{david2021tfmlite}, recent academic 
and industrial focus has shifted toward a far more challenging frontier:
in-situ (on-device) training for deeply resource-constrained hardware 
\cite{vita2023ondevice}. Unlike inference, local training requires 
backpropagation and gradient tracking, demands that quickly saturate 
the severely limited RAM and non-volatile storage typical of 
microcontroller-class devices. Consequently, much of the recent literature 
relies heavily on aggressive algorithmic workarounds and specialized
software stacks. These methods often center on specialized
tiny training engines and Fully Quantized Training (FQT)—a
technique that maintains weights, gradients, and activations in
low-precision integer formats (e.g., INT8) throughout the backward
pass—or dynamic partial gradient updates tailored to allow
devices like the ARM Cortex-M to perform localized fine-tuning 
\cite{saha2023tinypack}.

However, redesigning or completely rewriting the AI software
stack carries significant overhead: it is time-consuming,
highly expensive, and prone to subtle implementation errors
that are notoriously difficult to detect when evaluating
probabilistic outputs. This thesis takes a complementary,
platform-centric approach. Recognizing that modern embedded
hardware continues to advance rapidly under Moore's Law, today's
"traditional" embedded platforms have evolved into remarkably
capable computing engines. Rather than re-engineering complex
software stacks from the ground up, this work turns the question
around to ask: What capabilities and performance limits can be
realized for on-device learning by fully exploiting standard,
existing software and hardware platforms?

To ground this question in a practical engineering context,
consider a scenario representative of modern industrial
product lifecycle. A commercial HVAC manufacturer launches
a next-generation building automation controller (codenamed
Gryphon). Built around an integrated System-on-Module (SOM)
architecture, the device features a high-resolution touch
interface and sophisticated deterministic algorithms to govern
large-scale heating and cooling systems. Shortly after hardware
development is finalized, the landscape shifts dramatically
with the explosive rise and rapid enterprise adoption of modern
artificial intelligence and machine learning technologies.

Faced with this unexpected surge, leadership asks
a fundamental strategic question:  
\textit{\textbf{Can this newly deployed Gryphon platform—specifically built around an NXP i.MX 8M Plus SOM—be leveraged to run AI models directly at the edge, or is an immediate, costly hardware redesign required to support these modern workloads?}}
An in-house design such as Gryphon is
very expensive and the design is expected to have a 20-30 year
lifetime before being redesigned. However, Gryphon was basically
only three years old when ChatGPT appeared. If customer expects
AI-enhanced features, the question is whether Gryphon can deliver
or does the company have to "bite the bullet" and design a new
"AI-ready" hardware platform for current and future products.

This thesis addresses that dilemma through a formal feasibility
study of the i.MX 8M Plus platform. Rather than modifying
hardware or rewriting underlying software execution engines,
we evaluate whether this existing embedded architecture can
satisfy strict completion and execution constraints across
inference, prediction, and in-situ learning. By testing model
execution across varied dataset scales—ranging from small
operational samples to multi-year historical telemetry—this
work establishes the empirical boundaries of performing AI compute
on modern industrial SOMs without requiring a hardware overhaul.

This thesis makes these primary contributions:  
\begin{itemize}
\item First, it evaluates the feasibility of deploying machine learning workloads 
  on an existing industrial embedded platform, the NXP i.MX 8M Plus-based 
  Gryphon controller, without redesigning the underlying hardware. 
\item Second, it examines the practical constraints of on-device inference and 
  in-situ training on a resource-constrained Linux-based embedded system, 
  including software integration, execution overhead, memory limitations, and dataset-dependent performance. 
\item Third, it provides an empirical comparison between the target 
  embedded platform and a conventional laptop environment to establish the 
  real-world performance boundaries and engineering trade-offs for AI-enabled 
  industrial control applications.
\item It also offers evidence-based guidance for industrial system designers
  considering whether existing SOM-based platforms can support modern AI 
  features within lifecycle, cost, and reliability constraints.
\end{itemize}

The rest of this thesis is organized as follows: Chapter 2 (Background) 
performs a deeper dive into the research and studies already 
performed in the Artificial Intelligence, IoT (Internet of Things), and 
embedded controls device spaces. The section describes the background of 
embedded control devices and attempts to give examples where small 
embedded devices can be used to run both inference and training with an 
edge system that is not running these algorithms on a separate device. 
The system in this case is running the models on the edge device platform.  
This chapter also shows examples of AI running on those devices, and some 
avenues this is performed on those devices with limited compute and memory 
capabilities.  

Chapter 3 (Design) describes the setup of the systems under test, and how these 
systems were configured and built from the perspective of executing the 
experiments.  The Gryphon controller was used, but while its a native Linux 
base platform, Python was not running or installed onto the system. 
The experimental code base was written in python.  How the system 
was modified to overcome this discrepancy, and other challenges to running 
are explained in this chapter. 

Chapter 4 (Results) goes into the results of all these experiments and analysis 
of those results. A target embedded Gryphon controller device was 
used to run the tests.  This was also compared to running the same 
software on a MacBook Pro (2019) system originally used for the 
graduate level Intro to ML course assignments. These comparisons 
are detailed in this section

Chapter 5 (Conclusion) summarizes all of these results and gives a final conclusion 
derived from the experiments and testing.  The conclusion answers the 
thesis question with a qualified answer.  Its important to note that while 
it may seem intuitive to generate an hypothesis of the outcome, the data 
in theses experiments and results within bounds is what drove the answer 
to the thesis question. The resulting evaluation provides a convincing 
argument for why this embedded device can/cannot be used for training and 
inference applications for large commercial comfort cooling applications.  

The Appendix contains reference code bases for all of the experiments 
and contains the algorithms as modified to run on both the target 
embedded system as well as the laptop machine. 
