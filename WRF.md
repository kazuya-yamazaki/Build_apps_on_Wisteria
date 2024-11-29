# How to build WRF
If you want to perform the entire simulation process on Wisteria, you need both WRF (a forecast model) and WPS (preprocessing tool). In this case, you need to go through four steps:
- [Fill out registation on the WRF page](#fill-out-registation-on-the-wrf-page)
- [Decide where to install WRF](#decide-where-to-install-wrf)
- [Compile WRF for Odyssey](#compile-wrf-for-odyssey)
- [Compile WRF for Intel CPUs](#compile-wrf-for-intel-cpus)
- [Compile WPS for Intel CPUs](#compile-wps-for-intel-cpus)

## Fill out registation on the WRF page
Go to [https://www2.mmm.ucar.edu/wrf/users/download/get_source.html] and register as a new user or submit your registered e-mail address as a "Returning User".

## Decide where to install WRF
On Wisteria and many other supercomputers, you have to place essentially all files under a directory separated from your home directory.

> [!IMPORTANT]
Do not build WRF or store any large files in your home directory!

### Your root working directory
Your root working directory on Wisteria is `/work/your_group_name/your_user_ID/`, in which you can create a directory for WRF.
After logging in, you can check your group name by typing `id -Gn` in the terminal. Most group names look like `gx00` or `jh000000a`. You can use an environment variable `${USER}` to represent your user ID. For example, if your group name is `gx00`, your root working directory is `/work/gx00/${USER}`.

### Create WRF-WPS root directory
You can create a directory for WRF and WPS anywhere inside [Your root working directory](#your-root-working-directory). Here, I assume that you want to install WRF in `/work/your_group_name/${USER}/WRF_WPS`. Then, do the following to create your WRF root directory:

```
mkdir -p /work/your_group_name/${USER}/WRF_WPS
WRF_ROOT=/work/your_group_name/${USER}/WRF_WPS
```

> [!NOTE]
> Replace `your_group_name` with one of your group id, which may look like `gx00` or `jh000000a`. See [Your root working directory](#your-root-working-directory) for details.

## Compile WRF for Odyssey
Make sure that `WRF_ROOT` is defined as the [root directory for WRF](#create-wrf-wps-root-directory). Then, run the following:

```
module purge
module load fj fjmpi hdf5 netcdf netcdf-fortran
export LANG=C
export NETCDF_classic=1
export NETCDF=$NETCDF_FORTRAN_DIR
export NETCDF_C=$NETCDF_DIR
export HDF5=$HDF5_DIR
git clone https://github.com/wrf-model/WRF.git

cd ${WRF_ROOT}/WRF
./configure
```

The `./configure` script will ask two questions.
- "Enter selection": select a number featuring Fujitsu and dmpar. It is `82` in WRF 4.6.1, but it may change.
- "Compile for nesting?": select whatever you like. Choose the basic nesting if moving nesting is unnecessary.
If you see some errors, you'll have to read them and look them up on the Internet, or ask experts for help. If the configuration succeeds, you can go ahead and build WRF:
```
nohup ./compile em_real &> compile.log
```
Note that the compilation may take hours.

> [!NOTE]
> WRF binaries built in this section are valid only in jobs submitted to Odyssey.
> Before you execute WRF binaries in your job scripy, load `fj fjmpi hdf5 netcdf netcdf-fortran` modules.

## Compile WRF for Intel CPUs
If you perform all preprocessing on your local system and you will not use WPS, this step is unnecessary.

WPS, the preprocessing tool, requires a build of WRF in the compilation process. However, the WPS configuration tool is apparently imcompatible to the Fujitsu compiler. Hence, you'll have to build another copy of WRF using other compilers. Here, I introduce how to build WRF using the Intel compiler.

Make sure that `WRF_ROOT` is defined as the [root directory for WRF](#create-wrf-wps-root-directory). Then, run the following:

```
cd ${WRF_ROOT}
mv WRF WRF_odyssey

git clone https://github.com/wrf-model/WRF.git
module purge
module load intel hdf5 netcdf netcdf-fortran
export NETCDF=$NETCDF_FORTRAN_DIR
export NETCDF_C=$NETCDF_DIR
export HDF5=$HDF5_DIR
cd ${WRF_ROOT}/WRF
./configure
```

The `Enter selection` prompt will appear again. This time, select a number (13 in WRF 4.6.1) featuring "serial" and "INTEL" with **NO** midifiers such as "Xeon Phi" and "SGI MPT". 

As for the nesting, select `0=no nesting` if available because you are NOT going to use this build for actual simulations.

If the configuration succeeds, you can go ahead and build WRF:
```
nohup ./compile em_real &> compile.log
cd ${WRF_ROOT}
mv WRF WRF_intel
```
Note that the compilation may take hours again.

## Compile WPS for Intel CPUs
Make sure that `WRF_ROOT` is defined as the [root directory for WRF](#create-wrf-wps-root-directory). Then, run the following:
```
module purge
module load intel hdf5 netcdf netcdf-fortran
export NETCDF=$NETCDF_FORTRAN_DIR
export NETCDF_C=$NETCDF_DIR
export HDF5=$HDF5_DIR
export WRF_DIR=${WRF_ROOT}/WRF_intel

cd ${WRF_ROOT}
git clone https://github.com/wrf-model/WPS.git
cd WPS
./configure
```
This time, select a number described as `Linux x86_64, Intel Classic compilers    (serial)`.
If the configuration succeeds, you can build WPS:
```
nohup ./compile &> compile.log
```

> [!NOTE]
> WPS binaries built in this section are valid on prepost nodes and login nodes, but not on Odyssey.
> Please use the prepost node to prepare the initial conditions. However, if you are ABSOLUTELY SURE that the process finishes within a few minutes, you can launch WPS binaries on the login node.
> Before you launch WPS binaries such as `ungrib.exe` and `metgrid.exe`, load `intel hdf5 netcdf netcdf-fortran` modules.

