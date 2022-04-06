
1. use /b2c/v2/locations to find A=origin (start) and B=destination (end) Location's → any Location hit with type=STATION will have a concrete UIC standardized ID as given in the "uicOrId" field (for e.g. 8507000 for "Bern").
2. use /b2c/v2/trips to find a set of trips (usually between 4..7 hits) and interprete the best case in your case. The reponse TripV2 object will contain Leg's (segments operated by a specific transport-company (for e.g. "SBB") and a list of Stop's where A=stops[0] and B=stops[last], each Stop has various data such as departureDateTime, stopStatus, ...

### Info API
Concepts:
* The **timetables for all public transportation products are renewed on a yearly base** (de: Fahrplanwechsel). There is typically a change about 1st decade of December.

Remark:
* For most users this service will not be relevant. J-S will navigate in the current pool automatically or throw errors if your queries do not match the current period.

#### /v2/info
Returns "PoolInfo":
* declaring the current service-days period (usually current year between first decade of december)
* STATION data-pools (validity periods of operation)

#### /v2/traffic
Allows search for active HIM-Messages about disruptions, construction sites, events,..

Be aware:
* HimMessageV2 objects related to concrete journeys are returned by /v2/trips in LegV2::messages or JourneyDetail::himMessages directly

### Location API
Useful to query for locations (stations, addresses, POIs).

Concepts:
* Switzerland uses UIC codes for station IDs, DiDok maps other countries to UIC as well and therefore troughout J-S such values like 8507000 for "Bern" are consistent.
* Addresses are postal addresses imported from Swiss POST in regular phases.
* Points of Interest (POI) are licenced data such as Museums, Shopping mall etc.

#### /v2/locations
Returns a generic structure of a Location. Discriminator Location::type specifies a Locationas:
* **STATION** (known Stop, routed by Hafas)
* **ADDRESS** (postal addresses)
* **POI** (points of interest, like Museum, ..)

### Journey-Planning services
There are various ways to query for vehicle (transport-product) routes or tailored passenger trips from A to B.

Concepts:
* All journeys rely on a yearly plan (de:Soll-Fahrplan).
* However in reality not all transport-products may be operated in time, therefore realtime changes happen to the plan, see [J-A: Trip-Analyse](https://confluence-ext.sbb.ch/display/JAD/J-A%3A+Trip-Analyse)
* Trip's are tailored traveling routes for a specific passenger. They do not have a unique ID, rather a reconstruction- or Trip-Context. Each Leg means a separate transport-product (by means passengers need to change at the ending Stop of a Leg). The underlying system provides a default change time when passengers need to switch transport-products. However these values may be customized as well.
* JourneyDetails cover the whole scope of a single transport-product from the very start to the very end. They might be identified by a unique JourneyReference (which is not a physical vehicle-ID).
* Time aspects: For Switzerland most date/time data is given in Swiss timezone "Europe/Zurich" (however check OffsetDateTime UTC values for foreign Locations).

#### Stationboard API
##### /v2/departures
Departureboard (de:Abfahrtstabelle) at STATION showing departures leaving from there with optional directions.

##### /v2/arrivals
Arrivalboard (de:Ankunftstabelle) at STATION showing arrivals.

#### Trips API
Complex Journey-Planning (connections or de:Verbindungen) for travelling passengers.

#### /v2/trips

##### Accessibility
is supported on some transport-products (depends on given operator-data). The following Enum's have a Business Rule related hierarchy which is handled by response:
* "BOARDING_ALIGHTING_SELF"
* "BOARDING_ALIGHTING_BY_CREW"
* "BOARDING_ALIGHTING_BY_NOTIFICATION"

Be aware:
* The goal is to recreate the origin TripV2 where the reconstructionContext is taken from
  if your interested in accessibility (de:Barriere frei) data, make sure the previous /v2/trips request called for &accessibility=
  however reconstruction is not guranteed for various reasons (realtime changes, ..) -> catch 404 therefore

##### Scrolling
any /trips request returns a set of TripV2 within 0..7 hits. To get previous or next hits use the Header field "SCROLL-CONTEXT":
1. /trips inital search -> Response with "scroll*" Header-fields
2. execute very same Request (identical parameters) and set Header scrollContext= with SCROLL-FORWARD or -BACKWARD value

##### Eco calculation
comparing public transportation vs private car or airplane:
* set request param "calculateEco":"true" -> check response for EcoBalance

##### Vias
are optional routing points to be included or avoided for trip requests as a JSON Object parameter:

&vias
{"uic":mandatory Integer,"status":"BOARDING_ALIGHTING_NECESSARY","transportProducts":[list of TransportProduct-Category],"waittime":Integer in min.,"direct":true|false,"couchette":true|false,"sleepingCar":true|false}

&notVias
{"uic":mandatory Integer,"status":"NO_PASS_THROUGH"}  where possible status are: NO_PASS_THROUGH_META_STATION, NO_PASS_THROUGH

&noChangesAt
{"uic":mandatory Integer,"status":"NO_CHANGE"} where possible status are: NO_CHANGE_META_STATION, NO_CHANGE

##### trainFormationType
for performance reasons keep the default, if you are not interested in Train-Formations!
By default no Train-Formation hints are given. You may then call /v2/trainFormation anyway, but to get an early hint specify HINT_ORIGIN_DESTINATION to get such an info on each TripV2::LegV2::formationHint


##### createSummary
will add a "TripV2::summary" as an overview with realtime and him-messages about the trip. Further on, in StopV2::stopStatus according to SBB Business Rule a state will be calculated
/v2/trips/{reconstructionContext} (GET)
Parameter "reconstructionContext" is given in any previous TripV2::reconstructionContext response by /v2/trips request.

##### /v2/trainFormation/{reconstructionContext}
TrainFormation (de:Zugformation) may be requested for Legs with LegType.PUBLIC_JOURNEY by this API.

Returns details about train-compositions their wagons, sections and Occupancy at origin and destination of a Leg.

See [J-A: Zug-Formationen](https://confluence-ext.sbb.ch/display/JAD/J-A%3A+Zug-Formationen)

Be aware:
* Train-Formations are considered realtime data, therefore they are only available today! Do not request in the past or in the future, those will be lost requests.

Request a desired trip by /v2/trips, if you are interested in formations consider setting &trainFormationType=HINT_ORIGIN_DESTINATION
will give you a TripV2::legs → LegV2::formationHint whether it is worth to request for TrainFormation's at all by this service
Call this API if FormationHint is given (or just by chance if you like)
404 is returned if the reconstructionContext cannot be resolved by Hafas
FUTURE USE:

&trainFormationType=ORIGIN_DESTINATION → will return TrainFormation directly at first call

#### Routes API
About JourneyDetail (de:Zuglauf).

##### /v2/routes/{journeyReference}
Such JourneyReference values may be obtained by Trip::Leg::journeyReference oder Departure/Arrival::journeyReference

#### Reports API
SBB dedicated usage for FZF (Fahrplan-Zusatz-Funktionen)
