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
Each ViaStop is a JSON object with the following Structure:  

    {
      uic: <Integer(mandatory)>,
      status:"BOARDING_ALIGHTING_NECESSARY" (default and may be omitted) | "BOARDING_NOT_NECESSARY" | "ALIGHTING_NOT_NECESSARY" | "BOARDING_ALIGHTING_NOT_NECESSARY",
      transportProducts:[<list of TransportProduct-Category>],
      waittime: <Integer (in min. >=0)>,
      direct: true|false(default),
      couchette: true|false(default),
      sleepingCar: true|false(default)
    }


* transportProducts="All" is default and may be ommited. Be aware that these transport-products will be considered at the start of this via until the next via (if given) or final destination (related to parameter transportProducts between origin and 1. via).
* Possible status are: "BOARDING_ALIGHTING_NECESSARY" (default and may be omitted), "BOARDING_NOT_NECESSARY", "ALIGHTING_NOT_NECESSARY", "BOARDING_ALIGHTING_NOT_NECESSARY"
* Direct has an impact from the Via to the next Via or until destination if no more vias.

Examples:
* `{"value":8507000,"transportProducts":["INTERREGIO"],"waittime":3}`
    
## v3 JSON Request Objects

Be aware:
* Use URL encoding for GET params containing JSON-Objects!

### LineReference
    {
        "productCategoryShortName":"<String>",
        "line":"<String>",
        "number":"<String>",
        "origin":"<ScheduledStopPointReference>",
        "destination":"<ScheduledStopPointReference>",
    }

Hint:
* origin/destination is only relevant for &excludeLines params

Examples:
* `{"productCategoryShortName":"IC","line":"1","number":"753"}`

### OperatorReference
    {
        "type":"OpentransportData"|"PLAIN",
        "value":"<String>",
    }

Hint:
* type=OpentransportData: Operator-numbers (de:Transportunternehmen) according to ['Business organisations' column 'Nummer'](https://opentransportdata.swiss/en/dataset/goch).
* type="PLAIN": add Hafas translation of Operator as given in replies by Journey-Service or any other plain text for e.g. "LEX" or '10____'

Examples:
* `{"OpentransportData":"11"}`
* `{"PLAIN":"Schweizerische Bundesbahnen SBB"}`
* `{"PLAIN":"LEX"}`

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

### PTViaReference
    {
      stopPlaceValue: <StopPlace::id(mandatory)>,
      status: "BOARDING_ALIGHTING_NECESSARY" (default and may be omitted) | "BOARDING_NOT_NECESSARY" | "ALIGHTING_NOT_NECESSARY" | "BOARDING_ALIGHTING_NOT_NECESSARY",
      vehicleModes:[<list of VehicleMode>],
      waittime: <Integer (in min. >=0)>,
      direct: true|false(default),
      couchette: true|false(default),
      sleepingCar: true|false(default)
    }

Examples:
* `{"stopPlaceValue":"8507000"}`
* `{"stopPlaceValue":"8507000","status":"BOARDING_ALIGHTING_NECESSARY","vehicleModes":["rail"],"waittime":3,"direct":true,"couchette":false,"sleepingCar":false}"`

### PTViaNotReference
TODO

### PTNoChangeAtReference
TODO

### ScheduledStopPointReference
    {
        "stopPlaceValue: "<StopPlace::id(mandatory)>",
        "dateTime":"<OffsetDateTime>
    }
    
Examples:
* `{"stopPlaceValue":"8507000","2021-06-14T15:01:00+02:00"}`
