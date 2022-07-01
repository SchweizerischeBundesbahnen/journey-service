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

#### Plain value

Analog returned `ServiceProduct::name` resp. `::vehicleSubModeShortName`, ::line`, `::number`.

Various content is possible:
* typically either vehicleSubModeShortName AND (line AND/OR number)
* just number

For semantic parsing, specify always all 3 elements, **if one is unknown use '-'**.
Examples:

* `"IC 1 753"`  
* `"IC 1 -"`     // vehicleSubModeShortName - line
* `"IC - 753"`   // vehicleSubModeShortName - line
* `"- - 753"`    // number only, in CH unique (implicites time and direction)

#### JSON object

Hint:
* `name` must be given
*  `ScheduledStopPointReference` start/end is optional but relevant for e.g.:
    * &excludeLines params
    * SOT-PathParam /v3/INCUBATOR/trips/{date}/**{line}**/{destination}

Examples:
* `{"name":"IC 1 753"}`
* `{"name":"IC 1 711","start":{"stopPlaceId":"8501026","dateTime":"2021-06-04T07:32:00+02:00"},"end":{"stopPlaceId":"8506302","dateTime":"2021-06-04T11:35:00+02:00"}}`

### OperatorReference

For **Switzerland**, see managed Transport-Organsiations in **['Business organisations](https://opentransportdata.swiss/en/dataset/goch)**:
* type="NUMBER" corresponds to **column 'NUMMER'**
* type="TEXT" corresponds **column `BEZEICHNUNG_DE`**, be aware other translations are not supported at request time

For international Transport-Organisations there is no such managed list, check and reuse what J-S returns as `Operator::name`.

#### Plain value

Examples:
* `"Schweizerische Bundesbahnen SBB"` as `BEZEICHNUNG_DE` in Business Organisation and defaults to TEXT (and can also be found with "NUMMER=11")
* `"11"` as `NUMMER`or `"000011"` as returned by J-S `Operator::id` defaults to NUMBER CH (J-S will make a lookup itself to access underlying systems)
* `LEX`or `"___10"` defaults to TEXT (for e.g. foreign value, not managed by opendatatransport.swiss)

#### DEPRECATED ~~Explicite JSON Object `OperatorReference`~~

    {
        "type":"TEXT"(default) | "NUMBER",
        "value":"<String>"
    }

Examples:
* `{"type":"NUMBER","value":"11"}`
* `{"type":"TEXT","value":"Schweizerische Bundesbahnen SBB"}`

### PlaceReference

#### Plain value
As given in **Place::id** (`StopPlace::id`, `AddressPlace::id` or `PoiPlace::id`) returned by J-S responses.

Remark:
* in v2 we expected a type as well, but in v3 determines according to id, what Place::type is to be found.

Examples:
* `"8507000"` as returned by StopPlace::id (official station UIC)
* `"A=2@O=3008 Bern, Effingerstrasse 15@X=7435194@Y=46945679"` as returned by J-S `AddressPlace::id` or `PoiPlace::id`

Or **[GeoJSON](https://datatracker.ietf.org/doc/html/rfc7946) `Point`** given _`"[<lon>,<lat>]"`_ in WGS84 decimal-degrees:
* `"[7.43519,46.94567]"` ~Bern, CH


### PTViaReference
    {
      stopPlaceId: <StopPlace::id(mandatory)>,
      status: "BOARDING_ALIGHTING_NECESSARY" (default and may be omitted) | "BOARDING_NOT_NECESSARY" | "ALIGHTING_NOT_NECESSARY" | "BOARDING_ALIGHTING_NOT_NECESSARY",
      vehicleModes:[<list of VehicleMode>],
      waittime: <Integer (in min. >=0)>,
      direct: true|false(default),
      couchette: true|false(default),
      sleepingCar: true|false(default)
    }

Examples:
* `{"stopPlaceId":"8507000"}`
* `{"stopPlaceId":"8507000","status":"BOARDING_ALIGHTING_NECESSARY","vehicleModes":["rail"],"waittime":3,"direct":true,"couchette":false,"sleepingCar":false}"`

### PTViaNoChangeAtReference
TODO

### PTViaNotReference
TODO

### ScheduledStopPointReference
    {
        "stopPlaceId: "<StopPlace::id(mandatory)>",
        "dateTime":"<OffsetDateTime>
    }
    
Examples:
* `{"stopPlaceId":"8507000","2021-06-14T15:01:00+02:00"}`

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
