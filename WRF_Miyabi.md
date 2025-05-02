# Running WRF on Miyabi
Miyabi consists of Miyabi-G (larger and GPU-equipped subsystem) and Miyabi-C (small and CPU-only subsystem). While both subsystems are capable of running WRF, it runs more efficiently on Miyabi-C.

[Miyabi-C](WRF_Miyabi_C.md)
- Pros
  - Faster than Miyabi-C and consumes 20 % less token per node hour
  - You can build both WRF and WPS using the default Intel compiler
- Cons
  - Only 150-200 nodes are available for regular jobs
  - Clusters like Miyabi-C may become rarer and smaller in the upcoming decade because many supercomputing centers are leading toward GPU-equipped machines

[Miyabi-G](WRF_Miyabi_G.md)
- Pro
  - Around 1000 nodes are available
- Cons
  - Generally slower than Miyabi-C because the current public version of WRF is not compatible to GPUs
  - Consumes 20 % more token per node hour
  - WPS is currently not compatible to NVIDIA HPC SDK
    - This would force you to either build another set of WRF+WPS on Miyabi-C or perform preprocessing on your local computer
