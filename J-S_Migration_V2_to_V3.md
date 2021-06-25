# Journey-Service (J-S) migration v2 to v3
Unfortunately relating to Transmodel comes with some serious breaking model changes in v3.

## Type mappings

Remark:
* Obvious (quiet similar named) mappings are not mentioned explicitely below.

| V3                    | V2                              | J-A                           |
| --------------------- | ------------------------------- |-----------------------------  |
| ServiceJourneyPattern | see J-A     --------------------| JourneySegment                |
| PTRideLeg             | LegV2 (::type=PUBLIC_TRANSPORT) | Leg (::type=PUBLIC_TRANSPORT) |
