## Generate raw data from qGIS
1. Obtain latest Fabric data.
- Locate email with link to latest data
2. Generate Point Data
3. Import into QGIS (Layer -> Add Layer -> Add Delimited Text Layer or Shift + Command + T)
- 	verify x and y axis are correct (x longitude, y latitude) 
- 	verify Geometry CRS is ERSG: 4326 - WDS 84
- 	Click Add
4. Verify serviceable area polygon is still accurate.
5. Select points within polygon (Processing -> Toolbox, Search for “Select by location”)
- 	Select features from: fabric layer
- 	Where the features: intersect
- 	By comparing to the features from: serviceable area layer
- 	Leave everything else as default
- 	Click Run
6. Right click fabric layer (points) and select Export -> Save Selected Features As
-	 Format CSV
-  Select file name and location
- 	Save

## Generate Availability Data File
1. Create new csv file
```
echo "provider_id,brand_name,location_id,technology,max_advertised_download_speed,max_advertised_upload_speed,low_latency,business_residential_code" > $BDC_FILE
```
2. Run the following to process residential customers
```
grep ',R,' $EXPORTED_AVAILABILITY_FILE | awk -F "," '{print 430035", Foster Communications LLC, "$1", "70", "25", "5", "1", "$9}' | sed 's/"//g' >> $BDC_FILE
```
3. Run the following to process business customers
```grep ',B,' $EXPORTED_AVAILABILITY_FILE | awk -F "," '{print 430035", Foster Communications LLC, "$1", "70", "25", "25", "1", "$9}' | sed 's/"//g' >> $BDC_FILE
grep ',X,' $EXPORTED_AVAILABILITY_FILE | awk -F "," '{print 430035", Foster Communications LLC, "$1", "70", "25", "25", "1", "$9}' | sed 's/"//g' >> $BDC_FILE
```

4. Inspect file for accuracy
  - Submit file and verify no processing errors
  - If errors are found (such as business_residential_code: B is not valid) try removing and replacing the last few characters of the file and resubmit
Fix:  
```
sed 's/, /,/g' $BDC_FILE > $BDC_FILE
```
