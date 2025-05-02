# How to build WRF on Miyabi-C
- [Fill out registation on the WRF page](#fill-out-registation-on-the-wrf-page)
- [Decide where to install WRF](#decide-where-to-install-wrf)
- [Compile WRF](#compile-wrf)
- [Compile WPS](#compile-wps)

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
You can create a directory for WRF and WPS anywhere inside [Your root working directory](#your-root-working-directory). Here, I assume that you want to install WRF in `/work/your_group_name/${USER}/WRF_WPS_C`. Then, do the following to create your WRF root directory:

```
mkdir -p /work/your_group_name/${USER}/WRF_WPS_C
WRF_ROOT=/work/your_group_name/${USER}/WRF_WPS_C
```

> [!NOTE]
> Replace `your_group_name` with one of your group id, which may look like `gx00` or `jh000000a`. See [Your root working directory](#your-root-working-directory) for details.

## Compile WRF
Make sure that `WRF_ROOT` is defined as the [root directory for WRF](#create-wrf-wps-root-directory). Then, run the following:

```
module purge
module load intel impi hdf5 netcdf netcdf-fortran
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
- "Enter selection": select a number featuring `INTEL (ifx/icx) : oneAPI LLVM` and `dmpar`. It is `78` in WRF 4.7.0, but it may change.
- "Compile for nesting?": select whatever you like. Choose the basic nesting if moving nesting is unnecessary.
If you see some errors, you'll have to read them and look them up on the Internet, or ask experts for help. If the configuration succeeds, you can go ahead and build WRF:
```
nohup ./compile em_real &> compile.log
```
The compilation should take around 20 minutes.

> [!NOTE]
> When you run WRF on Miyabi-C, load `intel impi hdf5 netcdf netcdf-fortran` modules in your job script before invoking `wrf.exe`.

## Compile WPS

Make sure that `WRF_ROOT` is defined as the [root directory for WRF](#create-wrf-wps-root-directory). Then, run the following:
```
module purge
module load intel impi hdf5 netcdf netcdf-fortran
export NETCDF=$NETCDF_FORTRAN_DIR
export NETCDF_C=$NETCDF_DIR
export HDF5=$HDF5_DIR
export WRF_DIR=${WRF_ROOT}/WRF_intel

cd ${WRF_ROOT}
git clone https://github.com/wrf-model/WPS.git
cd WPS
```

At present, you have to apply one modification to the WPS codes.

In `external/Makefile`, Insert `--disable-strict` to the configuration of Jasper. The modified line may look like this:
```
	(cd jasper-1.900.29; ./configure --prefix=$(INTERNAL_GRIB2_PATH) --disable-strict --disable-shared && make && make install)
```
Then execute `./configure --build-grib2-libs` and select a number described as `Linux x86_64, Intel oneAPI compilers    (serial)`.
If the configuration succeeds, you can build WPS:
```
nohup ./compile &> compile.log
```

> [!NOTE]
> WPS binaries built in this section are valid on Miyabi-C compute nodes, prepost nodes, and Miyabi-C login nodes.
> Please use the compute nodes (as a job) or the prepost nodes to prepare the initial conditions. However, if you are ABSOLUTELY SURE that the process finishes within a few minutes, you can launch WPS binaries on the login node.
> Before you launch WPS binaries such as `ungrib.exe` and `metgrid.exe`, load `intel impi hdf5 netcdf netcdf-fortran` modules.
