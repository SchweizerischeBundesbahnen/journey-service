# J-S API Request Params
Journey-Service introduces by definition some JSON-Objects for single GET params with strongly related aspects.

PRO:
* easier understanding of releated params
* changes are easier to maintain because of String "with outer format"
* 
CONTRA:
* long params -> leads rather to POST 
* perhaps more coding effort for Consumers
    
## v3 JSON Request Objects

Be aware:
* Use URL encoding for GET params containing JSON-Objects!

### ServiceProductReference
Goal: find [0..*] DatedVehicleJourney's matching the given ServiceProduct.
* the better specified the more accurate the search

#### Plain value

Analog to the returned `ServiceProduct::name` resp. its `vehicleSubModeShortName`, `line`, `number`.

Various content is possible:
* at least specify {`number`} OR {`vehicleSubModeShortName` AND `line`}

For semantic parsing, specify always all 3 elements, **if one is unknown use '-'**.  

Examples:

* `"IC 1 753"`   // vehicleSubModeShortName line  number
* `"IC 1 -"`     // vehicleSubModeShortName line (number missing)
* `"IC - 753"`   // vehicleSubModeShortName (line missing) number
* `"- - 753"`    // number only, in CH unique (implicit time and direction)

### DatedVehicleJourneyReference
Goal: find concrete DatedVehicleJourney by ServiceProduct and first and last ScheduledStopPoint -> should lead to [0..*] hits 

#### JSON object

Hint:
* `name` must be given precisely analog [**ServiceProductReference**](#serviceproductreference)
* `start`, `end` analog [**ScheduledStopPointReference**](#scheduledstoppointreference) and mandatory

Examples:
* `{"name":"IC 1 711","start":{"stopPlaceId":"8501026","dateTime":"2021-06-04T07:32:00+02:00"},"end":{"stopPlaceId":"8506302","dateTime":"2021-06-04T11:35:00+02:00"}}`

### OperatorReference

For **Switzerland**, see managed Transport-Organsiations in **['Business organisations](https://opentransportdata.swiss/en/dataset/goch)**:
* type="NUMBER" corresponds to **column 'NUMMER'**
* type="TEXT" corresponds **column `BEZEICHNUNG_DE`**, be aware other translations are not supported at request time

For international Transport-Organisations there is no such managed list, check and reuse whatever J-S returns as `Operator::name`.

#### Plain value

Examples:
* `"Schweizerische Bundesbahnen SBB"` as `BEZEICHNUNG_DE` in Business Organisation and defaults to TEXT (and can also be found with "NUMMER=11")
* `"11"` as `NUMMER`or `"000011"` as returned by J-S `Operator::id` defaults to NUMBER CH (J-S will make a lookup itself to access underlying systems)
* `LEX`or `"___10"` defaults to TEXT (for e.g. foreign value, not managed by opendatatransport.swiss)

#### DEPRECATED ~~Explicite JSON Object `OperatorReference`~~

    {"type":"TEXT"(default) | "NUMBER","value":"<String>"}

~~Examples:~~
* ~~`{"type":"NUMBER","value":"11"}`~~
* ~~`{"type":"TEXT","value":"Schweizerische Bundesbahnen SBB"}`~~

### PlaceReference

#### Plain value
As given in **Place::id** (`StopPlace::id`, `Address::id` or `PointOfInterest::id`) returned by J-S responses.

Remark:
* in v2 we expected a type as well, but in v3 determines according to id, what Place::type is to be found.

Examples:
* `"8507000"` as returned by StopPlace::id (official station UIC)
* `"A=2@O=3008 Bern, Effingerstrasse 15@X=7435194@Y=46945679"` as returned by J-S `AddressPlace::id` or `PoiPlace::id`

Or **[GeoJSON](https://datatracker.ietf.org/doc/html/rfc7946) `Point`** given _`"[<lon>,<lat>]"`_ in WGS84 decimal-degrees:
* `"[7.43519,46.94567]"` ~Bern, CH


### PTViaReference
Optional routing points to be included or avoided for trip requests as a JSON Object parameter:

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
    {
      stopPlaceId: <StopPlace::id(mandatory)>,
      status: "NO_TRANSFER_META_STOPPLACE" (default and may be omitted) | "NO_TRANSFER"
    }
    
Examples:
* `{"stopPlaceId":"8507000"}`
* `{"stopPlaceId":"8507000","status":"NO_TRANSFER"}`

### PTViaNotReference
    {
      stopPlaceId: <StopPlace::id(mandatory)>,
      status: "NO_PASS_THROUGH_META_STOPPLACE" (default and may be omitted) | "NO_PASS_THROUGH"
    }

Examples:
* `{"stopPlaceId":"8507000"}`
* `{"stopPlaceId":"8507000","status":"NO_PASS_THROUGH_META_STOPPLACE"}`


### ScheduledStopPointReference
    {
        "stopPlaceId: "<StopPlace::id(mandatory)>",
        "dateTime":"<OffsetDateTime>
    }
    
Examples:
* `{"stopPlaceId":"8507000","2021-06-14T15:01:00+02:00"}`

## v3 JSON Response objects

## LinkedText

`Notice::text` for e.g. contains a special formattable `LinkedText` object to make some values like PHONE, EMAIL, URL interactable with UI capabilities, for e.g. like:

    // LinkableType = {PHONE, EMIAL, URL} where arguments are entries for each LinkedType in proper order matching the template
    public static String formatLinkedText(String template, Map<LinkableType, List<String>> arguments) {
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
