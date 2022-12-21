# WRF-Hydro Model Installation on personal computers (Optional)

## Installing basic package

```bash
sudo apt update
sudo apt upgrade  
```

```bash
sudo apt install gcc gfortran g++ libtool automake autoconf make m4 default-jre default-jdk csh ksh tcsh okular cmake time xorg openbox xauth git python3 python3-dev python2 python2-dev cmake mlocate
```

## Setting paths & creating directories

Go to home directory

```bash
cd ~ 
```

Export the **HOME** and its path to the environment variables

```bash
export HOME=`cd;pwd`
```

Set the **WRFHYDRO/Libs** directory within the root folder (HOME)

```bash
export DIR=$HOME/WRFHYDRO/Libs
```

Make the WRFHYDRO directory

```bash
mkdir $HOME/WRFHYDRO
```

Move to **WRFHYDRO** directory

```bash
cd $HOME/WRFHYDRO
```

Inside the **WRFHYDRO** directory make a **Download** folder

```bash
mkdir Downloads
```

Inside the **Downloads** folder make **Libs** folder

```bash
mkdir Libs
```

Inside the **Libs** folder make **grib2** folder

```bash
mkdir Libs/grib2
```

Inside the **Libs** folder make **NETCDF** folder

```bash
mkdir Libs/NETCDF
```

Inside the **Libs** folder make **MPICH** folder

```bash
mkdir Libs/MPICH##  Downloading Libraries
```

## Downloading Libraries

 Move to **Downloads** folder

```bash
cd Downloads
```

### Download zlib library

```bash
wget -c -4 https://github.com/madler/zlib/archive/refs/tags/v1.2.12.tar.gz
```

### Download HDF library

```
wget -c -4 https://github.com/HDFGroup/hdf5/archive/refs/tags/hdf5-1_12_2.tar.gz
```

### Download netCDF-C library

```bash
wget -c -4 https://github.com/Unidata/netcdf-c/archive/refs/tags/v4.9.0.tar.gz
```

### Download netCDF-fortran library

```bash
wget -c -4 https://github.com/Unidata/netcdf-fortran/archive/refs/tags/v4.6.0.tar.gz
```

### Download MPICH library

```bash
wget -c -4 https://github.com/pmodels/mpich/releases/download/v4.0.2/mpich-4.0.2.tar.gzwget -c -4 https://github.com/pmodels/mpich/releases/download/v4.0.2/mpich-4.0.2.tar.gz
```

### Download libpng library

```bash
wget -c -4 https://download.sourceforge.net/libpng/libpng-1.6.37.tar.gz
```

### Download jasper library

```bash
wget -c -4 https://www.ece.uvic.ca/~frodo/jasper/software/jasper-1.900.1.zip
```

## Setting Compilers

Set **GCC** compiler

```bash
export CC=gcc
```

Set **G++** compiler

```bash
export CXX=g++
```

Set **Fortran** compiler

```bash
export FC=gfortran
export F77=gfortran
```

Print **GCC** version

```bash
export gcc_version="$(gcc -dumpversion)"
```

Print **gfortran** version

```bash
export gfortran_version="$(gfortran -dumpversion)"
```

Print **G++** version

```bash
export gplusplus_version="$(g++ -dumpversion)"
```

Set version 10

```bash
export version_10="10"
```

Fix compiler mismatch

```bash
if [ $gcc_version -ge $version_10 ] || [ $gfortran_version -ge $version_10 ] || [ $gplusplus_version -ge $version_10 ]
then
  export fallow_argument=-fallow-argument-mismatch 
  export boz_argument=-fallow-invalid-boz
else 
  export fallow_argument=
  export boz_argument=
fi
```

Set the FFLAGS and FCFLAGS

```bash
export FFLAGS=$fallow_argument
export FCFLAGS=$fallow_argument
```

## Installing main libraries

### Installing zlib

```bash
# Uncalling compilers due to comfigure issue with zlib1.2.12
# With CC & CXX definied ./configure uses different compiler Flags

cd $HOME/WRFHYDRO/Downloads
tar -xvzf v1.2.12.tar.gz
cd zlib-1.2.12/
CC= CXX= ./configure --prefix=$DIR/grib2
make
make install
#make check
```

### Installing libpng

```bash
cd ..
or
cd $HOME/WRFHYDRO/Downloads

export LDFLAGS=-L$DIR/grib2/lib
export CPPFLAGS=-I$DIR/grib2/include
tar -xvzf libpng-1.6.37.tar.gz
cd libpng-1.6.37/
./configure --prefix=$DIR/grib2
make
make install
#make check
```

### Installing Jasper

```bash
cd ..
or
cd $HOME/WRFHYDRO/Downloads

unzip jasper-1.900.1.zip
cd jasper-1.900.1/
autoreconf -i
./configure --prefix=$DIR/grib2
make
make install

export JASPERLIB=$DIR/grib2/lib
export JASPERINC=$DIR/grib2/include
```

### Installing MPICH

```bash
cd ..
or
cd $HOME/WRFHYDRO/Downloads

tar -xvzf mpich-4.0.2.tar.gz
cd mpich-4.0.2/
F90= ./configure --prefix=$DIR/MPICH --with-device=ch3 FFLAGS=$fallow_argument FCFLAGS=$fallow_argument
make
make install
#make check

export PATH=$DIR/MPICH/bin:$PATH
```

### Installing HDF5

```bash
cd ..
or
cd $HOME/WRFHYDRO/Downloads


tar -xvzf hdf5-1_12_2.tar.gz
cd hdf5-hdf5-1_12_2
./configure --prefix=$DIR/grib2 --with-zlib=$DIR/grib2 --enable-hl --enable-fortran
make 
make install
#make check

export HDF5=$DIR/grib2
export LD_LIBRARY_PATH=$DIR/grib2/lib:$LD_LIBRARY_PATH
```

### Installing NETCDF-C

```bash
cd ..
or
cd $HOME/WRFHYDRO/Downloads

tar -xzvf v4.9.0.tar.gz
cd netcdf-c-4.9.0/

export CPPFLAGS=-I$DIR/grib2/include 
export LDFLAGS=-L$DIR/grib2/lib

./configure --prefix=$DIR/NETCDF --disable-dap
make 
make install
#make check

export PATH=$DIR/NETCDF/bin:$PATH
export NETCDF=$DIR/NETCDF
```

### Installing netCDF-fortran

```bash
cd ..
or
cd $HOME/WRFHYDRO/Downloads

tar -xvzf v4.6.0.tar.gz
cd netcdf-fortran-4.6.0/

export LD_LIBRARY_PATH=$DIR/NETCDF/lib:$LD_LIBRARY_PATH
export CPPFLAGS=-I$DIR/NETCDF/include 
export LDFLAGS=-L$DIR/NETCDF/lib

./configure --prefix=$DIR/NETCDF --disable-shared
make 
make install
#make check
```

## Installing, Configuring & Compiling WRF-HYDRO V5.2.0 in Standalone Mode

```bash
cd $HOME/WRFHYDRO/Downloads
wget -c https://github.com/NCAR/wrf_hydro_nwm_public/archive/refs/tags/v5.2.0.tar.gz -O WRFHYDRO.5.2.tar.gz
tar -xvzf WRFHYDRO.5.2.tar.gz -C $HOME/WRFHYDRO
```

### Modifying WRF-HYDRO Environment

```bash
#Echo commands use due to lack of knowledge
cd $HOME/WRFHYDRO/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS/template
```

```bash
sed -i 's/SPATIAL_SOIL=0/SPATIAL_SOIL=1/g' setEnvar.sh
echo " " >> setEnvar.sh
echo "# Large netcdf file support: 0=Off, 1=On." >> setEnvar.sh
echo "export WRFIO_NCD_LARGE_FILE_SUPPORT=1" >> setEnvar.sh
ln setEnvar.sh $HOME/WRFHYDRO/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS
```

### Compile WRF-Hydro offline with the NoahMP

```bash
cd $HOME/WRFHYDRO/wrf_hydro_nwm_public-5.2.0/trunk/NDHMS

./configure # Option 2
./compile_offline_NoahMP.sh setEnvar.sh

ls -lah Run/*.exe #Test to see if .exe files have compiled
```

### Copy the .TBL and executable files

```bash
cd $HOME/WRFHYDRO
mkdir -p $HOME/WRFHYDRO/domain/NWM

#Copy the *.TBL files to the NWM directory.
cp wrf_hydro_nwm_public*/trunk/NDHMS/Run/*.TBL domain/NWM
#Copy the wrf_hydro.exe file to the NWM directory.
cp wrf_hydro_nwm_public*/trunk/NDHMS/Run/wrf_hydro.exe domain/NWM
```

### Download and Run the Croton_NY test case

```bash
cd $HOME/WRFHYDRO/Downloads
wget -c https://github.com/NCAR/wrf_hydro_nwm_public/releases/download/v5.2.0/croton_NY_training_example_v5.2.tar.gz
tar -xzvf croton_NY_training_example_v5.2.tar.gz
```

### Copy forcing, domain & other configuration files

```bash
cp -r example_case/FORCING $HOME/WRFHYDRO/domain/NWM
cp -r example_case/NWM/DOMAIN $HOME/WRFHYDRO/domain/NWM
cp -r example_case/NWM/RESTART $HOME/WRFHYDRO/domain/NWM
cp -r example_case/NWM/nudgingTimeSliceObs $HOME/WRFHYDRO/domain/NWM
cp -r example_case/NWM/referenceSim $HOME/WRFHYDRO/domain/NWM
cp example_case/NWM/namelist.hrldas $HOME/WRFHYDRO/domain/NWM
cp example_case/NWM/hydro.namelist $HOME/WRFHYDRO/domain/NWM
```

### Run Croton NY Test Case

```bash
cd $HOME/WRFHYDRO/domain/NWM
mpirun -np 2 ./wrf_hydro.exe
ls -lah HYDRO_RST*
echo "IF HYDRO_RST files exist and have data then wrf_hydro.exe sucessful"
```

## Export PATH and LD_LIBRARY_PATH

```bash
cd $HOME

echo "export PATH=$DIR/bin:$PATH" >> ~/.bashrc
echo "export LD_LIBRARY_PATH=$DIR/lib:$LD_LIBRARY_PATH" >> ~/.bashrc
```

## WRF Python

### Installing Miniconda3 to WRF directory

```bash
cd ~

git clone https://github.com/HathewayWill/WRFHYDRO-Standalone-5.2-install-script-linux-32_64bit.git
source WRFHYDRO-Standalone-5.2-install-script-linux-32_64bit/Miniconda3_Install.sh 
source $Miniconda_Install_DIR/etc/profile.d/conda.sh

conda init bash
conda activate base
conda create -n wrf-python -c conda-forge wrf-python
conda activate wrf-python
conda update -n wrf-python --all
conda install -c conda-forge matplotlib
conda install -c conda-forge NETCDF4
```

### Test WRf-Python

```bash
cp $HOME/WRFHYDRO-Standalone-5.2-install-script-linux-32_64bit/SurfaceRunoff.py $HOME/WRFHYDRO/domain/NWM
cd $HOME/WRFHYDRO/domain/NWM

python3 SurfaceRunoff.py
okular SurfaceRunoff.pdf
```

---

> "Congratulations! :star: :tada: You've successfully installed WRFHydro on your personal computer.

---
