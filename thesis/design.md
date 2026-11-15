# System Design and Implementation

\label{sec:design}

In the introduction, we posed the question of what on-device learning
can be realized by fully exploiting standard, existing
software and hardware platforms. This chapter answers it by
construction. Rather than proposing a purpose-built learning
accelerator or a re-engineered software stack, it examines
a deployed building automation controller, designed before
contemporary machine learning workloads were a consideration,
and asks what architectural elements are necessary to host
both inference and training directly on the device. The
chapter proceeds in four steps. It first characterizes the
existing hardware platform, including the compute, memory, and
storage resources that bound what the device can support. It
then describes the existing software platform, identifying the
operating environment and the real-time control functions that
must continue to operate undisturbed. Third, it specifies the
additional system software, along with its dependencies, needed
to turn this general-purpose embedded platform into a viable host
for machine learning. Finally, it shows how representative AI
workloads can be integrated alongside the deterministic control
application.

The goal of this chapter is architectural understanding,
not product engineering. The system described here is a
working prototype that exposes the structure, dependencies,
and integration points that any in-situ training and inference
platform of this kind would share. It is not a hardened,
optimized, or production-ready deliverable. By documenting these
elements explicitly, the chapter establishes the platform baseline
against which the experimental results in subsequent chapters are
interpreted, and it provides a template that practitioners can
adapt to other embedded systems facing the same retrofit problem.

## Existing Hardware

The Gryphon controller is based on a ADLink IMX8M Plus system on Module
design. The baseboard is designed in house. This is a very
popular and widely successful 64-bit low power, high efficiency chip.
The microcontroller is a System-On-Module design with a Quad Cortex A53
ARM design CPU. MCU is shown in Figure 3.1 and described in Table 4.1.

The System on Module design mounts a SoC (System on Chip) onto a ready
to use PCB with RAM, flash storage, and power management onto a single
board. This board can be interchanged quickly and easily without
fully redesigning the entire PCB and interfaces to upgrade the system.

\begin{figure}
\centering
\includegraphics[width=0.9\textwidth]{figs/SOM.pdf}
\caption{\label{fig:som} ADLink System-On-Module }
\end{figure}

The controller has an sufficient amount of memory available including 16GB of
RAM and an SD card slot and Video decoding. The MCU hass 512KB of system
L2 cache as well as security features like ARM TrustZone support.

There is a GPU on the module, vision processing unit (VPU), and neural
processing unit (NPU) to support high resolution
graphics, video encoding and decoding to 1080p, and feature support.
Other hardware include WiFi and Ethernet onboard the
controller with a SMARC architecture. 

These items describe the base SOM control of the control board, but
there is also the application software and firmware that allow the
controller to process algorithms to properly maintain an operating
temperature on the Chiller cooling water.

## Existing System Software

In order to get started on the board to make modifications and
determine if this controller can run AI algorithms, there was a need
to connect and run the board support package and figure out what would
be missing when trying to run standard Python applications on the
controller.

First step was to run the build and get a running firmware for the
device. The build guide describes how to build an image for the I.MX
board using a Yocto Project build environment with an addition feature
of Mender included.

Yocto is an open-source collaboration toolset for building custom
Linux OS, specifically for embedded and IoT devices. This distribution
is a framework and different from just downloading and installing the
Linux operating system. The framework provides templates, tools, and
metadata to compile complete Linux systems specifically for custom
hardware. This caveat is important when thinking of building a
system designed for HVAC and not simply a standard user looking to
surf web pages.

With Yocto, kernel versions, software packages, drivers, and security
settings are included in the final image. There are layers and
recipes that tell the system where to get the source code and how to
compile specific software packages or board support packages. The
recipes and layers can be tuned and include certain features for
custom build.

Bitbake is the task execution and build engine used to handle
cross-compilation and software dependencies. The recipes and layers
provide an avenue for repeatability, hardware independence, and
precise version control for use in this commercial application of
software/firmware.

These builds also have a downside of long compilation times during
builds, which is definitely something that was experienced during this
design process. Sometimes hours and overnight runs were spent getting
the correct recipes and modifications to layers to get the controller
to properly build and run on the target device it was designed for.

With this project, the initial goal was to attempt to run a few nominal
AI and ML model scripts to verify that they are able to generate
inference and perform training on the current boards as designed.  
The Intro to ML and AI class at UNC Charlotte provided a great option
to run a few of the projects on the board to verify this capability.

These projects mostly run in Python on a Linux or WSL platform. This
would be good to test out on the system while verifying the running
time compared to a standard macOS MacBook Pro laptop running nominal
scripts. So the setup is to compare running times on this laptop with
a target machine as the host.

To build the linux kernel and compile to run on a target machine which
in this case is the embedded Gryphon controller, a build guide is used
to run through all of the recipes and layers. The image would be built
using the latest stable Ubuntu Linux. This is run on a VM, on the
Macbook to create and compile a build for the target platform, which
also happens to run Ubuntu at the core.

\autoref{tab:sw_components} shows the layers used in this release.

\begin{table}
\caption{\label{tab:sw_components} Yocto Recipes}
\small
\begin{tabular}{rp{0.67\linewidth}l}
\toprule
$N$ & {\bfseries Description} & {\bfseries Link} \\
\midrule

1. & {\bfseries Poky} — Basic Yocto Project items in Poky. & \href{https://git.yoctoproject.org}{poky}\\
2. & {\bfseries Meta-carrier} — BSP and machine configurations. & \href{https://github.com/Carrier-CESP/meta-carrier}{meta-carrier} \\
3. & {\bfseries Meta-packages} — Provides middleware packages. & \href{https://github.com/Carrier-CESP/meta-packages}{meta-packages}\\
4. & {\bfseries Meta-qt6} — Provides Qt 6.5.7 LTS. & \href{https://code.qt.io/yocto}{meta-qt6}\\
5. & {\bfseries Meta-arm} — Layer containing support for Arm products. & \href{https://git.yoctoproject.org}{meta-arm}\\
6. & {\bfseries Meta-freescale} — Provides support for the base and Arm reference boards. & \href{https://github.com/Freescale}{Freescale}\\
7. & {\bfseries Meta-freescale-3rdparty} — Provides support for 3rd-party boards. & \href{https://github.com/Freescale}{Freescale}\\
8. & {\bfseries Metafreescale-distro} — Additional items to aid in development and board capabilities. & \href{https://github.com/Freescale}{Freescale}\\
9. & {\bfseries Fsl-community} — BSP base files. & \href{https://github.com/Freescale}{Freescale}\\
10. & {\bfseries Meta-imx} — Yocto Project BSP layer for i.MX. & \href{https://github.com/nxp-imx}{nxp-imx}\\
11. & {\bfseries Meta-browser} — Provides several browsers. & \href{https://github.com/OSSystems}{O.S.Systems}\\
12. & {\bfseries Meta-security} — Security tools for Internet-connected devices. & \href{https://git.yoctoproject.org}{meta-security}\\
13. & {\bfseries Meta-virtualization} — Layer enabling virtualization support. & \href{https://git.yoctoproject.org}{meta-virtualization}\\
14. & {\bfseries Meta-nxp-demo-experience} — Launcher application for GoPoint for i.MX applications processors. & \href{https://github.com/nxp-imx-support}{NXP i.MX Support}\\
15. & {\bfseries Meta-openembedded} — Collection of layers for the OE-core universe. & \href{https://github.com/openembedded}{openembedded}\\
16. & {\bfseries Meta-clang} — Clang C/C++ cross compiler and runtime for OpenEmbedded/Yocto. & \href{https://github.com/kraj}{kraj}\\
17. & {\bfseries Meta-carrier-distro} — Provides distro configurations. & \href{https://github.com/Carrier-CESP/meta-carrier-distro}{meta-carrier-distro}\\
18. & {\bfseries Carrier-manifests} — Manifest file to fetch meta-layers. & \href{https://github.com/Carrier-CESP/carrier-manifests}{carrier-manifests}\\
19. & {\bfseries Meta-timesys-security} — Provides Vigilent tools for monitoring BSP vulnerabilities. & \href{https://github.com/Carrier-CESP/meta-timesys-security}{meta-timesys-security}\\
20. & {\bfseries Mender} — Mender-related support. & \href{https://github.com/mender}{Mender}\\
    \bottomrule
    \end{tabular}
    \end{table}

`#` - Not included as part of the RFS or image.

The final four layers in the previous listing are specific to the
Carrier corporation and would need privileged access to run. The
firmware modules are proprietary information while the other recipes
are standard and available through the Yocto project site or others
provided in the links.

The build could possibly take several hours to compile and run. This
could cause debugging recipes difficult, where if there were a problem
with the build, it may not show up for a couple of hours during the
build.

The following is the minimum recommended environment for building
an image to run on the target device. For the MacOS VM used, these
parameters were configured, and a much smaller amount of hard disk
was allocated (125GB) and the build ran successfully. Initially
built with 75 GB and the build failed.

- Minimum hard disk size of 500 GB

- Minimum RAM of 16 GB

- Ubuntu 22.04

- For one build minimum of 160GB is required.

The next step requires running the following command on the build
machine to install essential Yocto Project Host packages necessary
for the install. These are not recipes, but only needed to install
on the build machine in order to get the compilation right. The
recipes are what determines what is able to run on the target image.

\begin{figure}
\caption{\label{fig:aptInstall} Install needed Files/Dependencies}
\begin{lstlisting}
$ sudo apt install gawk wget git diffstat unzip texinfo gcc \
$ build-essential chrpath socat cpio python3 python3-pip \
$ python3-pexpect xz-utils debianutils iputils-ping python3-git \
$ python3-jinja2 libegl1-mesa libsdl1.2-dev python3-subunit \
$ mesa-common-dev zstd liblz4-tool file locales -y
\end{lstlisting}
\end{figure}

The following command sets up the locale and then the Repo utility.

\begin{lstlisting}
$ sudo locale-gen en_US.UTF-8
$ export LC_ALL="en_US.UTF-8"
$ export LC_CTYPE="en_US.UTF-8"
\end{lstlisting}

The repo is a tool built on top of Git that makes it easier to manage
projects containing multiple repositories, which do not need to be
on the same server. Repo complements very well the layered nature of
the Yocto Project, making it easier for users to add their own layers
to the BSP. This controller setup makes use of the repo utility.

To install the “repo” utility, perform these steps. This step can be
ignored if repo utility already present.

The next step creates a bin folder in the home directory. Following
this step the `~/bin` directory and repo are added to the local PATH.

\begin{lstlisting}
$ mkdir ~/bin
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
\end{lstlisting}

\begin{lstlisting}
$ export PATH=/home/YOURUSERNAME/bin:$PATH
$ source .bashrc
\end{lstlisting}

The profile file of the user is edited, and the below snippet added at the
end of the file

\begin{lstlisting}
if [ -d “$HOME”/bin ]; then 2 PATH=$PATH:$HOME/bin 3 fi
\end{lstlisting}

The next steps including preparing to build the image with QT and the
proper license file. This was a critical step in the building of the
image for the project. This license is obtained from the software
IT team or can be purchased directly from the developer.

Qt is a cross-platform software dev framework used for creating the UI
and deploy it to the operating system. While the UI is not a part of
this testing and experiments, this was a critical step in getting the
software image to build correctly. There was a failure after the build
when the license file was not present and installed in the build server.

Perform the following steps on the build machine for Qt 6 compilation.

1. Get the .netrc & .qt-license files

2. Copy the files into ~/ (home directory)

The next steps include generating an SSH key. This key is used for
logging into the target image once created and built. Also GitHub
user settings are cloned from the repository to the target device.

1. Open a terminal.

2. Generate SSH keypair using the command:

3. ssh-keygen -t rsa -b 4096 -C your_email@example.com

4. Confirm the location to save the key (default is ~/.ssh/id_rsa)

5. **Note:** Selecting the same location will overwrite any existing key pair.

6. Ignore and proceed without an optional passphrase to protect the private key.

7. After completing the prompts, a default keypair will be generated. \
   a. Private key: Stored in ~/.ssh/id_rsa \
   b. Public key: Stored in ~/.ssh/id_rsa.pub \
   c. Open the id_rsa.pub and copy the key for the next step.

To clone from the required repositories from Carrier-CESP GitHub
organization, the ssh public key of the host (development machine)
should be added under the section “SSH and GPG keys” in the user-settings.
This is a company proprietary step needed to have access to the repo
in order to build company specific firmware. The Git repo in this case
is behind a company firewall and not publicly accessible.

After adding the SSH key, also configure SSO for the key.

Firstly, make sure that Git is set up properly with the commands below:

\begin{lstlisting}
$ git config --global user.name "Your Name"
$ git config --global user.email "Your Email"
$ git config --list
\end{lstlisting}

Run the following commands to clone the repositories.

\begin{figure}
\caption{\label{fig:repoSync} Initialize Repo}
\begin{lstlisting}
$ mkdir cesp-yocto-bsp
$ cd cesp-yocto-bsp
$ # repo init -u git@github.com:Carrier-CESP/carrier-manifests -b \
$ scarthgap -m "cesp3.1/cesp-3.1-c.xml"
$ repo sync
\end{lstlisting}
\end{figure}

**_Note:_** Modify the git URL to point to the BU source control
repository

With the above commands, the yocto layers are cloned into the
sources folder.

```
Note:

Update Manifest remote URL

 - After repo init update fetch url of carrier remote in
   cesp-3.1-c.xml(.repo/manifests/cesp-3.1)

<remote name="carrier" fetch="ssh://git@ssh.dev.azure.com/v3/carriercorp/Gryphon"/>
```

So for a number of these steps, specific access to repos and files
needed to be obtained from the company and software/firmware teams
after explaining the reasoning behind gathering these files. The
documentation here is specified to illustrate just what is needed
to build this specific image. If certain proprietary recipes are
not needed for a nominal build, these steps can be replaced.

\begin{lstlisting}
# MACHINE=imx8mp-ht-ecs DISTRO=chvac source sources \
   /meta-carrier-distro/conf/carrierconf/carrier-imx-setup-release.sh -b build
\end{lstlisting}

This is the command that actually creates the build folder. The
following step will update the platform Git URI to add the certain
information to the local.conf file. This step contains a GIT repo
that is not publicly accessible. These files are proprietary, but
any other repo will work in this step.

```
Note:

   Update Platform Git URI

   - After build setup add variable PLATFORM_GIT_URI in local.conf file
     (build/conf/local.conf).

     PLATFORM_GIT_URI = git://git@ssh.dev.azure.com/v3/carriercorp/Gryphon

or

Run the following command

# echo "PLATFORM_GIT_URI =
# \"git://git@ssh.dev.azure.com/v3/carriercorp/Gryphon\"" >> conf/local.conf
```

The next step is to run the bitbake command and start the compilation
process. There is a test image and production image. The test image
contains a subset of files instead of the production image. For this
testing, the production image was created.

```
1 Bitbake command for Prod Image
2 # bitbake cesp-image-prod
3
4 Bitbake command for Test Image
5 # bitbake cesp-image-test
```

This will compile and build the production image of the controller and
algorithms. Once built additional changes were needed in order to run
python and the respective dependencies needed for running AI and ML
algorithms.

The bitbake command used to build an image is bitbake <image name>.
Additional parameters can be used for specific activities described
below. Bitbake provides various useful options for developing as a
single component. While these options were not used for the purposes
of this experiment, it may be helpful in some other cases where and
this can be used for reference.

The following list provides some BitBake options:

1. `-c fetch` — Fetches if the download state is not marked as done.
2. `-c cleanall` — Cleans the entire component build directory; all changes are lost.
3. `-c deploy` — Deploys an image or component to the root filesystem.
4. `-k` — Continues building components even if a build break occurs.
5. `-c compile -f` — Forces a recompile after the image is deployed.
6. `-g` — Lists a dependency tree for an image or component.
7. `-DDD` — Turns on debug output three levels deep.
8. `-s` — Shows the current and preferred versions of all recipes.

Post Bitbake, the images are created and placed in a temporary folder,
tmp/deploy/sdk on the build machine. Once successfully built, the image
can be placed on an SD Card and used to run the target device.

During this process there was an initial back and forth with the builds
failing for a number of reasons. Once the image is created and saved
there are two options for running the image. The settings on the target
can be changed with a boot dip switches on the control board.

The board can boot off of the SD Card device image, or the image can be
flashed and saved to the EMMC resident on the board. For this testing
setup, the SD Card boot sequence was used.

## Additional System Software for AI Applications

For the purposes of this experimentation, the idea was to determine if
simple programs for AI and ML from a graduate level Intro to
AI and ML class at UNCC could run on the target machine as configured.
This functionality was already tested on a macOS laptop for the purposes
of passing the class, but it would go a long way to prove applicability
if these same learning algorithms could be used on the target device.

Initially, to gain access to the device, login and password
information shared from the software team. Once a connection to the unit
was made, it was determined that Python was not natively running on the
target machine. Also, all of the dependencies needed were also not running.

There was no Wi-Fi and/or cellular connection up, even though the unit
had the hardware to provide these interfaces. The scripts to run the
software were all written in Python. Also, there were a number of
additional libraries needed to run the scripts.

Pandas is an open-source Python library used for data manipulation and
analysis. In the case of the initial AI and ML algorithm assignments,
this library was needed to create dataframes for holding data. Also,
Numpy, Matplotlib, OS, SkLearn, and Seaborn are all libraries from
Python needed in order to run the initial algorithm assignments.

These all running together make up the backbone of the Python data
science and machine learning ecosystem. NumPy handles the numerical
data, Matplotlib makes plots and visuals possible, and Scikit-Learn
uses these other tools to build predictive models. There are a number
of other components within SkLearn used for running the algorithms.

Here is a sample code from one of the tests to run a basic Linear
Regression model to train the model on a nominal dataset downloaded from
kaggle.com. The code then uses Gradient Descent to tune the model for
better predictions and then Plots the results using the Matplotlib
library.

In order to get this code to run, those previous discussed libraries
were needed to be on the target system. In order to get those onto
the target, Yocto has recipes that can be added to the build. So the
recipe '.bb' files were modified to add python and additional
libraries for the purposes of getting the target device to have these
capabilities.

Once the recipes are added and the layers modified, the build is run
again. Any failures that were encountered were corrected in the .bb files
and also the local.conf config files in order to properly build the
target system.

In some cases, and for the purposes of timely running of these
experiments, a network connection was created and certain dependencies
were pulled directly from the target device. Examples of this were
APT and PIP package management libraries used in Linux environments.

Apt manages system-wide software across the entire Linux operating
system. Pip specifically manages libraries and modules inside Python
environments. Neither of these libraries, which has a number of
additional dependencies were initially a part of the build.

Apt specifically can be used in Ubuntu Linux to download and install
Python and any other software to run within Linux. PIP command within
Python can be used to install additional dependencies. Python was
installed using a Yocto recipe modification, as well as SciPy for
running algorithms prior to the build. These are common recipes and
modifications for these were complex, but achieved good results.

Other specific python dependencies required a network connection once
the target was built. Then APT, and PIP could be installed on the
target device. Once installed, Seaborn and other dependencies could
be downloaded and all of the project Python code could be tested on
the target.

## Experiments and information

For the experiments, Assignment 1 is a small gradient-descent exercise
for simple linear regression on the Steel dataset. It reads a CSV,
scales the feature column using scikit-learn’s StandardScaler, and then
defines a one-feature model that predicts a target value from a slope/
intercept pair. The code computes a cost function based on prediction
error, updates the parameters iteratively, and prints the learned theta
values for different variables such as X1, X2, and X3.

Assignments 1, 2 and 3 also includes plotting functions that visualize
how the training cost changes over iterations under different learning
rates. The scripts compare convergence behavior across several input
features and displays those curves as graphs, giving a practical
illustration of how learning rate and feature scaling affect optimization.

Assignment 3 is a classification comparison project using the breast
cancer dataset from scikit-learn. It splits the data into training and
test sets, standardizes the inputs, and trains several models: logistic
regression, Gaussian Naive Bayes, and a linear SVM. For each model, it
makes predictions on the test data, builds a confusion matrix, and
visualizes it with a heatmap so the distribution of true vs. predicted
classes is easy to inspect.

The second half of Assignment3 script adds PCA-based dimensionality
reduction and then retrains a logistic regression model on the reduced
data to compare performance. It prints accuracy, precision, and recall
for each model and comments on which approach performs best. This file
is an end-to-end machine-learning experiment designed to compare
supervised classifiers and evaluate them on a real dataset.

The final project also is similarly a summary of all of the experiments.
The MicroGPT script implements a compact, self-contained GPT-like
language model in pure Python, without external deep-learning libraries.
It loads a text corpus of names, tokenizes characters into integer IDs,
and builds a tiny transformer-inspired architecture with embeddings,
positional embeddings, multi-head attention, residual connections, RMS
normalization, and a feedforward MLP. The code defines a custom autograd-
like Value class so the model can compute gradients through the entire
computation graph and update its parameters.

The script trains the model with a simplified Adam optimizer over many
steps, minimizing a next-character prediction loss on the dataset. After
training, it generates new names by sampling from the model’s output
probabilities. This file is primarily a “from scratch” implementation
of a small GPT, meant to show how the core mechanics of embeddings,
attention, and training work without relying on PyTorch or TensorFlow.

These are tests used to run a design a system on the target iMX8M Plus
device. Running these scripts and setup on the edge target platform
using a number of different sized datasets shows the limitations and
capabilities of AI and ML on the target device.
