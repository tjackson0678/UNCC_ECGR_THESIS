---
title: "Edge IOT AI and ML Feasibility on HVAC Controllers"
author:
  - "Terrence Jackson"
  - "Advisor: Dr. Ron Sass"
institute: "UNC Charlotte"
date: "Fall 2026"
aspectratio: 169
---

## Background

- BS in Computer Science from UMBC (Maryland Balt Co.)

- Currently Staff Systems Engineer @ Carrier Corporation

- 20+ years experience in Defense, Technology

- LM, Honeywell, Centene, Carrier, GA

- Software Development, and Systems Engineering experiences

- Pursuing MS Computer Engineering from UNCC starting 2024

## Consider: Imaginary Scenario

- **2023** a large HVAC company launches a "next generation" controller
  (Gryphon)
  - Embedded controllers for Commercial Water Cooled HVAC units
  - Modern interface with high resolution screen
  - Sophisticated algorithms for use in large commercial buildings
  - SOM (System-on-a-Module) integrated with a
    full-custom embedded system
    \pause

* **2024** ChatGPT introduced and AI explodes
  \pause

* **2026** naturally a fictional executive wants to be a part of AI boom
  and starts to ask ...
  \pause
  - can we do AI on the Gryphon controller?
  - noting, we are only 3 years into a 15-20 year lifetime!
  - the engineering question:
    \pause
    **_Are ML algorithms feasible on Gryphon?_**

## Outline

- Intro/Framing of the Question

- Background and Related Information

- Extensions to Infrastructure & Firmware

- Build Experiments and Analysis

- Ultimate Result and Conclusion

# Background

## Embedded Systems and AI / ML Overview

- Artificial Intelligence & Machine Learning
  - Usually require large memory allocations, parallel compute, and matrix-heavy operations for training and inference [@patel2026]
  - Examples include image classification on industrial cameras, speech recognition on smart devices, and predictive maintenance on sensor streams
  - These workloads benefit from GPUs/NPUs, high-bandwidth memory, and high clock rates

- Embedded Systems
  - Typically operate under constrained RAM, storage, thermal, and power budgets, and are designed for deterministic real-time control rather than large-scale model training [@pinto2019demystifying]
  - Examples include HVAC controllers, motor drives, industrial gateways, and sensor nodes
  - Many are limited to smaller form factors, lower power envelopes, and minimal operating systems or storage

- However - embedded systems are becoming more capable, and the real question is no longer whether AI is possible, but which model complexity and latency trade-offs are acceptable for the application.

# Infrastructure Extensions & Firmware

## Experiments & Infrastructure

- Gryphon controller running Ubuntu Linux
  - Quad-core ADLINK System on Module (SOM)
  - SMARC (Smart Mobility Architecture) form factor
  - Cellular M.2 modem slot

- i.MX 8M Plus-based controller design
  - Integrated NPU, VPU, and GPU
  - 4 GB of LPDDR memory
  - Yocto-based software build system

- Custom PCB with an integrated SOM
  - ARM Cortex-A53 and Cortex-M7 processors
  - Integrated Wi-Fi and Bluetooth

## ADLINK MODULE

::: columns
::: {.column width="50%"}

- NXP I.MX8M PLUS
- Integrated Microcontroller and EMMC
- Integrated Wi-Fi Modem
  :::

::: {.column width="50%"}
![ADLINK SOM](./figs/SOM.pdf){height=50%}
:::
:::

## Experiments & Infrastructure (cont'd)

- Processing processing potential
  - 2.3 TOPS Neural Processing Unit
  - Vivante. GC7900UL GPU

- Linux Ubuntu with Yocto Build Recipe
  - 16GB Flash Memory available
  - SD Card slot, Video decoding

- Customized build structure and AI ML libraries
  - Tensor Flow, APT, PIP Installers
  - SciPY, Numpy, Matplotlib for charts, graphs
  - Python Pandas for datatables

- Build Guide explains exact recipes uses, but others added
  - Sklearn, Seaborn for training models, datasets

# Build Experiments and Analysis

## Analysis and Setup

- Experimented with a number of intro to ML projects to test
  - Linear Regression with Gradient Descent
  - Computing Cost Functions using variable Theta and Cost functions

- Predictive Models using Training and Test data
  - Used to create more accurate predictions
  - Supervised learning modes to create predictions

- Classification Models with predictions and Accuracy scoring
  - Case where there is a discrete subset of outcomes
  - Different models allow for better accuracy

- 4 Projects in total

## Analysis and Experiments (cont'd)

- Datasets from Kaggle.com and Sklearn libraries
  - Energy Data - multiple features and sampled efficiencies
  - Steel Data - years worth of efficiencys for plant
  - Breast Cancer data - classification of outcomes
  - Housing data - prices and home feature data

- Used for training as well as predictions

- Runtimes varied across the machines used
  - 2s to 6-10 minutes on a laptop
  - 22s to 59min on target device
  - small/nominal dataset sizes for assignments

## AI & ML Assignments

![ML Assignment Results](./figs/timing_bar_chart.png){height=70%}

## Analyssis and Experiments (cont'd)

- Runtimes accros the machines tested:
  - Small, med, large datasets used in projects
  - Smaller sets ran quicker
  - larger sets (700 - 35K samples) ran slower

- Target machine seemed up to the challenge:
  - Larger took more time but eventually completed
  - Multiple Learning Models used for projects
    - Classification, linear, logistic regression models
  - Theta and interation values affected outcome times
    - most still completed with target machine working much slower
    - 6-10min vs 20-59min on target
    -

## Analysis and Experiments (cont'd)

- Additional Experiments - MicroGPT=
  - Out of the box experiment using MicroGPT
  - Python version of scaled down ChatGPT
  - No libraries or dependencies, just one text input file

- MicroGPT - 1 text file as input
  - Outputs predictions based on input
  - Use any input for training
  - Names, address, businesses

- Running results of MicroGPT
  - 1000 Steps / samples
  - Initially 3-4 minutes on Laptop
  - 1000 (default) - 24 minutes on target

## MicroGPT

![MicroGPT Results](./figs/microPlot.pdf){height=70%}

## Analysis and Experiments (cont'd)

- Additional tests w/MicroGPT
  - 15000 steps (learning inputs)
    - 6 hrs on target
  - 4 Layers of Neural Network Transformers
    - 50 Minutes on Target
  - 10000 steps and 4 depths of layers
    - 17 hours on target

- Other options include:
  - Width of network
  - Attenuation heads

- Tests w/options rapidly push target limits

# Conclusion

## Conclusion

- Positive case outcomes with limitations
  - Nominal testing of AI and ML
  - Successful Linear Regression runs
  - Classifications successful

- Complex cases of AI present limitations
  - Tuning of AI/ML Learning variables
  - Theta, iterations, sample size

- Hardware sufficient for what is needed at this time
  - NPU / GPU may also add capability
  - Tensor flow for GPU runs

[def]: ./figs/microPlot.pdf

<!--

  Thesis Defense Slides

  all in one command to make a quick PDF...

  pandoc -t beamer --slide-level 2 thesis_slides.md -o thesis_slides.pdf


# Intro
-->
