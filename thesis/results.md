# Results \& Analysis

## Initial Experiments Results 

Initial experiments focused on comparing test runs of a series of code bases
running on both a nominal Macbook Pro laptop (2019 MacOS) and the target 
ADLink i.MX 8M Plus System on Module (SOM) embedded device. Both devices 
are referenced in Table \ref{tab:hardware-comparison}.  The code bases were 
derived from UNC Charlotte Intro to ML graduate course assignments including
the Final project, and were designed to evaluate basic model behavior on 
representative system workloads. 

## Hardware Specification Summary 

Table \ref{tab:hardware-comparison} summarizes the hardware specifications of
the embedded platform and the laptop used as the development and performance
comparison system. 

\begin{table}[htbp]
\centering
\small
\caption{\label{tab:hardware-comparison} Hardware - ADLINK i.MX8M Plus and 2019 MacBook Pro}
\begin{tabular}{p{0.16\textwidth}p{0.35\textwidth}p{0.35\textwidth}}
\toprule
{\bfseries } & {\bfseries ADLINK i.MX8M Plus} & {\bfseries 2019 MacBook Pro} \\
\midrule
{\bfseries Processor} &  Arm Cortex-A53 1.8 GHz 4-core, 800 Mhz Cortex-M7 real-time core & Quad-core Intel Core i5, 1.4 GHz base  \\
\\
{\bfseries Memory} & 4 GB LPDDR4 & 16 GB 2133 MHz LPDDR3 \\
\\
{\bfseries Graphics and AI} & Integrated GPU, VPU, image signal processor, and NPU & Intel Iris Plus Graphics 645; no dedicated NPU specified \\
\\
{\bfseries Storage} & 32 GB eMMC & 128 GB SSD \\
\bottomrule
\end{tabular}

\vspace{0.5em}
\begin{minipage}{0.9\textwidth}
\footnotesize
Sources: ADLINK LEC-IMX8MP specs \citep{adlinkLECIMX8MP} and Apple MacBook Pro spes \citep{appleMacBookPro2019}.
\end{minipage}
\end{table}

The experiment initial results tested on the specified laptop and target 
platform display the training and inference run timing for each device, and 
how these differ between the devices. The code base projects covered linear 
regression with gradient descent, cost-function analysis, and the role of 
parameters such as theta values and iteration counts in controlling 
convergence and model quality.

## Initial Datasets and Test Results

The intial experimanets used nominal sized datasets (~750 samples, 10-15 
features), and the results from those experiments are detailed in 
Figure \ref{fig:init}. These intial datasets were unmodified from the 
original assigments used in the classes. The tests results formed a baseline 
comparing nominal test run timing between the laptop and target embedded 
controller device. 

The timing of those runs were inconsequential when only viewing them from 
the original test runs on the laptop.  The longest time recored running for 
less than 4s.  These times outside of comparisons on an embedded controller 
are barely noticeable, and under normal circumstances, (i.e. during an Intro 
to ML class) would not prove significant.  

Running theses same tests on an embedded control device showed significant 
increases to running times for some experiments. While the relative times 
are much larger on the embedded controller, overall the timing being less 
than 30 secs in the worst case per run would not preclude the embedded 
target system from being a viable solution. 

\begin{figure}
\centering
\includegraphics[width=0.95\textwidth]{figs/InitTimingChart.pdf}
\caption{\label{fig:InitTimingChart} Initial Timing Chart}
\label{fig:init}
\end{figure}

The initial assignment opens with a nominal dataset, and selects a learning 
rate as well as initial theta values. Theta represents the models parameters 
(weights and bias), while the learning rate controls how aggressively the
parameters are updated during optimization (gradient descent).  Each feature 
was given a weight and therefore more importance during learning. The values
were then interatively adjusted so the cost function is minimized.  The 
datasets were also normalized to allow features to share the same scale during
training.  

The second assignments relied heavily on supervised learning and predictive
modeling, using labeled training data to establish relationships between
inputs and outputs before evaluating the model on held-out test data. This
process was important for comparing regression and classification approaches
under different problem types. Additional feature were used to make predic-
tions using the housing data.  Again the data was normalized so as not to 
allow large values (house prices) to dominate results over smaller values 
the datasets (bedrooms, bathrooms). 
 
Regression models were used when the goal was to estimate real-valued outputs
such as cost, energy, or housing price, while classification models were
applied when the system had to choose among discrete outcomes. Across the four
projects that were completed, the resulting accuracy metrics and prediction
behavior showed that different algorithms and parameter choices produced
different trade-offs between performance, training time, and final output
quality.

In each case, the goal was not only to verify the learning algorithm
could complete successfully, but also to understand how runtime and prediction
quality were affected by dataset size, model selection, and training
configuration. These exercises provided a useful baseline for evaluating
whether the target hardware can sufficiently support more realistic AI and ML
tasks under practical constraints.  

Minimal modifications were made to the initial assignments to disable the 
printout of the plots and graphs generated in the code. Also, the datasets for 
the First assignment which tested Linear Regression and Gradient 
Descent contatained an Energy dataset sourced from [kaggle.com](www.kaggle.com)  

The dataset for the second exercise which tested Linear Regression, but 
with variable inputs for thea and cost function inputs.  The housing 
dataset was provided online, but also was nominal in sized with about 
540 samples with about 12 attributes. 

The third exercise split a dataseet from sklearn dataset into both training 
and test data.  The Breast cancer dataset used 80% of the samples for 
training and the other 20% for testing the results.  Logistic regression 
was used to train the model. 

Although the runtime differences from the MacOS laptop were significant 
between the laptop and the embedded device, the overall times for running 
the algorithms was not incredibly long.  22s in the worst case for 
Exercise 1, but all of the algorithtms ran in at least that amount of 
time for all three exercises and also the Final Project.  

The dataset selection for the build experiments reflected a realistic mix of
small and medium-scale machine learning problems. Data sources included
kaggle.com and scikit-learn repositories, with examples such as energy
consumption records, steel production data, breast cancer diagnosis data, and
housing price features.

## Larger Dataset Experiments 

For the second round of experiments, larger datasets were used to attempt 
to stress the target and laptop systems additionally. The worst case running 
item from the initial round of experimnents was chosen to run the tests. 
Testing with linear regresstion and gradient descent was used but in this 
case larger datasets were used. 

For the midrange size of the testing, a dataset from kaggle.com was used and 
the dataset from the previous sample was extrapolated to double the size of  
the initial dataset. The larger dataset was approximately containing 1500 
samples and the same/similar number of features (10-15).  For this experiment
the data was run using normalized data to be able to run through the gradient
descent algorithm. 

The results of the testing with midrange sizes of datasets for both the 
laptop and target device are contained and displayed in Figure \ref{fig:A1LinearRegDatasets}
The results show approximately 50 percent increase in the running time on both
the target device and the laptop run times. For the laptop machine this 
increase woudld be no more than 1 sec inccrease.  For the target device this 
results in about 10 additional seconds of running time. 

\begin{figure}
\centering
\includegraphics[width=0.9\textwidth]{figs/A1LinearRegDatasets.pdf}
\caption{\label{fig:A1LinearRegDatasets} Assignment1 - Small , Med, Large Datasets}
\end{figure}

For the larger dataset sizes, the steel data was derived from the Kaggle.com
site.  \cite{sathishkumar2020steeldata} This dataset contains information on 
electricity consumption for the steel industry on a 4 times daily basis. The 
information on energy consumption of the industry is stored on the website 
of the Korea Electric Power Corporation.

The dataset contains about 35K samples and about 12 featers of the data 
including date, usage, current, day of the week, and load type.  This 
information is used to determine a good training set for when usage is 
high for the times of the year, and to predict when the usage may be low. 
 
These datasets enabled evaluation across both continuous and classification-
oriented tasks, while also exposing how training time increased with larger
feature sets and sample counts. In all cases, the model performance was
strongly tied to the size of the training data, the complexity of the learned
relationships, and the number of iterations used during optimization. The
experiments therefore confirmed that while the target platform could execute
these workloads, the computational burden increased significantly as the
problem size and model complexity increased.

Timing for the experiments ranged from approximately 2 seconds to 10 minutes 
depending on the assignment and dataset size. On the target device, the same 
workloads took substantially longer, with observed runtimes spanning roughly 
22 seconds to nearly 59 minutes.

These differences were expected, given the target hardware's lower performance
characteristics relative to the laptop, but they also demonstrated that the
device was capable of completing the workloads successfully, even when
execution was markedly slower. This result is particularly relevant for
embedded AI applications, where feasibility is determined not only by whether
a model can run, but also by whether it can do so within acceptable latency
and system constraints for the intended task.

## MicroGPT Tests and Results 

\begin{figure}
\centering
\includegraphics[width=0.9\textwidth]{figs/microPlotHours.pdf}
\caption{\label{fig:microPlotHours} MicroGPT Plot Hours }
\end{figure}

A second phase of testing used MicroGPT, a minimal Python implementation of a
lightweight generative model, to evaluate whether a more AI-intensive workload
could run on the embedded controller without a large software stack. The
system required only a text input file, and it was capable of using that data
to train and predict outputs based on a limited set of learned patterns. In
a baseline run with 1,000 training samples, the laptop completed the workload
in roughly 3 to 4 minutes, while the target hardware required about 24 minutes.

The MicroGPT repo was designed to simulate a ChatGPT generative learning 
model but written totally in python without any dependencies.  While the 
nominal run using MicroGPT was performed in 24 minutes, which was very promising 
for the target device changing some of the parameters and input variables for 
the project caused significant changes to the runtime causing the project to 
challenge the target devices computing solution times as displayed in 
Figure \ref{fig:microPlotHours2} 

\begin{figure}
\centering
\includegraphics[width=0.9\textwidth]{figs/microPlotHours2.pdf}
\caption{\label{fig:microPlotHours2} MicroGPT Plot Modified }
\end{figure}

As the learning configuration was increased, the runtime grew rapidly: 15,000
steps required approximately 6 hours on the target device, while four-layer
network contigurations and larger model depths increased the cost further,
reaching around 17 hours for some runs. These results indicate that the embedded
platform is viable for modest AI workloads, but that more demanding models and
deeper configurations push the system toward operational limits. In practice,
the feasibility of embedded AI depends on balancing model complexity, dataset
size, and acceptable latency, which the experiments were able to characterize
quantitatively.

More tuning could also be performed in future tests to add addiitonal 
changes to input variable in the MicroGPT function to test the number of 
attention heads, which were defaulted to the value of 4, along with the 
derived dimension of each head. This value is derived from the width of 
the neural network divided by the number of heads.  These added tests  
could push the target device even further.  The first test running for 
close to 18 hours on the dataset was enough to show that training and inference
is possible on this target device, but additional measures may be best run 
and tested on a more capable device.  


\label{sec:results}
