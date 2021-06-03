# J-S API Request Params
Journey-Service introduces by definition some JSON-Objects for single GET params with strongly related aspects.

PRO:
* easier understanding of releated params
* changes are easier to maintain because of String "with outer format"
* 
CONTRA:
* long params
* perhaps more coding effort for Consumers

## v2 JSON Request Objects
### ViaStop
    {
      uic:mandatory Integer,
      status:BOARDING_ALIGHTING_NECESSARY,
      transportProducts:[list of TransportProduct-Category],
      waittime:Integer in min.,
      direct:true|false,
      couchette:true|false,
      sleepingCar:true|false
    }
    
Examples:
* `{"value":8507000,"transportProducts":["INTERREGIO"],"waittime":3}`
    
## v3 JSON Request Objects

Be aware:
* Use URL encoding for GET params containing JSON-Objects!

### LineReference

### LineBlocked

### PlaceReference
    {
      "type":"StopPlace"|"PoiPlace"|"AddressPlace"|"COORDINATES",
      "value":"<AbstractPlace::id>"|"<latitude>,<longitude>"
    }
  
Examples:
* `{"type":"StopPlace","value":"8507000"}`
* `{"type":"AddressPlace","value":"A=2@O=3008 Bern, Effingerstrasse 15@X=7435194@Y=46945679"}`
* `{"type":"COORDINATES","value":"46.948658,7.437406"}`
* `"8507000"` (numeric) is a short convenience form defaulting to "StopPlace

### ScheduledStopPointReference

### ViaReference
    {
      uic:mandatory Integer,
      status:BOARDING_ALIGHTING_NECESSARY,
      transportProducts:[list of TransportProduct-Category],
      waittime:Integer in min.,
      direct:true|false,
      couchette:true|false,
      sleepingCar:true|false
    }
