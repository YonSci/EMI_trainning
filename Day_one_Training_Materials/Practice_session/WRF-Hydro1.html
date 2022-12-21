# WRF-Hydro Installation, Configuration, & Test Runs

### WRF-Hydro Model Installation on EMI HPC Cluster (Mandatory)

Step 1: Log in to the EMI HPC cluster using the SSH network protocol.

Connect to the local network and Open your terminal and type:

```bash
username: ssh -X user1@192.168.15.30
password: xxxx
```

Step 2: Move to your project folder 

```bash
cd /cluster/s7/workshop_dec2022/user1/
wrfhome=/cluster/s7/workshop_dec2022/user1/
```

Step 3: Load the necessary module environments 

Check the module available on the cluster 

```bash
module avail
```

Load the netCDF and Openmpi modules 

```bash
module load netcdf  
module load openmpi
```

Verify that the modules are loaded

```bash
module list
```

Step 4: Extract the wrfhydro source code to the installation directory

```bash
mkdir -p wrfhydro/downloads
mkdir -p WRFHYDRO

cd /scratch/WRF_HYDRO_SHARED/
cp WRFHYDRO.5.2.tar.gz $wrfhome/wrfhydro/downloads
tar xzvf WRFHYDRO.5.2.tar.gz -C  $wrfhome/WRFHYDRO


# if you have good internet network  
# cd wrfhydro/downloads
# wget -c https://github.com/NCAR/wrf_hydro_nwm_public/archive/refs/tags/v5.2.0.tar.gz -O WRFHYDRO.5.2.tar.gz
# cp WRFHYDRO.5.2.tar.gz ../xxxxx/wrfhydro/downloads
# tar xzvf WRFHYDRO.5.2.tar.gz -C  $wrfhome/WRFHYDRO
```

Step 4:  Make a copy of the compile time options script and specify compile options

```bash
cd $wrfhome/WRFHYDRO/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS/template
cp setEnvar.sh ../
cd ..
vi setEnvar.sh
```

Description of WRF-Hydro compile time options

| Variable                     | Options     | Description                                                                                                                                                                                                            |
| ---------------------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WRF_HYDRO                    | 1=On        | Always set to 1 for compiling WRF-Hydro                                                                                                                                                                                |
| HYDRO_D                      | 0=Off, 1=On | Enhanced diagnostic output for debugging.                                                                                                                                                                              |
| SPATIAL_SOIL                 | 0=Off, 1=On | Spatially distributed parameters for Noah-MP. This allows Noah-MP to use spatially distributed parameters for the land surface model rather than parameter based upon soil class and land use category look up tables. |
| WRF_HYDRO_RAPID              | 0=Off, 1=On | Coupling with the RAPID routing model. This option is not currently supported.                                                                                                                                         |
| NCEP_WCOSS                   | 0=Off, 1=On | Do not use unless working on the WCOSS machines. \|                                                                                                                                                                    |
| NWM_META                     | 0=Off, 1=On | NWM output metadata. Do not use unless running the operational NWM.                                                                                                                                                    |
| WRF_HYDRO_NUDGING            | 0=Off, 1=On | Streamflow nudging. Enable the streamflow nudging routines for Muskingum-Cunge Routing.                                                                                                                                |
| WRFIO_NCD_LARGE_FILE_SUPPORT | 0=Off, 1=On | To compile with netCDF large file support                                                                                                                                                                              |

###### Make sure the Spatially distributed parameters for Noah-MP =1

###### Large netcdf file support = 1

![](/home/yoni/Pictures/Screenshots/compile_timeoption.png)

Step 5: Compile WRF-Hydro offline with the NoahMP model

```bash
cd $wrfhome/WRFHYDRO/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS
./configure 

# Please select Option 2 from following supported linux compilers
1 pgi
2 gfortran
3 ifort
4 luna intel parallel (WCOSS Luna)
5 ifort_omp intel openmp
6 intel.cray_xc intel parallel (cray_xc)
7 cray_fortran Cray Fortran PE (ftn)
0 exit 
```

In this case, the WRFhydro will be compiled with the Noah-MP land surface model. We can compile the model by running `compile offline NoahMP.sh` with `setEnvar.sh` script.

```bash
./compile_offline_NoahMP.sh setEnvar.sh | tee compile.log
```

Step 6: You can check the compile log to make sure that compilation completed successfully

```bash
tail -13 compile.log
```

Check the contents of the `Run` directory in the `trunk/NDHMS` directory. The Run directory contains `parameter tables`, two `namelist files`, and the `executable` files.

```bash
ls Run
```

![](/home/yoni/Pictures/Screenshots/run_file.png)

Description of the file contents of the `Run` directory.

| Filename             | Description                                                                                                                  |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| CHANPARM.TBL         | Channel routing parameter table                                                                                              |
| GENPARM.TBL          | Global parameters for the Noah-MP land surface model                                                                         |
| HYDRO.TBL            | Parameter table for lateral flow routing within WRF-Hydro. The parameters are specified by land cover type or soil category. |
| MPTABLE.TBL          | Land surface model parameters that are a function of land cover type.                                                        |
| SOILPARM.TBL         | Land surface model parameters assigned based upon the soil classification.                                                   |
| hydro.namelist       | Specifies the settings for all of the routing components of WRF-Hydro                                                        |
| namelist.hrldas      | Specifies the land surface model options to be used.                                                                         |
| wrf_hydro.exe        | Symbolic link to the WRF-Hydro executable file                                                                               |
| wrf_hydro_NoahMP.exe | Exectable file for WRF-Hydro with Noah-MP                                                                                    |

Step 7: Create an archive directory for the output files 

```bash
cd $wrfhome
mkdir -p domain/NWM
```

Copy the `.TBL` and  `executable`  files into the  `domain/NWM`

```bash
#Copy the *.TBL files to the NWM directory.
cp $wrfhome/WRFHYDRO/wrf_hydro_nwm_public-5.2.0*/trunk/NDHMS/Run/*.TBL $wrfhome/domain/NWM
#Copy the wrf_hydro.exe file to the NWM directory.
cp $wrfhome/WRFHYDRO/wrf_hydro_nwm_public-5.2.0*/trunk/NDHMS/Run/wrf_hydro.exe domain/NWM
```

Step 8:  Download the `Croton_NY` test case dataset

```bash
cd $wrfhome/downloads/
cp croton_NY_training_example_v5.2.tar.gz /scratch/WRF_HYDRO_SHARED/
tar xzvf croton_NY_training_example_v5.2.tar.gz 
```

Step 9: Copy forcing, domain & other configuration files to the `domain/NWM`

```bash
cd example_case
cp -r example_case/FORCING $/wrfhydro/domain/NWM
cp -r example_case/DOMAIN  $wrfhome/wrfhydro/domain/NWM
cp -r example_case/NWM/DOMAIN  $wrfhome/wrfhydro/domain/NWM
cp -r example_case/NWM/RESTART  $wrfhome/wrfhydro/domain/NWM
cp -r example_case/NWM/nudgingTimeSliceObs  $wrfhome/wrfhydro/domain/NWM
cp -r example_case/NWM/namelist.hrldas  $wrfhome/wrfhydro/domain/NWM
cp -r example_case/NWM/hydro.namelist  $wrfhome/wrfhydro/domain/NWM
```

Step 10: Run Croton NY Test Case

```bash
mpirun ./wrf_hydro.exe

# Move to the archive directory to view the output files 
cd $wrfhome/wrfhydro/domain/NWM
ls -lah HYDRO_RST*
```

"Congratulations! ⭐ 🎉 You've successfully installed  and test the WRFHydro Model !!!
