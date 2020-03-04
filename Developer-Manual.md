
**SBB internal users:** temporarily use https://confluence-ext.sbb.ch/display/JAD/J-S%3A%3AB2C+Developer+Manual for better readablibity (FPLJS-369 work in progress)

# Journey-Service::B2C Developer Manual
Support: journey-service@sbb.ch

## Abstract
The Journey-Service is a RESTful facade abstracting public transportation routing and planning for a set of underlying systems, such as Hafas, HIM, CUS, INFO+, DiDok, FOS, PLABE, OccupancyPrognosis.

The main use cases are:

Finding locations, such as Stations (trains, busses, tramways, ..), Addresses and Points of Interest (POI).
Finding concrete journeys (Trip's) for a traveling passenger
Finding concrete Transport-Product journeys
Providing details about Trainformations, sections, wagons and their occupancy
A set of implemented Business Rules (such as delays, platform changes,..) guarantees that consuming channels may display consistent data.

Lots of convenient utilities provide easy interpretable models.

Journey-Service logic and backend interfering is heavily based on the library Journey-Assistant (J-A), therefore J-A documentation or even J-A Javadoc might be helpful as well, if you need to dig in deeper.

If you are new to journey-planning with SBB, the OpenJourneyPlanner Standard might give you a broader picture, what commonly is understood by this context.

## URL
We currently support 4 environments, where DEV and TEST are available for SBB internal consumers only. INT and PROD are accessable by APIM for www consumers as well.

Important:

Data per environment is completely detached from other environment, by means results on DEV, TEST, INT and PROD may differ (like different routings, stations, translations, accessibility infos, ..) and are therefore not comparable among 2 environments.



J-S::B2C

DEV:

https://ki-journey-service-dev.app.ose.sbb-cloud.net/swagger-ui.html

TEST:

https://ki-journey-service-test.app.ose.sbb-cloud.net/swagger-ui.html#/sbb-trip-controller/getTripsUsingPOST

INT:

neutrale Route: https://ki-journey-service-int.app.ose.sbb-cloud.net/swagger-ui.html#/
green: https://ki-journey-service-int-green.app.ose.sbb-cloud.net/swagger-ui.html#/
blue: https://ki-journey-service-int-blue.app.ose.sbb-cloud.net/swagger-ui.html#/
PROD:

neutrale Route: https://ki-journey-service.app.ose.sbb-cloud.net/swagger-ui.html
green: https://ki-journey-service-green.app.ose.sbb-cloud.net/swagger-ui.html
blue: https://ki-journey-service-blue.app.ose.sbb-cloud.net/swagger-ui.html
DEV: <N.A>

TEST: <N.A>

INT: https://sso-int.sbb.ch resp. https://developer-int.sbb.ch/api/76/journey-service

PROD: https://sso.sbb.ch resp. https://sso.sbb.chhttps://developer.sbb.ch/api/33/journey-service



Be aware:

INT and PROD is secured by APIM /pages/viewpage.action?spaceKey=AITG&title=Getting+started+with+APIM
Not all described APIs below are available by API-Mgmt, but might be restricted to SBB internal access only.
API-Resources might by executed by SwaggerFox-UI by browser (external consumers must go through the enabling process J-S: Consumer Registration SBB API-Management)

### Backward compatibility
We live different concepts to maintain backward compatibility:

#### URL versioning
All /b2c/v2/* APIs without "INCUBATOR" in their URL may be considered as stable. Any breaking changes will be signaled by our Release-Manager (to the e-Mail you registered at APIM registration process)!

Any APIs with "INCUBATOR" in their URL are highly @Experimental and might be changed on a daily basis (if you use it, make sure you keep frequent communication with the journey-service@sbb.ch) or might even be deleted without further notice.

#### Deprecated stuff
Any APIs to be removed soon, will have a @Deprecated  annotation and will be easily visible in the Swagger-UI (check any migration hints given and migrate as soon as possible).

Some properties in response models might have a "description" like "@Deprecated use ... instead". Unfortunately such marks are not optically supported by Swagger and you have to go through each model-property description carefully.

Remark:
* such deprecated code will be removed in future versions finally

### Testing your APIM access
Please check our Testing hints: https://sbb.sharepoint.com/:w:/r/teams/297/Oeffentlich/S3_Programm/Anwendungen/Oeffentlich/KIP/Journey-Service/Testing/Test%20your%20access.docx?d=wbe3f5fa01f2b43c495c1b7cf61920f1a&csf=1&e=OjRFQb

## journey-service-client
Be aware that the J-S::B2C Team provides a generated  response-model and an ApiClient to perform requestsfor convenience reasons

GIT/Artifactory dependency: https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-client
A showcase demonstrates its usage with minimal developing effort: https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-integration/src/test/java/ch/sbb/ki/journeyservice/showcase/client


<dependency>
    <groupId>ch.sbb.ki.journey-service</groupId>
    <artifactId>journey-service-client</artifactId>
    <version>${journey-service.version}</version>
</dependency>


However, if the provided client does not work for you (for e.g. wrong Spring version, ..) you may generate it yourself according to the json-definitions related to the given contract.

However the generated client depends on the Spring, SpringFox, Swagger-Annotation versions used by J-S, see properties in https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-client/pom.xml

If you want to create your own client-version, you might copy our approach:

create a TestCase instantiating J-S and download the swagger-json-definition, for e.g. like 
https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-boot/src/test/java/ch/sbb/ki/journeyservice/web/SwaggerDefinitionModelGeneratorTest.java

generate client by swagger-codegen-maven-plugin, for e.g. like https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-client/pom.xml

Be aware of known bugs we fixed:

OffsetDateTime problem: https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/commits/312479191d4d500922a533fbdd4e357d7d139e21
Encoding https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/commits/b7934e8c54ef2c83b7a9a8d03d38ff0a78697b4d

## API Doc
All Services (short abstract, parameters, models) are documented directly by swagger-annotations, therefore the documentation below is reduced to the max and is hopefully not really necessary for v2 APIs in most cases.

### Choosing the right service definition
Select the version of J-S REST-API: currently v2

Remark:

if accessing by APIM, some APIs may not be visible or accessable, especially those who are meant for Intranet SBB users only.


API-Path	
/b2c/v2/* 

Remark:

some APIs might have "INCUBATOR" in the URL →  indicates ongoing (sometimes breaking) changes and design aspects, the final API without "INCUBATOR" might not have.
Request-Header	
"Accept-Language" → de, fr, it, en (SBB supports those 4 languages currently)

For e.g. Trip-ScrollContextForward/Backward

"Log-Context" logs technical traces into Sematext by J-S und Splunk by underlying Hafas (set your traceId if relevant for you)

Request-Params	
GET ?param1=..&param2=..

return ResponseEntity<List<?>>

Known Problems:

OffsetDateTime encoding, see  https://github.com/spring-projects/spring-framework/issues/21577
400: ..?dateTime=2019-04-23T14:56:14+00:00
OK: /b2c/v2/departures?originUIC=8503000&dateTime=2019-04-27T14%3A50%3A37.375%2B02%3A00
Response-Header	
"Content-Language" (relates to "Accept-Language" or fallback-language)

For e.g. Trip → "ScrollContext"

"Log-Context" relates to your given "Log-Context" in appropriate request

Response-Body	
200 → returns List<Model>

204 → returns "{}"

4xx/5xx → returns error JSON, s. https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-b2c/V2_Error-Handling.md

journey-service-client	
jar in Artifactory or

download http://<ENVIRONMENT>/v2/api-docs?group=journey-service-api-2.0 and generate yourself

arg defaulting	better defaulting (for minimal performance)
Realtime handling	less but well calculated attributes → consumer needs to analyze much less or nothing at all


Remark about v1:

 /api/sbb/v1/extXmlCrutch is for dedicated SBB internal use only

### End-user consistency (de:KI)
According to SBB KI strategy (de:Kundeninformation) it is very important to communicate consistent travel information to all our passengers on any end-user API resp. -UI.

J-S works hard to provide such consistent end-user information and therefore J-S consumers must be aware that they might violate this SBB top strategy by ignoring or redefining relevant values and impact end-user or public media critics.

Some expressions are even related to conventions by BAV, SBB Infrastructure, SBB Personenverkehr or public transport in general.

Further details s. chapters: 

S::B2CDeveloperManual-Businessdataaspects
S::B2CDeveloperManual-Businesslogicaspects
S::B2CDeveloperManual-Error-handling

### Request
For each Request to J-S set the header-fields:

WSG-Credentials":
for SBB Intranet acces → "FUserId:FUserPwd" (FUser for WSG,  SBB Users ask xfpl001@sbb.ch)
for APIM 3rd Party access →  see J-S::B2C Consumer Registration SBB API-Management
Optional: "Log-Context":"mySematext/Splunk logText" → useful to log your Use Case or transaction-Id if you have (will be returned appropriately in v2 response header field)

### Response
#### Error-handling
See Swagger API doc concerning known HttpStatus (for e.g. 400 BAD_REQUEST)

##### /v2/*
###### Error body
In most cases the body will contain a detailed error, according to https://tools.ietf.org/html/rfc7807:
* Header: HttpStatus resp. high-level error class
* Body: finer-grained details of the problem (machine-readable format, the client can treat it appropriately) ** section 3.1 declares members of a Problem Details (problem+json) Object: status, type, title, detail, instance
Error texts in "title" or "detail" will be given in english (en) only and:
* are not meant to display to end-users 1:1, proper error handling and displaying is the responsibility of the UI developer
* can be used for consumer logging and might be helpful in postponed analysis tasks
* the contents of such errors relate to underlying system and what J-S thinks is appropriate from the viewpoint of its layer


For example, an HTTP response carrying JSON problem details:
HTTP/1.1 204 Not Found
Content-Type: application/problem+json
Content-Language: en
Log-Context: <your value replied>
 
{
 "type": "https://ki-journey-service.app.ose.sbb-cloud.net/sbb/v2/trips",
 "title": "No hits",
 "detail": "There were no trips found for your query arguments.",
 "instance": "/v2/trips"
}

###### 200 for emptyList, 404 for not found Object?
v2 is based completely on HTTP GET, therefore various variants are technically possible to signal "No hits found"!

According to https://medium.com/@santhoshkumarkrishna/http-get-rest-api-no-content-404-vs-204-vs-200-6dd869e3af1d the J-S team thinks it is best to return:

200 with an emptyList body "{}" for API's returning List<T> where no hits were found
404 with an optional Error body as described above if an expected object cannot be found, for e.g. /v2/trips/{reconstructionContext} which may not resolve
400
Swagger annotations are heavily used to validate the API. In such cases no error-body is returned mostly. Please check the Swagger-UI carefully.

#### Business data aspects
Some properties resp. their value-expressions might be translated according to requested "Accept-language" to german (de), french (fr), italian (it) and english (en) for e.g.:
* Station-Names in request accept all 4 languages, though the reply (StopV2::name) contains only the local Switzerland translation as a special case (Geneva → Genève)
* Note::value is sometimes translated by SBB P Data-Mgmt
* Translations with a standard and short-translation:
 * TransportProductV2::trackTranslation as a SBB-KI standard text
 * TransportProductV2::trackTranslationShort with an appropriate abbreviation if available
* other texts are translated by SBB BR, like StopV2::*DelayText

J-S uses some Enum's which relate typically to business defined values, for e.g.:
* TransportProductV2::category
 * relates to SBB P Data-Mgmt (config-file "Zugart") and refers to 10 classes from 0..9


There are also specific extensions for developer convenience, such as:
* TransportProductV2::vehicleIconName showing the appropriate name in SBB Corporate-Identity resources (though the resource itself must be allocated by the consumer)
 * the developer is still free whether he prefers its own mapping from TransportProductV2::vehicleType or uses the J-A given short cut to ::vehicleIconName

#### Business logic aspects
##### Formatted fields
J-S sometimes provides fields with a "*Formatted" suffix which contain values, that must be showed to public end-users. Alternatively the very same field might be given without the suffix for SBB internal usage only, for e.g.

TransportProductV2::number → B2E only: meant for SBB internal services or employees (for e.g. to display in SBB Casa to be seen by SBB employees only)
TransportProductV2::numberFormatted → B2C or B2P: BR impacted value for end-users (for e.g. to display in SBB Webshop)

##### Realtime analysis
StopV2 contains pre-calculated fields to inform about relevant realtime status of a TransportProduct at a specific StopV2:
* ::boardingAlightingStatus
* ::stopStatus
* About any *Rt properties:
 * Ideally these fields are always null, means transport organisations are operating as planned
 * If any vehicle (TransportProduct) is not operating according to its scheduled plan, *Rt fields may contain correcting values here and there (availability usually max 2h in the future and may disappear quickly in the past, because irrelevant for the current instant in time)
 * Journey-Service does not know the exact position of a vehicle yet and does not even guarantee that a vehicle has passed a station in reality. (However we have stories to transmit such additional info in the near future.)
 * *Rt fields may update their values for the same trip or journey if repeatedly requested, since they express “real-time” behaviour. (However do update your query as less as possible, for performance reasons.)
 * If the *Rt fields are empty, just use the corresponding (same name) fields without “Rt” suffix for properly planned values

Remark:
* based on /display/FAHRPLAN/Haltestellen-Status

### Most typical use case
Most consumers will probably be interested in a simple train-connection from A to B. This scenario is supported in 2 steps usually:

use /b2c/v2/locations to find A=origin (start) and B=destination (end) Location's → any Location hit with type=STATION will have a concrete UIC standardized ID as given in the "uicOrId" field (for e.g. 8507000 for "Bern").
use /b2c/v2/trips to find a set of trips (usually between 4..7 hits) and interprete the best case in your case. The reponse TripV2 object will contain Leg's (segments operated by a specific transport-company (for e.g. "SBB") and a list of Stop's where A=stops[0] and B=stops[last], each Stop has various data such as departureDateTime, stopStatus, ...

### Info API
Concepts:
* The timetables for all public transportation products are renewed on a yearly base (de: Fahrplanwechsel). There is typically a change about 1st decade of December.

Remark:
* For most users this service will not be relevant. J-S will navigate in the current pool automatically or throw errors if your queries do not match the current period.
* Provides data-pool infos

### /v2/info (GET)
Returns "PoolInfo":

declaring the current service-days period (usually current year between first decade of december)
STATION data-pools

### /v2/traffic
Allows search for active HIM-Messages about disruptions, construction sites, events,..

Be aware:
* HimMessageV2 objects related to concrete journeys are returned by /v2/trips in LegV2::messages or JourneyDetail::himMessages directly

### Locations API
Useful to query for locations (stations, addresses, POIs).

Concepts:
* Switzerland uses UIC codes for station IDs, see J-A: Haltestellen
* Addresses are postal addresses imported from Swiss POST in regular phases.
* Points of Interest (POI) are licenced data such as Museums, Shopping mall etc.

#### /v2/locations (GET)
Returns a non-inherited structure of a Location. Discriminator Location::type

STATION (known Stop, routed by Hafas routes or trips)
ADDRESS (postal addresses)
POI (points of interest, like Museum, ..)

### Journey-Planning services
There are various ways to query for vehicle (transport-product) routes or tailored passenger trips from A to B.

Concepts:
* All journeys rely on a yearly plan (de:Soll-Fahrplan).
* However in reality not all transport-products may be operated in time, therefore realtime changes happen to the plan, see J-A: Trip-Analyse
* Trip's are tailored traveling routes for a specific passenger. 
 * Each Leg means a separate transport-product (by means passengers need to change at the ending Stop of a Leg).
 * The underlying system provides a default change time when passengers need to switch transport-products. However these values may be customized as well.
* JourneyDetails cover the whole scope of a single transport-product. They might be identified by the JourneyReference (which is not a physical vehicle-ID).
* Time aspects:
 * The underlying system handles timetables in Swiss timezone "Europe/Zurich". 
 * J-S supports other timezones in requests, however they will be transformed to CH local time and replied in UTC. 
Seconds are irrelevant.

#### Stationboard API
##### /v2/departures (GET)
Departureboard (de:Abfahrtstabelle)

DeapartureBoard
Falls nur abfahrende Fahrzeuge mit einer bestimmten Richtung gewünscht sind:
 
  "directionUIC": 8508005
 
 
"dateTime" Feld kann man angeben, wenn man nicht den aktuellen Zeitpunkt anfragen will:
 
 
In der products Liste können gewünschte Kategorien gefiltert werden.
 
 
Weitere Felder:
- "duration": 30, "limit": 10 (beschränken Interval und Anzahl Ergebnisse)
- "filterEquivalentStops": false|true (Filter für als equivalent bezeichnete Halte)
- "resolveDirectionUIC" : Boolean @deprecated (nur false)

##### /v2/arrivals (GET)
Arrivalboard (de:Ankunftstabelle)

Trips API
Journey-Planning for travelling passengers.

de:Verbindungen

#### /v2/trips (GET)
Für "stopBehaviour" sind folgende Werte möglich
* "ORIGIN_DESTINATION_ONLY" -> liefert nur Abgangs- und Ankunftsort
* "REAL_BOARDING_ALIGHTING" -> liefert zusätzlich alle Zwischenhalte wo man tatsächlich ein - und aussteigen kann
* "ALL_BOARDING_ALIGHTING" -> liefert zusätzlich alle Zwischenhalte inkl. den virtuellen wie "Bahn-2000-Strecke"
 
Auf Station's und Leg's kann es Note's geben die bspw. auf Leg::attributes und Leg::infos resultieren.
* für ATTRIBUTE kann eingeschränkt werden (Use Case Casa), z.B. "attributes":["GROUPS_ADMITTED"]
* für INFOTEXTS kann zwar nicht aktiv gesucht werden, aber im nachhinein die Antwort gefiltert werden (Use Case Kabenas), z.B. "infos":["RN"]
 
Falls nach Barrierefreiheit eingeschränkt werden soll (Achtung: es gibt hier eine Hierarchie, d.h. die Codes sind in dem Sinne nicht als gleichwertig zu verstehen):
* "barrierFree": "ALL"
* "barrierFree": "BOARDING_ALIGHTING_SELF"
* "barrierFree": "BOARDING_ALIGHTING_BY_CREW"
* "barrierFree": "BOARDING_ALIGHTING_BY_NOTIFICATION"
 
 
Fall vorwärts oder rückwärts scrollen:
1. /trips inital Trip suchen -> Response enthält die Felder "scroll*" im Header
2. gleichen Request (identische Parameter) ausfüllen und zusätzlich in Header scrollContext= gewünschten "scroll*" Wert einfügen
 
Öko-Bilanz zwischen Zug und Privat-Auto (Indiviualverkehr):
"calculateEco":"true" -> liefert EcoBalance Werte
Parameters:

&vias={<JSON-Object>}

 Represents a List<String>, where each single Via is represented by a  JSON Object, with the following properties:
"uic": Integer (Mandatory)
"status":  enum
 "transportProducts": list of product-categories
"waittime": Integer in minutes
"direct": boolean, by means no change on the Via 
"couchette": boolean, searches for Couchette on the Via
"sleepingCar": boolean, searches for Sleeping-Car on the Via
Samples
{\"uic\":8507000,\"transportProducts\":[\"FASTTRAIN_REGIOEXPRESS\",\"INTERREGIO\",\"ICE_EN_CNL_CIS_ES_MET_NZ_PEN_TGV_THA_X2\",\"EUROCITY_INTERCITY_ICN_INTERCITYNIGHT_SUPERCITY\"],\"waittime\":20}

{\"uic\":8507000,\"status\":\"ALIGHTING_NOT_NECESSARY\",\"transportProducts\":[\"EUROCITY_INTERCITY_ICN_INTERCITYNIGHT_SUPERCITY\"]}

{\"uic\":8789023,\"direct\":true,\"couchette\":true,\"sleepingCar\":true}

&notVias=

 Analog &vias with "uic" and "status"

&noChangesAt=

Analog &vias with "uic" and "status"


&trainFormationType=

For performance reasons keep the default, if you are not interested in Train-Formations!
By default no Train-Formation hints are given. You may then call /v2/trainFormation anyway, but to get an early hint specify HINT_ORIGIN_DESTINATION to get such an info on each TripV2::LegV2::formationHint


&createSummary=

Will add a "TripV2::summary" as an overview with realtime and him-messages about the trip. Further on, in StopV2::stopStatus according to SBB Business Rule a state will be calculated
/v2/trips/{reconstructionContext} (GET)
Parameter "reconstructionContext" is given in any previous TripV2::reconstructionContext response by /v2/trips request.

Be aware:

the goal is to recreate the origin TripV2 where the reconstructionContext is taken from
if your interested in accessibility (de:Barriere frei) data, make sure the previous /v2/trips request called for &accessibility=
however reconstruction is not guranteed for various reasons (realtime changes, ..) -> catch 404 therefore

#### Train-Formation
TrainFormation may be requested for Legs with LegType.PUBLIC_JOURNEY by this API.

Returns details about train-compositions their wagons, sections and Occupancy at origin and destination of a Leg.

de:Zug-Formation

See J-A: Zug-Formationen

Be aware:

Train-Formations are considered realtime data, therefore they are only available today! Do not request in the past or in the future, those will be lost requests.


##### /v2/trainFormation/{reconstructionContext} (GET)
Request a desired trip by /v2/trips, if you are interested in formations consider setting &trainFormationType=HINT_ORIGIN_DESTINATION
will give you a TripV2::legs → LegV2::formationHint whether it is worth to request for TrainFormation's at all by this service
Call this API if FormationHint is given (or just by chance if you like)
404 is returned if the reconstructionContext cannot be resolved by Hafas
FUTURE USE:

&trainFormationType=ORIGIN_DESTINATION → will return TrainFormation directly at first call

#### Routes API
About JourneyDetail (de:Zuglauf).

##### /v2/routes/{journeyReference} (GET)
Die JourneyReference erhält man bspw. auf Trip::Leg::journeyReference oder Departure/Arrival::journeyReference

Optional kann der gleiche Zuglauf für ein anderes Datum angefragt werden.

#### Reports API
SBB dedicated usage for FZF (Fahrplan-Zusatz-Funktionen)
