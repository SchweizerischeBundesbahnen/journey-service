# Business Aspects

## Relevant Standards
Some important links:
* [BAV](https://www.bav.admin.ch/bav/de/home/verkehrsmittel/eisenbahn.html)
* [v580 FIScommun](https://www.allianceswisspass.ch/de/tarife-vorschriften/uebersicht/V580/Produkte-der-V580-FIScommun-1)
* [Open-Data Plattform Mobility Switzerland](https://opentransportdata.swiss/de/)
* [TransportMode](TransportMode.md)

## End-user consistency
According to **SBB KI strategy (de:Kundeninformation)** it is a **declared goal to communicate consistent public transport information**!
**J-S plays an important role** to provide such consistency **to any passengers on any end-user API resp. UI-channel**.

**J-S consumers must be aware that they might violate these SBB business rules by ignoring, manipulating or redefining given values and may impact unwanted end-user or public media critics**.

Some expressions are even related to **conventions by [BAV](https://www.bav.admin.ch/bav/de/home/verkehrsmittel/eisenbahn.html), SBB Infrastructure, SBB Personenverkehr or public transport Switzerland** in general.

## Translations
Some properties resp. their value-expressions might be **translated according to requested "Accept-language" to german (de), french (fr), italian (it) and english (en)** for e.g.:
* Station-Names in request accept all 4 languages usually, though the reply (StopV2::name) contains only the local Switzerland translation as a special case (Geneva → Genève)
* v3.Notice::value or v2.Note::value are sometimes translated by SBB P Data-Mgmt
* Translations with a standard (TransportProductV2::trackTranslation) and short-translation (TransportProductV2::trackTranslationShort):
* other texts are translated by SBB Business Rules, like StopV2::*DelayText

## About ServiceVehicle

See [TransportMode](TransportMode.md)

There are specific extensions for developer convenience, such as:
* TransportProductV2::vehicleIconName showing the appropriate name in **SBB Corporate-Identity resources** (though the resource itself must be allocated by the consumer)
* the developer is still free whether he prefers its own mapping from TransportProductV2::vehicleType

## Business logic aspects
### Formatted fields
J-S sometimes provides fields with a "*Formatted" suffix which contain values, that must be showed to public end-users instead of alternatively declared field without the suffix for SBB internal usage only, for e.g.
* TransportProductV2::number → B2E only: meant for SBB internal services or employees (for e.g. to display in SBB Casa to be seen by SBB employees only)
* TransportProductV2::number**Formatted** → B2C or B2P: Business Rule impacted value for end-users (for e.g. to display in SBB Webshop, SBB Mobile, ..)

### Realtime analysis
The SBB underlying systems may **provide realtime-data, typically TODAY only (~ NOW..+4h)**.

Getting the right realtime conclusions can be tricky, therefore J-S provides convenience data whenever possible.

StopV2 for e.g. contains pre-calculated fields to inform about relevant realtime status of a TransportProductV2 at a specific StopV2:
* ::boardingAlightingStatus
* ::stopStatus s. [Journey-Service_Routing-Basics](https://github.com/SchweizerischeBundesbahnen/journey-service/blob/master/Journey-Service_Routing-Basics.pdf)

About any ***Rt** properties:
* Ideally these fields are always null, means transport organisations are operating as planned
* If any vehicle (TransportProductV2) is not operating according to its scheduled plan, *Rt fields may contain correcting values here and there (availability usually max 2h in the future and may disappear quickly in the past, because irrelevant for the current instant in time)
* *Rt fields may update their values for the same trip or journey if repeatedly requested, since they express “real-time” behaviour. (However do update your query as less as possible, for performance reasons.)
* If the *Rt fields are empty, just use the corresponding (same name) fields without “Rt” suffix for properly planned values

Journey-Service does not know the exact position of a vehicle yet and does not even guarantee that a vehicle has passed a station in reality. (However we have stories to transmit such additional info in the near future.)

Remark:
* SBB staff see [StopPlace/Station state]( based on /display/FAHRPLAN/Haltestellen-Status)

## Most typical use cases
Most consumers will probably be interested in a simple train-connection from A to B. This scenario is supported in 2 steps usually:
* see [v2 APIs](v2/V2_APIs.md)
* see v3 TODO
