# CEPARCO-Integrating-Project
Group 4 CEPARCO

Source Code = https://colab.research.google.com/drive/1YVG0DSg6NhhWLuIsCbS56z15khkSuyc5?usp=sharing
Demo Video Recording = https://drive.google.com/drive/folders/1S41WUmnvUNkFyUV93NDbA9KbjSM86qc7?usp=sharing
Test Cases = https://docs.google.com/document/d/1mAK8bgEqX4NlCTVWy7JyZEwDsjBSkpTJyHgUoo-3OHE/edit

## Parallel Algorithms

1. Parallelization in preprocess_BM_kernel:
The preprocess_BM_kernel function is also a CUDA kernel, which runs in parallel on multiple GPU threads.
The kernel is launched with multiple blocks of threads (the number of blocks is determined by blocksPerGridBM).
Each thread is responsible for updating the Bad Character Shift table for a specific character in the pattern.
The if condition inside the kernel allows only the threads corresponding to characters in the pattern to update the table, while others do nothing. The __syncthreads() function is used to synchronize all threads in the block before proceeding to ensure consistency.

2. Parallelization in main:
The main function launches the CUDA kernels in parallel.
Each kernel runs concurrently on the GPU with multiple threads executing different parts of the computation.

3. Parallelization in Memory Allocation and Data Transfer:
The memory allocation for d_string, d_pattern, d_kmp_table, and d_bm_table is performed using cudaMalloc function.
The memory allocation is a CUDA operation that runs on the GPU in parallel.
It allocates memory on the GPU for the specified variables.

4. Parallelization in Data Transfer:
The cudaMemcpy function is used to transfer data from the host (CPU) to the device (GPU).
Two calls to cudaMemcpy are made to copy sample_string and sample_pattern from the CPU to d_string and d_pattern on the GPU.
Data transfer between the host and the device is a parallel operation and is handled by the GPU's memory controller.

5. Parallelization in Memory Advise:
The cudaMemAdvise function is used to provide advice about the usage pattern of the allocated memory on the GPU.
The advice given here is to set the memory region (both d_string and d_pattern) as "read-mostly," indicating that the memory is likely to be read frequently but written to infrequently.
The memory advice is a GPU operation that is executed in parallel.

6. Parallelization in Memory Prefetch:
The cudaMemPrefetchAsync function is used to prefetch the data to the GPU asynchronously.
The function requests that the specified data (both d_string and d_pattern) be brought to the GPU memory.
The prefetch operation is a parallel task handled by the GPU's memory controller, allowing data to be brought to the GPU before it is actually used, thus reducing data transfer latency.

## Comparison of Execution Time (Parallel Vs. Sequential)
String = 100K
![image](https://github.com/DDGC7/CEPARCO-Integrating-Project/assets/99391788/28397849-d0e0-4c7f-a00e-6a807d03a187)

String = 50K
![image](https://github.com/DDGC7/CEPARCO-Integrating-Project/assets/99391788/4cc697dc-317c-449e-9676-6bb257989ea5)


String = 10K
![image](https://github.com/DDGC7/CEPARCO-Integrating-Project/assets/99391788/3fcc1a20-e636-44ce-a7af-a19addd2d958)

## Analysis and Results Discussion
Based on the output of our project, we weren't able to successfully parallelize the search pattern kernel. This led to the execution time of our CUDA implementation to be much slower compared to the C (sequential) implementation. In addition to that, the parallelization of the KMP pre-processing table wasn't successful as well, as the execution times were slower compared to the C implementation. For string lengths 100K and 50K, the Boyer-Moore preprocessing table execution times were faster compared to the C implementation. However, that is not the case when it came to string length being equal to 10K.

We were able to analyze and conclude many things in our project. First, is that the parallelization for the KMP is incorrect, since we learned that it is inherently sequential, hence displaying long execution times. Second, we learned that the combined search algorithm wasn't able to become parallelized due to some factors. These factors are when the thread focuses on each character comparison, the output of the program may be inconsistent yet fast. Another factor is that if each thread in the kernel focuses on doing comparisons for all possible starting points, the output is most likely to be correct but the execution time is slow since its executing sequentially. 

For the parallelization of our program, we were only able to do parallelization of the BM preprocessing table, Memory Allocation, Data Transfer, Memory Advise, and Memory Prefetch. 
