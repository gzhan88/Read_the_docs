.. OFDM_Receiver documentation master file, created by
   sphinx-quickstart on Sat Mar 23 13:02:50 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Project: Matrix Multiplication on Intel DevCloud Using DPC++
===============================================================

1) Introduction
---------------
This project provides an introduction to hardware acceleration using Intel `DevCloud <https://devcloud.intel.com/>`_. DevCloud provides access to Intel `oneAPI <https://www.oneapi.com/>`_ - a set of hardware acceleration development tools. We focus on the Data Parallel C++ (DPC++) programming methodology that is a core part of oneAPI. DevCloud provides access to different Intel hardware platforms including multicore CPUs, GPUs, and FPGAs. DPC++ aims to provide a single source programming methodology that covers these different hardware accelerators. 

2) Project Goal
---------------

We use matrix multiplication as an example as it is relatively simple, yet fundamentally important computation used in a wide variety of applications. As such, it is very well-studied in the hardware acceleration literature with a substantial number of technical reports and research papers. The project develops a block matrix multiplication architecture and discusses some common methods to optimize it. This enables a design space exploration process to determine the best architecture.

Before we begin, please complete :doc:`Lab: DPC++ on Intel DevCloud<devcloud>`. We will use this code as your baseline architecture.

3) Tasks and Questions
----------------------

Optimize Load Transfers
##########################

As discussed in the :doc:`lab<devcloud>`, the load store unit (LSU) in the baseline implementation requires hundreds of cycles and is the major bottleneck in the *c_calc* kernel. DPC++ uses a Burst/Coalesced LSU by default. Burst/Coalesced buffers contiguous memory requests until it reaches the maximum burst size. Change the LSU type to lower the latency for the load operations.

* Modify the **LD** operation to a different LSU style to achieve a lower latency. This should bring the *c_calc* latency to less than 25 cycles.

* Question 1: Describe your modification and discuss why it achieves a lower latency.

References:  

* `oneAPI Samples <https://github.com/oneapi-src/oneAPI-samples>`_

* `oneAPI FPGA Optimization Guide <https://software.intel.com/content/www/us/en/develop/documentation/oneapi-fpga-optimization-guide/top.html>`_

Loop Unrolling
#################

Loop unrolling is a common optimization to expose parallelism across iterations of a loop.

* Perform loop unrolling using **#pragma unroll**. Change the unroll factor by 2, 4, and 8.

* Question 2: What are the effects and general trends of performing unrolling using the pragma? Are the results as expected?

Manually unroll the loop by replicating the reductions into separate variables. 

* Perform loop unrolling manually. Change the unroll factor by 2, 4, and 8.

* Question 3: What are the effects and general trends of performing manual unrolling? Are the results as expected?

References: 

* The *Schedule Viewer* typically provides good insight about how DPC++ synthesizes the code.

* `oneAPI FPGA Optimization Guide <https://software.intel.com/content/www/us/en/develop/documentation/oneapi-fpga-optimization-guide/top.html>`_

* `dpcpp FPGA loop unroll example <https://github.com/oneapi-src/oneAPI-samples/tree/master/DirectProgramming/DPC++FPGA/Tutorials/Features/loop_unroll>`_

4) Block Matrix Multiplication (BMM)
------------------------------------

Block matrix multiplication is a common way to expose parallelism by loading and operating on blocks of the A and B matrices. Here is a BMM implementation in DPC++ `BMM_DPCPP.zip <https://bitbucket.org/akhodamoradiUCSD/237c_data_files/downloads/BMM_DPCPP.zip>`_. It is based on this `OpenCL implementation <https://www.intel.com/content/www/us/en/programmable/support/support-resources/design-examples/design-software/opencl/matrix-multiplication.html>`_, which provides good background on blocking and how the design leverages it for parallel execution. The implementation has the ability to change the block size and unrolling factor. We call these "knobs" since they can be changed to "tune" the dessign to the problem at hand.

Your goal is to change these *knobs* and observe their effects. After a few steps, you should see trends for each knob. You should use the results from previous steps to make *better* adjustments.

You also have to show the result of each step in a throughput vs. hardware utilization plot. Data points in that plot with the maximum throughput and minimum resource utilization are your plateau points (red dots in the following example).

.. image :: https://i.imgur.com/l9a1mRh.png

Requirements
###############

1. **Knobs**: You should define a set of variables (**knobs**) to change your optimizations for monitoring their effects on your design's performance and hardware utilization. You should use the following knobs:

	* Block Size

	* Matrix size (we use square matrices)

	* Unrolling factor for the unroll pragma

	* Unrolling factor for the manual unrolling

References: Spector is a good example to start with DSE; sample codes are available `here <https://github.com/KastnerRG/spector/tree/master/mm>`_.

Bonus
########

The `OpenCL implementation <https://www.intel.com/content/www/us/en/programmable/support/support-resources/design-examples/design-software/opencl/matrix-multiplication.html>`_ is simpler than the matrix multiply implementation used in Spector. As a bonus, you can implement the matrix multiply implementation used in Spector, in DPC++. A functionally correct code is enough for this section.

5) Submission Procedure
-----------------------

You must also submit your code (and only your code, not other files). Your code should have everything in it so that we can synthesize it directly. We must be able to only import your source file and directly synthesize it. You can assume that we have correctly set up the design environment. 

You must follow the file structure below. We use automated scripts to pull your data, so **DOUBLE CHECK** your file/folder names to make sure it corresponds to the instructions.

Your repo must contain a folder named "matrix_multiplication" at the top-level. This folder must be organized as follows (similar to previous projects):

**Contents:**

* **Report.pdf**

* Folder **mm_optimized1**

  - Source code (*.cpp, *.hpp) and reports (screenshots).
  
* Folder **mm_optimized2**

  - Source code (*.cpp, *.hpp) and reports (screenshots).
  
* Folder **mm_optimized3**

  - Source code (*.cpp, *.hpp) and reports (screenshots).


**Report:** For this project, you must submit a report that answers the questions on this page. You may add figures, diagrams, tables, or charts to describe your architectures with a sufficient explanation of how they were achieved and what they demonstrate.

6) Grading Rubric
-----------------

**100 points:** Your grade will be determined by your answers to the questions. Your answers should be well written and clearly delineated (for example: by copying the questions into the report before answering them, or placing each question under a separate subheading). Additional points (up to 20) will be subtracted for poor formatting and/or answers that are hard to understand. Examples of issues include any spelling errors, multiple/egregious grammar errors, poor presentation of results, lack of written comparison of the results, etc. Report throughput and resource usage for each design you discuss in your report, and include the files for these designs in your submission. We encourage the use of tables for stating results and the changes that produced them, and figures to draw comparisons between different designs. A well-written report is informative but not overly verbose. You will be deducted points if you do not follow the instructions on directory naming and file structure.

