### Defining the model domain and initial conditions using the WRF Preprocessing System (WPS)

#### Software and input data

- WPS built with the GNU compilers
- WRF built with the GNU compilers
- WPS geographical input data 

**Step 1: Setup the run wps directory**

```bash
mkdir -p $wrfhome/run_wps/
wps_dir=/scratch/WRF_
```

**Step 2: View the namelist file prepared for Dire Dawa region**

```bash
mkdir -p $wrfhome/run_wps/
cd $wrfhome/run_wps/
cp /scratch/WRF_HYDRO_SHARED/namelist.wps .
cat namelist.wps
```

**Table 1. WPS namelist options relevent to WRF-Hydro**

|Option|Description|
|------|-----------|
| e_we | The number of grid edges in the west-east dimension. The number of grid centers will be e_we-1. |
| e_sn | The number of grid edges in the south-north dimension. The number of grid centers will be e_sn-1. |
| ref_lat | A real value specifying the latitude part of a (latitude, longitude) center-point of the domain. South latitudes are negative, and the value of ref_lat should be in the range [-90, 90]. |
| ref_lon | A real value specifying the longitude part of a (latitude, longitude) center-point of the domain. West longitudes are negative, and the value of ref_lon should be in the range [-180, 180]. |
| dx | A real value specifying the grid distance in the x-direction where the map scale factor is 1. The grid distance is in meters for the 'polar', 'lambert', and 'mercator' projection, and in degrees longitude for the 'lat-lon' projection. |
| dy | A real value specifying the grid distance in the y-direction where the map scale factor is 1. The grid distance is in meters for the 'polar', 'lambert', and 'mercator' projection, and in degrees latitude for the 'lat-lon' projection. |
| map_proj | A character string specifying the projection of the simulation domain. Accepted projections are 'lambert', 'polar', 'mercator', and 'lat-lon'. Default value is 'lambert'. |
| truelat1 | A real value specifying, the first true latitude for the Lambert conformal conic projection, or the only true latitude for the Mercator and polar stereographic projections. |
| truelat2 | A real value specifying, the second true latitude for the Lambert conformal conic projection. For all other projections, truelat2 is ignored. No default value. |
| stand_lon | A real value specifying, the longitude that is parallel with the y-axis in the Lambert conformal and polar stereographic projections. For the regular latitude-longitude projection, this value gives the rotation about the earth's geographic poles. No default value. |
| geog_data_res | A character string specifying a corresponding resolution or list of resolutions separated by + symbols of source data to be used when interpolating static terrestrial data to the grid. This string should contain a resolution matching a string preceding a colon in a rel_path or abs_path specification (see the description of GEOGRID.TBL options) in the GEOGRID.TBL file for each field. If a resolution in the string does not match any such string in a rel_path or abs_path specification for a field in GEOGRID.TBL, a default resolution of data for that field, if one is specified, will be used. If multiple resolutions match, the first resolution to match a string in a rel_path or abs_path specification in the GEOGRID.TBL file will be used. Default value is 'default'. |
| geog_data_path | A character string giving the path, either relative or absolute, to the directory where the geographical data directories may be found. This path is the one to which rel_path specifications in the GEOGRID.TBL file are given in relation to. No default value. |

**Step 3 copy or make a link to parameters and executable**
```bash
ln -s $wps_dir/geogrid.exe  .
ln -s $wps_dir/geogrid .

ln -s ${wps_dir}/ungrib.exe  .
ln -s ${wps_dir}/metgrid.exe  .

cp  ${wps_dir}/link_grib.csh .

ln -sf  ${wps_dir}/metgrid  .
#./link_grib.csh  /data/Build_WRF/DATA/fnl_data_2006/

```
Before running geogrid, make sure that you have your geog_data_path set to the location where you put your geography static data. 

**Step 4 Run geogrid**

```bash
./geogrid.exe 
```
geo_em* file for each domain will be created if the run is successful


**Step 5 link in the input GFS data**

```bash
./link_grib.csh path_where_you_placed_GFS_files
```
**Step 6 Then link to the correct Vtable (GFS, for this case)**

```bash
ln -sf ${wps_dir}/ungrib/Variable_Tables/Vtable.GFS Vtable
```
**Step 7 run the ungrib executable**
```bash
./ungrib.exe
You should now have files with the prefix "FILE" (or if you named them something else, they should have that prefix)
```
**Step 8 You are now ready to run metgrid*
```bash
./metgrid.exe 
```
You should now have files with the prefix met_em* for each of the time periods for which you are running.


**Step 9 Setup the run wrf simulation directory**

```bash
mkdir -p $wrfhome/run_wrf/
src_wrf=/scratch/WRF

cd $wrfhome/run_wrf
cp -RL $src_wrf/run/*  .
cp -RL $src_wrf/hydro/template/HYDRO/*TBL . 
ln -sf $wrfhome/run_wps/met_em*  . 
ln -sf $wrfhome/run_wps/geo_em*  .
```
**Step 10 Running WRF**

```bash
mpirun -np 1 ./real.exe
```
**Step 11 Check the end of your "rsl" files to make sure the run was successful**

```bash
tail rsl.error.0000
```
