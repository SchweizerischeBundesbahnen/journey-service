# Journey-Service (J-S) migration v2 to v3
Unfortunately relating to Transmodel comes with some serious breaking model changes in v3.

## Type mappings

Remark:
* Obvious (quiet similar named) mappings are not mentioned explicitely below.

| J-S v3                   | J-S v2                          | J-A                           |
| ------------------------ | ------------------------------- |-----------------------------  |
| AbstractPlace            | see J-A                         | Location                      |
| ScheduledStopPoint       | StopV2                          | OrigDestType / StopType       |
| GeometryObject (GeoJSON) | see J-A                         | CoordinatesWGS84              |
| ServiceJourneyPattern    | see J-A                         | JourneySegment                |
| PTRideLeg                | LegV2 (::type=PUBLIC_TRANSPORT) | Leg (::type=PUBLIC_TRANSPORT) |
| AccessLeg                | LegV2 (::type=FOOTPATH)         | Leg (::type=FOOTPATH)         |
| PTConnectionLeg          | LegV2 (::type=TRANSFER)         | Leg (::type=TRANSFER)         |
| Notice                   | see J-A                         | Note                          |
| PTSituationMessage       | HimMessageV2                    | Message                       |
| Line + Operator          | TransportProductV2              | ProductType                   |
| ServiceCalendar          | ServiceDaysV2                   | ServiceDays                   |
