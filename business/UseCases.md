# Journey-Planner use cases by J-S APIs

## General concepts
* All journeys rely on a yearly plan (de:Soll-Fahrplan).
* However, in reality not all transport-products may be operated in time, therefore realtime changes happen to the plan, see [Trip-Analyse](https://confluence.sbb.ch/pages/viewpage.action?pageId=1591346463)
* Trip's are tailored traveling routes for a specific passenger. They do not have a unique ID, rather a Trip-Context to refresh its state. Each Leg is operated by specific ServiceProduct (by means passengers need to change at the ending StopPlace of a Leg). The underlying system provides a default change time when passengers need to switch ServiceProduct's. However, these values may be customized as well.
* A DatedVehicleJourney covers the whole scope of a single ServiceProduct from the very start to the very end. They might be identified by a unique ServiceJourney::id (aka JourneyReference, which is not a physical vehicle-ID).
* Time aspects: For Switzerland most date/time data is given in Swiss timezone "Europe/Zurich" (however check OffsetDateTime UTC values for foreign Locations). Request-date/times are always interpreted to a StopPlace local time.


The most typical use case usually is to get a Passenger Trip, like:
1. use /v3/places to find A=origin (start) and B=destination (end) Place's → any hit as a StopPlace will have a concrete id (for e.g. UIC standardize for e.g. "8507000" for "Bern").
2. use /v3/trips to find a set of trips (usually between 4..7 hits) and interprete the best case in your case. The reponse Trip object will contain Leg's (operated by a specific transport-company, for e.g. "SBB") and a list of ScheduledStopPoint's where A=stops[0] and B=stops[last], each StopCall has various data such as timeAimed/Rt, stopStatus, ...

## Capabilities of each API-set
### Managed data
#### Places APIs
Useful to query for locations (`StopPlace`, `StopPlaceDetailed`, `Address`, `PointOfInterest`).

Concepts:
* Switzerland uses UIC codes for station IDs, DiDok maps other countries to UIC as well and therefore troughout J-S such values like 8507000 for "Bern" are consistent.
  * ::swissLocationId will be provided _soon_
* Addresses are postal addresses imported from Swiss POST in regular phases.
* Points of Interest (POI) are licenced data such as Museums, Shopping mall etc.

#### Schedules APIs
Concepts:
* The **timetables for all public transportation products are renewed on a yearly base** (de: Fahrplanwechsel). There is typically a change about 1st decade of December.

Returns "PoolInfo":
* declaring the current service-days period (usually current year between first decade of december)
* StopPlace data-pools (validity periods of operation)

Remark:
* For most users this service will not be relevant. J-S will navigate in the current pool automatically or throw errors if your queries do not match the current period.

#### Situations APIs
Allows search for active HIM-Messages about disruptions, construction sites, events,..

Be aware:
* PTSituation objects are also related to concrete journeys are returned by ServiceJourney::situation.


### Service-Journey

#### DatedVehicleJourney APIs
Complete by one physical vehicle (aka JourneyDetail, de:Zuglauf).

Partial DatedVehicleJourney's:
* Departure (de:Abfahrtstabelle)
* Arrival (de:Ankunftstabelle)
* PTRideLeg

#### Trips APIs
Complex Journey-Planning (connections or de:Verbindungen) for travelling passengers.

see [CompoundTrain](CompoundTrain.md)

##### Eco calculation
Comparing public transportation vs private car or airplane:
* set request param "calculateEco":"true" -> check response for EcoBalance

### Mixed functionality
#### Archive APIs
Recorded Journey-Planner data back to 60s in the past. Each day is recorded separately for effective public transport.

#### Open Journey Planner APIs
SBB Personenverkehr relies on Hafas as main router.

However, OJP is an alternate public routed provided by BAV CH.
The listed APIs behave similar to other APIs, except the router is different and therefore the responses are yet rather experimental.