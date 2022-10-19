
# Journey-Service - Developer Manual
[Support Mailbox](mailto:journey-service@sbb.ch)

## Abstract
The Journey-Service is a RESTful **"Journey-Planner"** facade abstracting **public transportation routing and planning** for a set of **SBB Switzrland** relevant underlying systems, such as Hafas, HIM, CUS, INFO+, DiDok, CAPRE, FOS, PLABE, OccupancyPrognosis, NOVA etc.

The main **Use Cases** are:
* Finding **`Place`** (aka OJP `Location`), such as `StopPlace` resp. (stations for public transport trains, busses, tramways, ...), `AddressPlace` and `PoiPlace` (Points of Interest (POI)).
* Finding concrete journeys (`Trip`, `DatedVehicleJourney`) as passenger-information.
* Finding some SBB specific additional info (such as `Trainformation`s, Realtime monitoring etc)

A set of implemented **SBB Business Rules** (such as delays, platform changes,...) guarantees that consuming channels may display consistent data.

If you are new to journey-planning with SBB, consider general standards as mentioned in [chapter "Routing Standards in "Public Transport"](v3/Journey-Service_v3_MODEL.md)

### Design Goals
* Strategic usage for SBB and partners: **Journey-Planner** systems are a business critical core functionality at SBB. Plenty of Consumer applications use it (like [sbb.ch](https://www.sbb.ch/en/home.html), [SBB Mobile](https://www.sbb.ch/de/fahrplan/mobile-fahrplaene/sbb-mobile.html), [SBB Ticket vending machines](https://www.sbb.ch/de/bahnhof-services/am-bahnhof/services-am-billettautomat/sbb-billettautomat.html), [SBB Group reservation and Capacity management](https://company.sbb.ch/de/sbb-als-geschaeftspartner/dienstleistungen/vertriebsmanagement-ktu/produkte/capre.html), [SBB B2P](https://company.sbb.ch/content/dam/sbb/de/pdf/sbb-konzern/sbb-als-geschaeftspartner/partnervertrieb/webserviceB2P_factsheet_d.pdf) and many others.
* [Business aspects](BusinessAspects.md): all consumer get the same information (we work hard to enhance quality and continuous feature innovation).
* Easy usage by consumer developers: Our APIs are **RESTful** and documented, [Openapi 3](https://oai.github.io/Documentation/) **generated ApiClient** capable.
* Follow existing standards but simplify and adapt to enhance time-to-market of new customer-information features

### Good to know
#### Blog announcements
Check the blog: https://developer.sbb.ch/apis/journey-service/blog

To stay up to date about new features or adjustments we highly advise you to use the RSS feed. Please follow the following instruction to insert the RSS Feed of the Journey-Service Blog: [Instruction RSS Feed](RSS%20Subscription-Instruction.pdf)

#### Release Notes (SBB internal)
See [SBB staff release notes](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/ReleaseNotes.md).
Because J-S heavily relies on the Journey-Assistant library, also those [J-A Release-Notes](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-assistant/browse/ReleaseNotes.md) might reveal some important updates.

## API in detail
All Services (short abstract, request-parameters, response-models) are documented directly by swagger-annotations, therefore the documentation below is reduced to the max and is hopefully rarely necessary for v3 (or v2) API understanding in most cases.

See [Technical Aspects](TechnicalAspects.md)

See [Business Aspects](BusinessAspects.md)

### Choosing the right service definition
In [developer.sbb.ch URLs](TechnicalAspects.md) all available APIs over all versions are listed in one mixed group. Some APIs may not be visible or accessable (based on your **API plan** at [registration](User-Registration-Process.md)).

However for internal developers at SBB building their own instance on localhost (or using SBB internal DEV environment), the version can be selected by OpenApi Group:

![Version JSON-definition](resources/J-S_version.png)
