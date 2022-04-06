
# Journey-Service - Developer Manual
[Support Mailbox](mailto:journey-service@sbb.ch)

## Abstract
The Journey-Service is a RESTful **"Journey-Planner"** facade abstracting **public transportation routing and planning** for a set of **SBB Switzrland** relevant underlying systems, such as Hafas, HIM, CUS, INFO+, DiDok, CAPRE, FOS, PLABE, OccupancyPrognosis, NOVA etc.

The main **use cases** are:
* Finding **`Place`s** (OJP Location), such as `StopPlace`s resp. (stations for public transport trains, busses, tramways, ..), `AddressPlace`s and `PoiPlace`s (Points of Interest (POI)).
* Finding concrete journeys (`Trip`s, `DatedVehicleJourney`s) as passenger-information.
* Finding some SBB specific additional info (such as `Trainformation`s, Realtime monitoring etc)

A set of implemented **SBB Business Rules** (such as delays, platform changes,..) guarantees that consuming channels may display consistent data.

If you are new to journey-planning with SBB, consider general standards as mentioned in [chapter "Routing Standards in "Public Transport"](v3/Journey-Service_v3_MODEL.md)

### Design Goals
* Strategic usage for SBB and partners: **Journey-Planner** systems are a business critical core functionality at SBB. Plenty of Consumer applications use it (like [sbb.ch](https://www.sbb.ch/en/home.html), [SBB Mobile](https://www.sbb.ch/de/fahrplan/mobile-fahrplaene/sbb-mobile.html), [SBB Ticket vending machines](https://www.sbb.ch/de/bahnhof-services/am-bahnhof/services-am-billettautomat/sbb-billettautomat.html), [SBB Group reservation and Capacity management](https://company.sbb.ch/de/sbb-als-geschaeftspartner/dienstleistungen/vertriebsmanagement-ktu/produkte/capre.html), [SBB B2P](https://company.sbb.ch/content/dam/sbb/de/pdf/sbb-konzern/sbb-als-geschaeftspartner/partnervertrieb/webserviceB2P_factsheet_d.pdf) and many others.
* Business Data consistency: all consumer get the same information (we work hard to enhance quality and continuous feature innovation).
* Easy usage by consumer developers: Our APIs are **RESTful** and documented, [Openapi 3](https://oai.github.io/Documentation/) **generated ApiClient** capable.

### About J-S versions

#### v1 and v2 (DEPRECATED)
* v1 is OBSOLETE, do not use it!
* v2 is still functional until END of ~2022
    * Based on [OpenJourneyPlanner](https://dms.vdv.de/mitglieder/Seiten/ojp.aspx), Hafas and some own flavor.

#### v3 (STRATEGIC)
v3 replaces v2 completely and extends new functionality.

See [**Standards considered** and its **Data Model**](v3/Journey-Service_v3_MODEL.md)

## Technical aspects

J-S v3 follows [SBB API Principles - RESTful APIs](https://schweizerischebundesbahnen.github.io/api-principles/restful) which is very alike to [Zalando RESTful API and Event Guidelines](https://opensource.zalando.com/restful-api-guidelines/).

Journey-Service logic and backend interfering is heavily based on the (SBB internal) library Journey-Assistant (J-A), therefore J-A documentation or even J-A Javadoc might be helpful as well, if you need to dig in deeper.

### URLs
We currently support **3 APIM accessible Openshift environments** for `Customer information/journey`
 * **TEST** via APIM https://developer-int.sbb.ch/apis/journey-service-test (early tests of newest features)
 * **INT** via APIM https://developer-int.sbb.ch/apis/journey-service (solid state, release-candidates)
 * **PROD** via APIM https://developer.sbb.ch/apis/journey-service (production state, well scaled and hopefully stable)

Important:
* All consumers must go through a proper [User-Registration-Process](User-Registration-Process.md) to get granted to APIM. We recommend to **register for AzureAD** (SBB IAM confirmed high availability) as a primary SSO Token Service Provider. It is up to you if you register additionally for "redHat-SSO" (there were some known incidents in the past) as a fallback Token-Provider if AzureAD should fail temporarily (might be a good scenario for extremely business critical applications).
* **Data per environment is completely detached from other environment**, by means results on DEV, TEST, INT and PROD **may differ (like different routings, stations, translations, accessibility infos, ..) and are therefore -not comparable among 2 environments-**.

### Backward compatibility
Considering [migration to newer versions of J-S](J-S_Migration_V2_to_V3.md): 
We have different concepts to maintain backward compatibility:

#### URL versioning
All APIs **without** `INCUBATOR` in their URL may be considered as **stable**. Any breaking changes will be leaded by our consumer-management (to the e-Mail you registered at APIM registration process)!

Any **APIs with `/v*/INCUBATOR/*` in their URL are highly @Experimental and might be changed breakingly on a daily basis or might even be deleted without further notice** (if you use it, make sure you keep frequent communication with us).

#### Deprecated stuff
Any APIs to be removed soon, will have a `@Deprecated` annotation and will be easily visible in the Swagger-UI (check any migration hints given and migrate as soon as possible).

Some **properties in response models might have a "description" like "@Deprecated use ... instead"**, to lead you into correcting steps.

Important:
* The **generated journey-service-client (see below) does not show any `@Deprecated` fields**, therefore you have to check the Swagger Doc carefully for your cases.
* Such deprecated code will be removed in future versions finally

### Testing your APIM access
See: [getting started](https://developer-int.sbb.ch/getting-started)
[Getting started with APIM (for Intranet users only)](https://confluence.sbb.ch/pages/viewpage.action?spaceKey=AITG&title=Getting+started+with+APIM)

Please check our [Testing hints](Test%20your%20access.pdf)

### Generated ApiClient's
See [Using OpenApi 3 ApiClient](ApiClient.md)

### Good to know
#### Release Notes
See [SBB staff release notes](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/ReleaseNotes.md)
Because J-S heavily relies on the Journey-Assistant library, also those [J-A Release-Notes](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-assistant/browse/ReleaseNotes.md) might reveal some important updates.

#### Blog announcements
Check the blog: https://developer.sbb.ch/apis/journey-service/blog

To stay up to date about new features or adjustments we highly advise you to use the RSS feed. Please follow the following instruction to insert the RSS Feed of the Journey-Service Blog: [Instruction RSS Feed](RSS%20Subscription-Instruction.pdf)

#### Model with version suffix
Why do we use some Models with a Version suffix, for e.g. TripV2, StopV2, ArrivalV3,..?

Unfortunately our APIM (3Scale) does not support multiple JSON definitions (multi version or namespace) though Swagger would support such a concept by grouping.
Therefore we melt classes sometimes with the same name by adding different version suffixes per Swagger group and publish them as ONE JSON definition file.

#### About parameter encoding
* Post body does not need encoded String's
* TripV2::reconstructionContext must not be encoded -> pass it as received
* do not encode JSON Lists resp. '[' or ']'
* OffsetDateTime 
See  [Support stricter encoding](https://github.com/spring-projects/spring-framework/issues/21577)
400: ..?dateTime=2019-04-23T14:56:14+00:00
OK: /b2c/v2/departures?originUIC=8503000&dateTime=2019-04-27T14%3A50%3A37.375%2B02%3A00

## API in detail
All Services (short abstract, request-parameters, response-models) are documented directly by swagger-annotations, therefore the documentation below is reduced to the max and is hopefully not really necessary for v2 API understanding in most cases.

Further on consider our [Routing-basics](https://github.com/SchweizerischeBundesbahnen/journey-service-b2c/blob/master/Journey-Service_Routing-Basics.pdf)

### Choosing the right service definition
Select the version of J-S REST-API: currently **v3** and some **v2**.

Remark:
* If accessing by APIM, some APIs may not be visible (compared to SBB internal access) or accessable (based on your "plan per API").
* v1 is deprecated and for reserved SBB internal usage

![Version JSON-definition](resources/J-S_version.png)

| Aspect | /v2 (current version) |
|--------|-----------------------|
|API-Path|/b2c/v2/*              |
|Request-Header	|like "Accept-Language" is used for standard or meta aspects (which are not in)|
|Request-Params|GET ?param1=..&param2=..
|Response-Header|like "Content-Language" (relates to "Accept-Language" or fallback-language by J-S)
|Response-Body|returns HttpStatus and declared model|
|arg defaulting|better defaulting (for minimal performance)|
|Realtime handling|less but well calculated attributes → consumer needs to analyze much less or nothing at all|

### End-user consistency
According to **SBB KI strategy (de:Kundeninformation)** it is a **declared goal to communicate consistent public transport information**! 
**J-S plays an important role** to provide such consistency **to any passengers on any end-user API resp. UI-channel**.

**J-S consumers must be aware that they might violate these SBB business rules by ignoring, manipulating or redefining given values and may impact unwanted end-user or public media critics**.

Some expressions are even related to **conventions by [BAV](https://www.bav.admin.ch/bav/de/home/verkehrsmittel/eisenbahn.html), SBB Infrastructure, SBB Personenverkehr or public transport Switzerland** in general.

### Request
For each Request to J-S set the mandatory header-fields (especially "Authorization", "Request-Id", Accept-Language) and body fields as given in the [Service-Contract per API](https://developer.sbb.ch/apis/journey-service/documentation).

A word about GET and POST:
* J-S offers **genererally idempotent GET** requests (since "Journey-Planners" are mostly information providing systems). However for technical reasons (like very long URL) a few APIs are specificied according to [SBB RESTful Principles: GET with BODY](https://schweizerischebundesbahnen.github.io/api-principles/restful/principles/#get-with-body) or [Zalando RESTful](https://opensource.zalando.com/restful-api-guidelines/#get-with-body). Both variants are implemented according to this rule:
1. **GET** with **complex parameters enforce JSON-Objects** (as consumer you will have to URL encode such JSON objects to prevent transmitting marshalling faults)
2. **POST** replacements like /v3/trips/* are still to be understood as an **idempotent** GET request, the API description contains a hint like _Idempotent GET with Body_.

### Response
#### Error-handling
See [Problem-Manual](Problem-Manual.md)

#### Business data aspects
Some properties resp. their value-expressions might be **translated according to requested "Accept-language" to german (de), french (fr), italian (it) and english (en)** for e.g.:
* Station-Names in request accept all 4 languages usually, though the reply (StopV2::name) contains only the local Switzerland translation as a special case (Geneva → Genève)
* v3.Notice::value or v2.Note::value are sometimes translated by SBB P Data-Mgmt
* Translations with a standard (TransportProductV2::trackTranslation) and short-translation (TransportProductV2::trackTranslationShort):
* other texts are translated by SBB Business Rules, like StopV2::*DelayText

J-S uses some Enum's which relate typically to business defined values, for e.g.:
* v2.TransportProductV2::category
* v2.VehicleType
* v2.LegType
* v3 only supports x-tensible enums in Request, in response are enum's are returned as String's

There are also specific extensions for developer convenience, such as:
* TransportProductV2::vehicleIconName showing the appropriate name in **SBB Corporate-Identity resources** (though the resource itself must be allocated by the consumer)
* the developer is still free whether he prefers its own mapping from TransportProductV2::vehicleType or uses the J-A given short cut to ::vehicleIconName

#### Business logic aspects
##### Formatted fields
J-S sometimes provides fields with a "*Formatted" suffix which contain values, that must be showed to public end-users instead of alternatively declared field without the suffix for SBB internal usage only, for e.g.
* TransportProductV2::number → B2E only: meant for SBB internal services or employees (for e.g. to display in SBB Casa to be seen by SBB employees only)
* TransportProductV2::number**Formatted** → B2C or B2P: Business Rule impacted value for end-users (for e.g. to display in SBB Webshop, SBB Mobile, ..)

##### Realtime analysis
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

### Most typical use cases
Most consumers will probably be interested in a simple train-connection from A to B. This scenario is supported in 2 steps usually:
* see [v2 APIs](v2/V2_APIs.md)
* see v3 TODO
