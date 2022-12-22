# Running WRF-Hydro

### Overview

In this lesson, we cover the basics of constructing and running a
WRF-Hydro simulation using a prepared domain for the *gridded* routing
configuration. 

### Constructing a simulation with a prepared domain

In this section we describe the primary files needed to run a WRF-Hydro
simulation. A WRF-Hydro simulation consists of the following major components: -
executable/binary file - parameter files - domain files - forcing
files - namelists. In this lesson we only cover basic descriptions of these elements.

Model run-time options are specified in two namelist files
`hydro.namelist` and `namelist.hrldas`. These namelist files contain
file path specifications, simulation duration, physics options, and
output file selections, and others. 

### Orientation to the *example_case*

This lesson will use a prepared domain located in the
`/cluster/s7/workshop_dec2022/username/example_case` directory. The structure of the
`example_case` directory serves as a good example of how to organize
your domain files. 

**Step 1: Log in to the EMI HPC cluster using the SSH network protocol.**

Connect to the local network and Open your terminal and type:

```bash
username: ssh -X username@192.168.15.30
password: passwd
```

**Step 2: Move to your project folder and declare wrfhome variable**

```bash
cd /cluster/s7/workshop_dec2022/username/
wrfhome=/cluster/s7/workshop_dec2022/username/
```

**Step 3: copy example_case directory from `/scratch/WRF_HYDRO_SHARED/addisug` to your working directory**

```bash
cp /scratch/WRF_HYDRO_SHARED/addisug/example_case .
cd example_case
```

If using an official WRF-Hydro training example case, there will be a
study area map and a readme file that describes the geographic setting,
the directory, and the files. First lets take a look at the *study_map.PNG* with `display` command.

![study_map.PNG](/data/wrf/wrfhydropy_end-to-end_example/example_case/study_map.PNG)

**Step 4: lets view the readme file included with the domain for a brief description of the example case and its contents.**

``` bash
cat Readme.txt
```

**Step 5: take a look at the `example_case` directory.**

``` bash
ls 
```
In this example case several configurations for
WRF-Hydro: *National Water Model (NWM)*, *Gridded*, and *Reach* are created. 

#### FORCING

This directory contains all of the forcing data for our simulation. Note
that there is only one `FORCING` directory. The same forcing data can be
used with all three configurations.

#### The Gridded configuration directory

**Step 6: explore the `$wrfhome/example_case/Gridded` directory.**

``` bash
ls Gridded
```

The contents of this directory are described briefly in the `README.txt`
file that we viewed earlier, but we will discuss them again here. 

**DOMAIN**: Directory containing all geospatial data and input files for
the Gridded routing option with lakes included

**Step 7: DOMAIN: Directory containing all geospatial data and input files for the Gridded routing option with lakes included**

``` bash
ls Gridded/DOMAIN
```

| Filename                            | Description                                                                                                                                                                                                                              | Source                                                                             | Required                                                                    |
|------------------|------------------|------------------|------------------|
| Fulldom_hires.nc                    | High resolution full domain file. Includes all fields specified on the routing grid.                                                                                                                                                     | WRF-Hydro GIS pre-processing toolkit                                               | Yes                                                                         |
| GWBASINS.nc                         | 2D file defining the locations of groundwater basins on a grid                                                                                                                                                                           | WRF-Hydro GIS pre-processing toolkit                                               | When the baseflow bucket model is turned on and user defined mapping is off |
| GWBUCKPARM.nc                       | Groundwater parameter table containing bucket model parameters for each basin                                                                                                                                                            | WRF-Hydro GIS pre-processing toolkit                                               | When the baseflow bucket model is turned on                                 |
| LAKEPARM.nc                         | Lake parameter table containing lake model parameters for each catchment                                                                                                                                                                 | WRF-Hydro GIS pre-processing toolkit                                               | When lake and reservoir routing is turned on                                |
| hydro2dtbl.nc                       | Spatially distributed parameter table for lateral flow routing within WRF-Hydro.                                                                                                                                                         | create_SoilProperties.R script (will also be automatically generated by WRF-Hydro) | When using spatially distributed terrain routing parameters                 |
| geo_em.d01.nc                       | The data required to define the domain and geospatial attributes of a spatially-distributed, or gridded, 1-dimensional (vertical) land surface model (LSM)                                                                               | GEOGRID utility in the WRF preprocessing system (WPS)                              | Yes                                                                         |
| wrfinput_d01.nc                     | file including all necessary fields for the Noah-MP land surface model, but with spatially uniform initial conditions. Users should be aware that the model will likely require additional spin-up time when initialized from this file. | create_Wrfinput.R script                                                           | Yes                                                                         |
| soil_properties.nc                  | Spatially distributed land surface model parameters                                                                                                                                                                                      | create_SoilProperties.R script                                                     | If SPATIAL_SOIL compile-time option set to 1                                |
| GEOGRID_LDASOUT_Spatial_Metadata.nc | projection and coordinate information for the land surface model grid.                                                                                                                                                                   | pre-processor                                                                      | No, but allows for CF compliant outputs                                     |
| lake_shapes/                        | supplemental shape files that define lakes                                                                                                                                                                                               | pre-processor                                                                      | No                                                                          |

**Step 8: RESTART: Directory containing model restart files**

``` bash
ls Gridded/RESTART
```

Restart files are an essential part of the WRF-Hydro modeling system.
They are output on a fixed timestep specified by the user in the
`namelist.hrldas` and `hydro.namelist` files, and represent a complete
‘snapshot’ of the model state at that time. These files can be used to
restart a WRF-Hydro simulation from where the previous simulation
terminated with all the model states intact.

When running a WRF-Hydro simulation, you may start your simulation with
default initial conditions, referred to as a ‘cold start’. When starting
from a cold start, a model spinup period is needed to move the model
state away from the default initial conditions to a more realistic,
physically-based model state. Model output from the spinup period is
generally not used for interpretation.

Restart files output at the end of the spinup period can be used as the
initial conditions for subsequent simulations, referred to as a ‘warm
start’.

#### Namelists

`hydro.namelist` for the routing and hydrologic model and
`namelist.hrldas` for the land surface model.

**NOTE: These filenames are hard-coded into the model and can not be
changed.**

For all official WRF-Hydro domains, namelists will be included with each
of the model configurations. This is done so that a user can easily run
each configuration with minimal setup, and they serve as a starting
point for users to specify their own namelists for a given
configuration.

There are different namelists for each configuration because certain
namelist options are specific to the domain configuration used. 


**Step 9: Take a moment and read through the two namelists below and note how all filepaths are relative to the current directory containing the namelist.**

``` bash
cat Gridded/hydro.namelist
cat Gridded/namelist.hrldas
```

### Creating a simulation directory

Now that we have covered the major functional elements that constitute a
simulation, we will combine these elements and construct a simulation.
This is done by placing the `FORCING`, `Gridded/DOMAIN`,
`trunk/NDHMS/Run` directories and `namelist.hrldas` and `hydro.namelist`
files together in a directory that will be our simulation directory.
However, to save disk space it is often preferable to create symbolic
links rather than copying the actual files.

**NOTE: We will only use symbolic links with files that we will NOT be
editing**

![fig2.png](images/fig2.png)

In the following steps, we will construct our simulation directory.

**Step 10. Create simulation directory**

``` bash
mkdir -p $wrfhome/run_gridded_default
cd  $wrfhome/run_gridded_default
```

**Step 11. Copy model run files**

We will copy the required model run files from the*
*$wrfhome/WRFHYDRO/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS/Run* directory.
These files are small so we will make actual copies rather than symbolic
links in this case. Additionally, copies are preferred in this case
because a user may want to edit the \*.TBL files and as stated
previously symbolic links should not be used with files that we may
edit.

``` bash
cp $wrfhome/WRFHYDRO/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS/Run/*.TBL .
cp $wrfhome/WRFHYDRO/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS/Run/wrf_hydro.exe .

ls 
```

    CHANPARM.TBL  GENPARM.TBL  HYDRO.TBL  MPTABLE.TBL  SOILPARM.TBL  wrf_hydro.exe

**Step 12. Symlink *FORCING*, *DOMAIN* and *RESTART* files**

We will create symbolic links to the required files from the
`$wrfhome/example_case/` directory. These files can be
large so we will make symbolic links rather than copying the actual
files.

``` bash
ln -sf ~/wrf-hydro-training/example_case/FORCING .
ln -sf ~/wrf-hydro-training/example_case/Gridded/DOMAIN .
ln -sf ~/wrf-hydro-training/example_case/Gridded/RESTART .

ls 
```

    CHANPARM.TBL  FORCING      HYDRO.TBL    RESTART       wrf_hydro.exe
    DOMAIN        GENPARM.TBL  MPTABLE.TBL  SOILPARM.TBL

**Step 13. Copy *namelist* files**

Because we are using the default prepared namelists from the example
WRF-Hydro domain, we will copy those in as well. If you were using your
own namelists, they would likely be edited and copied from elsewhere.
These are small text files so we will make actual copies rather than
symbolic links.

``` bash
cp $wrfhome/example_case/Gridded/namelist.hrldas .
cp $wrfhome/example_case/Gridded/hydro.namelist .

ls 
```

    CHANPARM.TBL  GENPARM.TBL     MPTABLE.TBL      SOILPARM.TBL
    DOMAIN        hydro.namelist  namelist.hrldas  wrf_hydro.exe
    FORCING       HYDRO.TBL       RESTART

We have now constructed our simulation directory with all the requisite
files.


### Running WRF-Hydro using default run-time options

Now that we have constructed our simulation directory, we can run our
simulation. For this we will be using the `mpirun` command, which has a
number of arguments. For this simple case, we only need to supply one
argument, the number of cores. This is done with the `-np` argument, and
we will set it to 2 cores.

We will pipe the output to a log file because running a simulation can
generate a lot of standard output in the terminal.

**Step 14: Run the simulaiton**
``` bash
mpirun -np 2 ./wrf_hydro.exe >> run.log 2>&1
```

If your simulation ran successfully, there should now be a large number
of output files in the simulaiton directory. 

There are also four important files for determining the success or
failure of the run, diag_hydro.0000\*. The number of diag files is equal
to the number of cores used for the run. These diag_hydro.0000\* files
contain logs and diagnostics on the simulation run.

**Step 15: You can check that your simulation ran successfully by examining the last line of the diag files, which should read `The model finished successfully........`**

``` bash
tail -1 diag_hydro.00000
```

     The model finished successfully.......
     
     
## Working with WRF-Hydro inputs and output files

We will start with plotting a couple of variables from our geogrid file. 2D spatial with no temporal component.

**Step 16 Import xarray and turn on fancy HTML representations of datasets**

``` bash
conda create --name visualtools
conda activate visualtools
conda install -c anaconda xarray
# conda install -c conda-forge xarray
```

``` bash
import xarray as xr
xr.set_options(display_style="html")
```

**Step 17 Open the geogrid dataset - Load a dataset**

``` bash
geogrid = xr.open_dataset('$wrfhome/run_gridded_default/DOMAIN/geo_em.d01.nc')
```

**Step 18 Print some info about the dataset**

``` bash
geogrid
```

**Step 19 Plot the HGT_M variable, the topographic height in meters for each grid cell**

``` bash
geogrid.HGT_M.plot()
```

**Step 20 Plot the LU_INDEX variable, the dominant land-use class index for each grid cell**

``` bash
geogrid.LU_INDEX.plot(cmap="tab20")
```

**Step 21 How do you know what these values mean? You can check the parameter tables that come with the code to check lookup values. For example, the MPTABLE.TBL file lists the land cover categories.**

``` bash
cat $wrfhome/run_gridded_default/MPTABLE.TBL
```
**Step 22 The SOILPARM.TBL file lists the soil types.**

``` bash
cat $wrfhome/run_gridded_default/SOILPARM.TBL
```

**Step 23 Open the Fulldom dataset, the high-resolution routing domain file, Fulldom_hires.nc.**

``` bash
fulldom = xr.open_dataset('$wrfhome/run_gridded_default/DOMAIN/Fulldom_hires.nc')
```

**Step 24 Print some info about the dataset**

``` bash
fulldom
```

**Step 25 Plot the TOPOGRAPHY variable, the high-resolution elevation layer**

``` bash
fulldom.TOPOGRAPHY.plot(cmap="gist_earth")
```

**Step 26 Plot the CHANNELGRID variable, the location of channel cells on the high-resolution routing grid**
``` bash
fulldom.CHANNELGRID.plot()
```

You should notice an odd gap in the gridded channel network. This is where the lake sits in this particular configuration (gridded routing with a lake).

``` bash
fulldom.LAKEGRID.plot()
```

**Step 27 Open the soil_properties dataset and print some info about the file**

``` bash
soilprop = xr.open_dataset('$wrfhome/run_gridded_default/DOMAIN/soil_properties.nc')
soilprop
```

**Step 28 Plot the soil porosity (smcmax)**

``` bash
soilprop.smcmax.sel(soil_layers_stag = 0).plot(vmin=0.4, vmax=0.6, cmap="BuPu")
```
**Step 29 1D with temporal component - Open the chanobs multi-file dataset**

Now we will plot a timeseries from multiple netcdf files using the **open_mfdataset** command. We will plot a hydrograph at a gage point.

``` bash
chanobs = xr.open_mfdataset('/home/docker/wrf-hydro-training/output/lesson2/run_gridded_default/*CHANOBS*', combine='by_coords')
```

**Step 30 print some info about the chanobs file**

``` bash
chanobs
```


**Step 31 Plot a hydrograph for 1 gage point**

``` bash
#chanobs.sel(feature_id = 1).streamflow.plot()
chanobs.sel(feature_id = 2).streamflow.plot()
#chanobs.sel(feature_id = 3).streamflow.plot()
```





