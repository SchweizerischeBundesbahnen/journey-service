# J-S API
## v3 JSON Request Objects

Be aware:
* Use URL encoding for GET params containing JSON-Objects!

### LineReference

### LineBlocked

### PlaceReference
`{
  "type":"StopPlace"|"PoiPlace"|"AddressPlace"|"COORDINATES",
  "value":"<AbstractPlace::id>"|"<latitude>,<longitude>"
}`
  
Examples:
* {"type":"StopPlace","value":"8507000"}
* {"type":"AddressPlace","value":"A=2@O=3008 Bern, Effingerstrasse 15@X=7435194@Y=46945679"}
* {"type":"COORDINATES","value":"46.948658,7.437406"}
* "8507000" (numeric) is a short convenience form defaulting to "StopPlace

### ScheduledStopPointReference

### ViaReference
