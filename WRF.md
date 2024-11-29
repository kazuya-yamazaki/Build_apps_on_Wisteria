# How to build WRF
If you want to perform the entire simulation process on Wisteria, you need both WRF (a forecast model) and WPS (preprocessing tool). In this case, you need to go through four steps:
- [Fill out registation on the WRF page](#fill-out-registation-on-the-wrf-page)
- [Decide where to install WRF](#decide-where-to-install-wrf)
- [Compile WRF for Odyssey](#compile-wrf-for-odyssey)
- Compile WRF for Intel CPUs
- Compile WPS for Intel CPUs

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
