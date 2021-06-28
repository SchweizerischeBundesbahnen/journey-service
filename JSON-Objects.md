# J-S API Request Params
Journey-Service introduces by definition some JSON-Objects for single GET params with strongly related aspects.

PRO:
* easier understanding of releated params
* changes are easier to maintain because of String "with outer format"
* 
CONTRA:
* long params -> leads rather to POST 
* perhaps more coding effort for Consumers

## v2 JSON Request Objects
### CarReference
    {
      "type":"UIC"|"BEACON",
      "value":"<carUic>"|"<Beacon.MajorId>"
    }
    
  
Examples:
* `94 85 123 123 - 2` or `94851231232` is a short convenience form defaulting to type="UIC", variations with or without whitespaces and hyphen
* `{"type":"UIC","value":"94 85 123 123 - 2"}` or value like "94851231232"
* `{"type":"BEACON","value":"15011"}`

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
* origin/destination is relevant for
    * &excludeLines params
    * SOT-PathParam /v3/INCUBATOR/trips/{date}/**{line}**/{destination}

Examples:
* `{"productCategoryShortName":"IC","line":"1","number":"753"}`
* `{"productCategoryShortName":"IC","line":"1","number":"711","start":{"stopPlaceValue":"8501026","dateTime":"2021-06-04T07:32:00+02:00"},"end":{"stopPlaceValue":"8506302","dateTime":"2021-06-04T11:35:00+02:00"}}`

### OperatorReference
    {
        "type":"PLAIN"(default) | "OpentransportData",
        "value":"<String>",
    }

Hint:
* type="OpentransportData": Operator-numbers (de:Transportunternehmen) according to ['Business organisations' column 'Nummer'](https://opentransportdata.swiss/en/dataset/goch).
* type="PLAIN": add Hafas translation of Operator as given in replies by Journey-Service or any other plain text for e.g. "LEX"

Examples:
* `{"type":"OpentransportData","value":"11"}`
* `{"type":"PLAIN","value":"Schweizerische Bundesbahnen SBB"}`
* `"LEX"` defaults to PLAIN

### PlaceReference
    {
      "type":"StopPlace"|"PoiPlace"|"AddressPlace"|"COORDINATES",
      "value":"<AbstractPlace::id>"|"<longitude>,<latitude>"
    }
    
Remark:
* For COORDINATES GeoJSON lon/lat order is evaluated (vice versa of J-S v2!).
* type "StopPlace" is default, specificatin is optional
  
Examples:
* `{"type":"StopPlace","value":"8507000"}`
* `{"value":"8507000"}` will be interpreted as StopPlace
* `{"type":"AddressPlace","value":"A=2@O=3008 Bern, Effingerstrasse 15@X=7435194@Y=46945679"}`
* `{"type":"COORDINATES","value":"7.437406,46.948658"}`
* `"8507000"` (numeric) is a short convenience form defaulting to type="StopPlace"

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

# J-S API Response Params
## J-S  v2

## J-S v3

Notice::text contains a special Object
