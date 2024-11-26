# General exercises instructions

## Getting the materials

All course materials, slides and hands-out are available in the github repository. They can be downloaded with the command

```
git clone https://github.com/csc-training/high-level-gpu-programming.git
```

If you have a GitHub account you can also **Fork** this repository and clone
then your fork. That way you can easily commit and push your own solutions
to exercises.

### Repository structure

The exercise assignments are provided in various `README.md`s.
For most of the exercises, some skeleton codes are provided as starting point. In addition, all of the exercises have exemplary full codes
(that can be compiled and run) in the `solutions` folder. **Note that these are
seldom the only or even the best way to solve the problem.**

## Using supercomputers

Exercises can be carried out using the [LUMI](https://docs.lumi-supercomputer.eu/)  supercomputer, [Mahti](https://docs.csc.fi/computing/systems-mahti/), and [Intel DevCloud](https://console.cloud.intel.com/).

 ![](docs/img/cluster_diagram.jpeg)

LUMI can be accessed via ssh using the provided username and ssh key pair:
```
ssh -i <path-to-private-key> <username>@lumi.csc.fi
```
Mahti can be accessed via ssh using the provided username and CSC password:

```
ssh  <username>@mahti.csc.fi
```
The Intel DevCloud can be acces via the [web interface](https://console.cloud.intel.com/).

### Disk area

The  (computing and storage)  resources can be accessed on on supercomputers via project-based allocation system, where users are granted access based on the specific needs and goals of their projects. Running applications and storage area are directly linked ot this projects. For this event we have been granted access to the training `project_2012125` on Mahti and `project_462000456` on LUMI.

All the exercises in the supercomputers have to be carried out in the **scratch** disk area. The name of the scratch directory can be queried with the commands `csc-workspaces` on Mahti and `lumi-workspaces` onLUMI. As the base directory is shared between members of the project, you should create your own
directory:

on Mahti
```
cd /scratch/project_2012125
mkdir -p $USER
cd $USER
```

on LUMI
```
cd /scratch/project_462000752
mkdir -p $USER
cd $USER
```
The `scratch` area has quota of 1-2TB per project. More than enough for the training. In addition to this other areas are disks areas available. The `projappl/project_xyz` area is faster and can be used for storing the project applications (should not be used for data storage) and on LUMI the so called `flash/project_xyz` disk area can be used for IO intensive runs.

### Editors

For editing program source files you can use e.g. *nano* editor:

```
nano prog.f90
```
(`^` in nano's shortcuts refer to **Ctrl** key, *i.e.* in order to save file and exit editor press `Ctrl+X`)
Also other popular editors such as emacs and vim are available.


### Module environment
Supercomputers have a large number of users with different needs for development environments and applications. _Environment modules_ offers a convenient solution for dynamically altering the user's environment to suit their specific needs. This method makes it easier to use various compiler suites and app versions, making work smoother. Plus, when you switch compiler modules, the system takes care of loading the right library versions, cutting down on mistakes and keeping everything running smoothly. Also, loading a module that's customized for a specific app sets up the environment perfectly with just one command, making it super easy for users to get their software up and running.

This approach facilitates easier utilization of different compiler suites and application versions, enhancing workflow efficiency. Moreover, when changing compiler modules, the system automatically loads the correct versions of associated libraries, minimizing errors and ensuring seamless operation. Additionally, loading a module tailored to a specific application configures the environment correctly with a single command, simplifying the software setup process for users.

#### Common module commands
Below are the most commonly used module commands:

```
module load mod #Loads module **mod** in shell environment
module unload mod #Remove module **mod** from environment
module list #List loaded modules
module avail #List all available modules
module spider mod #Search for module **mod**
module show mod # Get information about module **mod**
```
Check for example the default cuda module on Mahti:
```
$ module show cuda
--------------------------------------------------------------------------------------------------------------------------
   /appl/spack/v017/modulefiles/linux-rhel8-x86_64/gcc/11.2.0/cuda/11.5.0.lua:
--------------------------------------------------------------------------------------------------------------------------
whatis("Name : cuda")
whatis("Version : 11.5.0")
whatis("Target : zen2")
whatis("Short description : CUDA is a parallel computing platform and programming model invented by NVIDIA. It enables dramatic increases in computing performance by harnessing the power of the graphics processing unit (GPU).")
help([[CUDA is a parallel computing platform and programming model invented by
NVIDIA. It enables dramatic increases in computing performance by
harnessing the power of the graphics processing unit (GPU). Note: This
package does not currently install the drivers necessary to run CUDA.
These will need to be installed manually. See:
https://docs.nvidia.com/cuda/ for details.]])
prepend_path("CPATH","/appl/spack/v017/install-tree/gcc-11.2.0/cuda-11.5.0-mg4ztb/include")
prepend_path("LIBRARY_PATH","/appl/spack/v017/install-tree/gcc-11.2.0/cuda-11.5.0-mg4ztb/lib64")
prepend_path("LD_LIBRARY_PATH","/appl/spack/v017/install-tree/gcc-11.2.0/cuda-11.5.0-mg4ztb/lib64")
prepend_path("PATH","/appl/spack/v017/install-tree/gcc-11.2.0/cuda-11.5.0-mg4ztb/bin")
prepend_path("CMAKE_PREFIX_PATH","/appl/spack/v017/install-tree/gcc-11.2.0/cuda-11.5.0-mg4ztb/")
setenv("CUDA_HOME","/appl/spack/v017/install-tree/gcc-11.2.0/cuda-11.5.0-mg4ztb")
setenv("CUDA_INSTALL_ROOT","/appl/spack/v017/install-tree/gcc-11.2.0/cuda-11.5.0-mg4ztb")
append_path("LIBRARY_PATH","/appl/spack/v017/install-tree/gcc-11.2.0/cuda-11.5.0-mg4ztb/lib64/stubs")
```
When we execute `module load cuda`, it will effectively modify the above environment variables. Now we can execute directly the cuda specifc commands such `nvcc` (cuda compiler)  or `nsys`(cuda profiler). 

## Compilation

SYCL is not part of the module system at the moment. The SYCL compilers were build for this training. We recommend that you use one of the two SYCL implementations.

### Intel oneAPI compilers
oneAPI is a collection of tool and library supporting a wide range of programming languange and parallel programming paradigms. It includes a SYCL implementation which supports all  Intel devices (CPUs, FPGAs, and GPUs) and has SYCL plug-ins for targeting Nvidia and AMD GPUs.
In order to use the intel SYCL compiler one has to  set the environment varibles first:

on Mahti:
```
. /projappl/project_2012125/intel/oneapi/setvars.sh --include-intel-llvm
module load cuda # This is needed for compiling sycl code for nvidia gpus
module load openmpi/4.1.2-cuda # This is neeeded for using CUDA aware MPI 
```

on LUMI:
```
. /projappl/project_462000752/intel/oneapi/setvars.sh --include-intel-llvm

module load LUMI
module load partition/G
module load rocm/6.0.3
export MPICH_GPU_SUPPORT_ENABLED=1 # Needed for GPU aware MPI
```
After this one can load other modules that might be needed for compiling the codes. With the environment set-up we can compile and run the SYCL codes.

On Mahti:
```
icpx -fuse-ld=lld -fsycl -fsycl-targets=nvptx64-nvidia-cuda,spir64_x86_64 -Xsycl-target-backend=nvptx64-nvidia-cuda --cuda-gpu-arch=sm_80 <sycl_code>.cpp
```
on LUMI
```
icpx -fsycl -fsycl-targets=amdgcn-amd-amdhsa,spir64_x86_64 -Xsycl-target-backend=amdgcn-amd-amdhsa --offload-arch=gfx90a <sycl_code>.cpp
```
Where `-fsycl` flag indicates that a sycl code is compiled and `-fsycl-targets` is used to instruct the compiler to generate optimized code for both CPU and GPU SYCL devices.

### AdaptiveCpp
This is another SYCL  implementation with support for many type of devices. No special set-up is needed, expect from loading the modules related to the backend (cuda or rocm).

on Mahti:
```
module purge
module use /scratch/project_2012125/cristian/spack/share/spack/modules/linux-rhel8-x86_64_v3/
module load hipsycl/24.06.0-gcc-10.4.0-4nny2ja
module load gcc/10.4.0
```
```
acpp -fuse-ld=lld -O3 -L/appl/spack/v020/install-tree/gcc-8.5.0/gcc-10.4.0-2oazqj/lib64/ --acpp-targets="omp.accelerated;cuda:sm_80" vector_add_buffer.cpp  vector_add_buffer.cpp
```
on LUMI:
```
module load LUMI
module load partition/G
module load rocm/6.0.3
export MPICH_GPU_SUPPORT_ENABLED=1
#export LD_LIBRARY_PATH=/appl/lumi/SW/LUMI-22.08/G/EB/Boost/1.79.0-cpeCray-22.08/lib:$LD_LIBRARY_PATH ??? 
#export LD_PRELOAD=/pfs/lustrep4/appl/lumi/SW/LUMI-22.08/G/EB/rocm/5.3.3/llvm/lib/libomp.so ??????
```

```
acpp -O3 --acpp-targets="omp.accelerated;hip:gfx90a" <sycl_code>.cpp
```
### MPI
MPI (Message Passing Interface) is a standardized and portable message-passing standard designed for parallel computing architectures. It allows communication between processes running on separate nodes in a distributed memory environment. MPI plays a pivotal role in the world of High-Performance Computing (HPC), this is why is important to know we could combine SYCL and MPI.

The SYCL implementation do not know anything about MPI. Intel oneAPI contains mpi wrappers, however they were not configure for Mahti and LUMI. Both Mahti and LUMI provide wrappers that can compile applications which use MPI, but they can not compile SYCL codes. We can however extract the MPI related flags and add them to the SYCL compilers.

For exampl on Mahti in order to use CUDA-aware MPI we would first load the modules:
```
module load cuda
module load openmpi/4.1.2-cuda
```
The environment would be setup for compiling a CUDA code which use GPU to GPU communications. We can inspect the `mpicxx` wrapper:
```
$ mpicxx -showme
/appl/spack/v017/install-tree/gcc-8.5.0/gcc-11.2.0-zshp2k/bin/g++ -I/appl/spack/v017/install-tree/gcc-11.2.0/openmpi-4.1.2-bylozw/include -I/appl/spack/v017/install-tree/gcc-11.2.0/openmpi-4.1.2-bylozw/include/openmpi -I/appl/spack/syslibs/include -pthread -L/appl/spack/v017/install-tree/gcc-11.2.0/openmpi-4.1.2-bylozw/lib -L/appl/spack/syslibs/lib -Wl,-rpath,/appl/spack/v017/install-tree/gcc-8.5.0/gcc-11.2.0-zshp2k/lib/gcc/x86_64-pc-linux-gnu/11.2.0 -Wl,-rpath,/appl/spack/v017/install-tree/gcc-8.5.0/gcc-11.2.0-zshp2k/lib64 -Wl,-rpath -Wl,/appl/spack/v017/install-tree/gcc-11.2.0/openmpi-4.1.2-bylozw/lib -Wl,-rpath -Wl,/appl/spack/syslibs/lib -lmpi
```
We note that underneath `mpicxx` is calling `g++` with a lots of MPI related flags. We can obtain and use these programmatically with `mpicxx --showme:compile` and `mpicxx --showme:link`
for compiling the SYCL+MPI codes:
```
icpx -fuse-ld=lld -fsycl -fsycl-targets=nvptx64-nvidia-cuda -Xsycl-target-backend=nvptx64-nvidia-cuda --cuda-gpu-arch=sm_80 `mpicxx --showme:compile` `mpicxx --showme:link` <sycl_mpi_code>.cpp
```
or
```
module purge
module use /scratch/project_2012125/cristian/spack/share/spack/modules/linux-rhel8-x86_64_v3/
module load hipsycl/24.06.0-gcc-10.4.0-4nny2ja
module load gcc/10.4.0
acpp -fuse-ld=lld -O3 -L/appl/spack/v020/install-tree/gcc-8.5.0/gcc-10.4.0-2oazqj/lib64/ --acpp-targets="omp.accelerated;cuda:sm_80" `mpicxx --showme:compile` `mpicxx --showme:link` <sycl_mpi_code>.cpp
```

Similarly on LUMI. First we set up the envinronment and load the modules as indicated above
```
. /projappl/project_462000752/intel/oneapi/setvars.sh --include-intel-llvm

module load LUMI
module load partition/G
module load rocm/6.0.3
export MPICH_GPU_SUPPORT_ENABLED=1
```
Now compile with intel compilers:

```
icpx -fsycl -fsycl-targets=amdgcn-amd-amdhsa,spir64_x86_64 -Xsycl-target-backend=amdgcn-amd-amdhsa  --offload-arch=gfx90a `CC --cray-print-opts=cflags` <sycl_mpi_code>.cpp `CC --cray-print-opts=libs`
```
Or with AdaptiveCpp:
```
module load LUMI/24.03
module load partition/G
module load rocm/6.0.3
export PATH=/projappl/project_462000752/ACPP/bin/:$PATH
export LD_LIBRARY_PATH=/appl/lumi/SW/LUMI-24.03/G/EB/Boost/1.83.0-cpeGNU-24.03/lib64/:$LD_LIBRARY_PATH
export LD_PRELOAD=/opt/rocm-6.0.3/llvm/lib/libomp.so
```
```
acpp -O3 --acpp-targets="omp.accelerated;hip:gfx90a" `CC --cray-print-opts=cflags` <sycl_mpi_code>.cpp `CC --cray-print-opts=libs`
```

## Running applications in supercomputers
Programs need to be executed via the batch job system.
```
sbatch job.sh
```
The `job.sh` file contains all the necessary information (number of nodes, tasks per node, cores per taks, number of gpus per node, etc.)  for the `slurm` to execute the program.

### Useful environment variables

Use [`SYCL_UR_TRACE`](https://intel.github.io/llvm-docs/EnvironmentVariables.html#sycl-pi-trace-options) to enable runtime tracing (e.g. device discovery):

    export SYCL_UR_TRACE=1


### Running on Mahti

#### CPU applications
```
#!/bin/bash
#SBATCH --job-name=example
#SBATCH --account=project_2012125
#SBATCH --partition=medium
#SBATCH --reservation=high_level_gpu_programming_medium_day_1 # This changes every day to _2 and _3, valid 09:00 to 17:00 
#SBATCH --time=00:05:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=1

srun my_cpu_exe
```

Save the script *e.g.* as `job.sh` and submit it with `sbatch job.sh`.
The output of job will be in file `slurm-xxxxx.out`. You can check the status of your jobs with `squeue -u $USER` and kill possible hanging applications with
`scancel JOBID`.

The reservation `hlgp-cpu-f2024` for partition `medium` is available during the training days and it
is accessible only if the users are part of `project_2012125`.

Some applications use MPI, in this case the number of node and number of tasks per node will have to be adjusted accordingly.

#### GPU applications

When running GPU programs, few changes need to made to the batch job
script. The `partition` is now different, and one must also request explicitly a given number of GPUs per node. As an example, in order to use a
single GPU with single MPI task and a single thread use:
```
#!/bin/bash
#SBATCH --job-name=example
#SBATCH --account=project_2012125
#SBATCH --partition=gpumedium
#SBATCH --reservation=high_level_gpu_programming_gpumedium_day_1 # This changes every day to _2 and _3, valid 09:00 to 17:00 
#SBATCH --time=00:05:00
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --time=00:05:00
#SBATCH --gres=gpu:a100:1

srun my_gpu_exe
```
The reservations `....medium_day_1` are  valid on Wednesday, 09:00 to 17:00. On Thursday we will use `...medium_day_2` , while on Friday `...medium_day_3`. Outside the course hours, you can use gputest partition instead without the reservation argument, ie, 
```
srun --account=project_2012125 --nodes=1 --partition=gputest --gres=gpu:a100:1 --time=00:05:00 ./my_gpu_exe
```


### Running on LUMI

LUMI is similar to Mahti.

#### CPU applications

```
#!/bin/bash
#SBATCH --job-name=example
#SBATCH --account=project_462000752
#SBATCH --partition=small
##SBATCH --reservation=GPUtraining_small
#SBATCH --time=00:05:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=1

srun my_cpu_exe
```


#### GPU applications

```
#!/bin/bash
#SBATCH --job-name=example
#SBATCH --account=project_462000752
#SBATCH --partition=small-g
#SBATCH --reservation=GPUtraining_small-g
#SBATCH --time=00:05:00
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=1
#SBATCH --gpus-per-node=1

srun my_gpu_exe
```
Similarly to Mahti, on LUMI we have 2 cpu nodes reservered for us, and as well 2 gpu nodes. 

**NOTE** Some exercises have additional instructions of how to run!
