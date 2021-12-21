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
        "productCategoryShortName":"<String(mandatory)>",
        "line":"<String>",
        "number":"<String>",
        "origin":"<ScheduledStopPointReference>",
        "destination":"<ScheduledStopPointReference>"
    }

Hint:
* line and/or number must be given
*  origin/destination is relevant for
    * &excludeLines params
    * SOT-PathParam /v3/INCUBATOR/trips/{date}/**{line}**/{destination}

Examples:
* `"IC-1-753"` // 
* `"IC-1"`   // productCategoryShort - line
* `"--753"`  // number only, in CH unique (implicites time and direction)
*  `{"productCategoryShortName":"IC","line":"1","number":"753"}`
* `{"productCategoryShortName":"IC","line":"1","number":"711","start":{"stopPlaceValue":"8501026","dateTime":"2021-06-04T07:32:00+02:00"},"end":{"stopPlaceValue":"8506302","dateTime":"2021-06-04T11:35:00+02:00"}}`

### OperatorReference
    {
        "type":"TEXT"(default) | "NUMBER",
        "value":"<String>"
    }

Hint:
* type="NUMBER": Operator-number (de:Transportunternehmen) according to ['Business organisations' column 'Nummer'](https://opentransportdata.swiss/en/dataset/goch).
* type="TEXT": add ['Business organisations' column 'BEZEICHNUNG_DE'](https://opentransportdata.swiss/en/dataset/goch) or Hafas translation of Operator::name as given in replies  by Journey-Service or any other foreign plain text for e.g. "LEX" (which are not defined in opentransportdata.swiss)

Examples:
* `{"type":"NUMBER","value":"11"}`
* `{"type":"TEXT","value":"Schweizerische Bundesbahnen SBB"}`
* `"LEX"` or `"Schweizerische Bundesbahnen SBB"` defaults to TEXT
* **`"11"` or `"000011"`** defaults to NUMBER CH (in this case SBB)
* `"___10"` defaults to TEXT (for e.g. foreign value, not managed by opendatatransport.swiss)

### PlaceReference

#### Plain value
As given in **AbstractPlace::id** (`StopPlace::id`, `AddressPlace::id` or `PoiPlace::id`) returned by J-S responses:

Examples:
* `"8507000"` (official station UIC)
* `"A=2@O=3008 Bern, Effingerstrasse 15@X=7435194@Y=46945679"` as returned by J-S `AddressPlace::id` or `PoiPlace::id`

Or **GeoJSON `Point`** given in WGS84 decimal-degrees "[<lon>, <lat>]"
* `"[7.435194,46.945679]"` ~Bern, CH

#### ~~Explicite JSON Object "PlaceReference"~~
    ~~{
      "type":"StopPlace"|"PoiPlace"|"AddressPlace"|"COORDINATES",
      "value":"<AbstractPlace::id>"|"<longitude>,<latitude>"
    }~~
    
~~Remark:
* For COORDINATES **GeoJSON lon/lat order** is evaluated (unfortunately vice versa of J-S v2!).
* type "StopPlace" is default, specification is optional
  
Examples:
* `{"type":"StopPlace","value":"8507000"}`
* `{"value":"8507000"}` will be interpreted as StopPlace
* `{"type":"AddressPlace","value":"A=2@O=3008 Bern, Effingerstrasse 15@X=7435194@Y=46945679"}`
* `{"type":"PoiPlace","value":"A=4@O=Pontresina, Polizei@X=9904773@Y=46489423@U=104@L=980045242@B=1@p=1603115030@"}`
* `{"type":"COORDINATES","value":"7.437406,46.948658"}`~~

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

## Notice

::text (template/arguments) contains a special formattable object to make some values like PHONE, EMAIL, URL linkable with UI capabilities, for e.g. like:

    public static String formatLinkedText(String template, Map<NoteValue.LinkableType, List<String>> arguments) {
        if (StringUtils.isBlank(template) || CollectionUtils.isEmpty(arguments)) {
            return template;
        }

        for (Map.Entry<LinkableType, List<String>> entry : arguments.entrySet()) {
            for (String arg : entry.getValue()) {
                template = template.replaceFirst(entry.getKey().name(), arg);
            }
        }
        return template;
    }
