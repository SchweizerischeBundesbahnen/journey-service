
# Journey-Service - Developer Manual
[Support Mailbox](mailto:journey-service@sbb.ch)

## Abstract
The Journey-Service is a RESTful **"Journey-Planner"** facade abstracting **public transportation routing and planning** for a set of **SBB Switzrland** relevant underlying systems, such as Hafas, HIM, CUS, INFO+, DiDok, CAPRE, FOS, PLABE, OccupancyPrognosis, NOVA etc.

The main **Use Cases** are:
* Finding **`Place`** (aka OJP `Location`), such as `StopPlace` resp. (stations for public transport trains, busses, tramways, ..), `AddressPlace` and `PoiPlace` (Points of Interest (POI)).
* Finding concrete journeys (`Trip`, `DatedVehicleJourney`) as passenger-information.
* Finding some SBB specific additional info (such as `Trainformation`s, Realtime monitoring etc)

A set of implemented **SBB Business Rules** (such as delays, platform changes,..) guarantees that consuming channels may display consistent data.

If you are new to journey-planning with SBB, consider general standards as mentioned in [chapter "Routing Standards in "Public Transport"](v3/Journey-Service_v3_MODEL.md)

### Design Goals
* Strategic usage for SBB and partners: **Journey-Planner** systems are a business critical core functionality at SBB. Plenty of Consumer applications use it (like [sbb.ch](https://www.sbb.ch/en/home.html), [SBB Mobile](https://www.sbb.ch/de/fahrplan/mobile-fahrplaene/sbb-mobile.html), [SBB Ticket vending machines](https://www.sbb.ch/de/bahnhof-services/am-bahnhof/services-am-billettautomat/sbb-billettautomat.html), [SBB Group reservation and Capacity management](https://company.sbb.ch/de/sbb-als-geschaeftspartner/dienstleistungen/vertriebsmanagement-ktu/produkte/capre.html), [SBB B2P](https://company.sbb.ch/content/dam/sbb/de/pdf/sbb-konzern/sbb-als-geschaeftspartner/partnervertrieb/webserviceB2P_factsheet_d.pdf) and many others.
* [Business aspects](BusinessAspects.md): all consumer get the same information (we work hard to enhance quality and continuous feature innovation).
* Easy usage by consumer developers: Our APIs are **RESTful** and documented, [Openapi 3](https://oai.github.io/Documentation/) **generated ApiClient** capable.

### Technical aspects
See [Technical Aspects](TechnicalAspects.md)

### Good to know
#### Release Notes
See [SBB staff release notes](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/ReleaseNotes.md)
Because J-S heavily relies on the Journey-Assistant library, also those [J-A Release-Notes](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-assistant/browse/ReleaseNotes.md) might reveal some important updates.

#### Blog announcements
Check the blog: https://developer.sbb.ch/apis/journey-service/blog

To stay up to date about new features or adjustments we highly advise you to use the RSS feed. Please follow the following instruction to insert the RSS Feed of the Journey-Service Blog: [Instruction RSS Feed](RSS%20Subscription-Instruction.pdf)

## API in detail
All Services (short abstract, request-parameters, response-models) are documented directly by swagger-annotations, therefore the documentation below is reduced to the max and is hopefully not really necessary for v2 API understanding in most cases.

### Choosing the right service definition
Select the version of J-S REST-API: currently **v3** and some **v2**.

Remark:
* If accessing by APIM, some APIs may not be visible (compared to SBB internal access) or accessable (based on your "plan per API").
* v1 is deprecated and for reserved SBB internal usage

![Version JSON-definition](resources/J-S_version.png)

| Aspect | /v2 (current version) | /v3 |
|--------|-----------------------|-----|
|API-Path|/b2c/v2/*              | /v3 |
|Request-Header	|like "Accept-Language" is used for standard or meta aspects (which are not in)|dito|
|Request-Params|GET ?param1=..&param2=..|dito|
|Response-Header|like "Content-Language" (relates to "Accept-Language" or fallback-language by J-S)|dito|
|Response-Body|returns HttpStatus and declared model or `Problem`|dito|
|arg defaulting|better defaulting (for minimal performance)|dito|
|Realtime handling|less but well calculated attributes → consumer needs to analyze much less or nothing at all|dito

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