# Technical Aspects
J-S v3 follows [SBB API Principles - RESTful APIs](https://schweizerischebundesbahnen.github.io/api-principles/restful) which is very alike to [Zalando RESTful API and Event Guidelines](https://opensource.zalando.com/restful-api-guidelines/).

Journey-Service logic and backend interfering is heavily based on the (SBB internal) library Journey-Assistant (J-A), therefore J-A documentation or even J-A Javadoc might be helpful as well, if you need to dig in deeper.

## URLs
We currently support **3 APIM accessible Openshift environments** for `Customer information/journey`
* **TEST** via APIM https://developer-int.sbb.ch/apis/journey-service-test (early tests of newest features)
* **INT** via APIM https://developer-int.sbb.ch/apis/journey-service (solid state, release-candidates)
* **PROD** via APIM https://developer.sbb.ch/apis/journey-service (production state, well scaled and hopefully stable)

Important:
* All consumers must go through a proper [User-Registration-Process](../User-Registration-Process.md) to get granted to APIM. We recommend to **register for AzureAD** (SBB IAM confirmed high availability) as a primary SSO Token Service Provider. It is up to you if you register additionally for "redHat-SSO" (there were some known incidents in the past) as a fallback Token-Provider if AzureAD should fail temporarily (might be a good scenario for extremely business critical applications).
* **Data per environment is completely detached from other environment**, by means results on DEV, TEST, INT and PROD **may differ (like different routings, stations, translations, accessibility infos, ..) and are therefore -not comparable among 2 environments-**.

## About J-S versions
### v2 (DEPRECATED)
* v2 most APIs are getting removed END of ~2024, those without a DEPRECATED tag remain as is, those with such a tag have a description what v3 API has to be used instead.
    * Based on [OpenJourneyPlanner](https://dms.vdv.de/mitglieder/Seiten/ojp.aspx), Hafas and some own flavor.

### v3 (STRATEGIC)
v3 replaces v2 completely and extends new functionality.

See [**Standards considered** and its **Data Model**](../v3/Journey-Service_v3_MODEL.md)

## Request and Response

All APIs are synchroneous Request/Response RESTful APIs.

See [v3 REST-API standards](../v3/Journey-Service_v3_MODEL.md)

### Request
For each Request to J-S set the header-fields (especially `Authorization`, `Request-Id`, `Accept-Language`) and GET or POST-body parameters as given in the [Service-Contract per API](https://developer.sbb.ch/apis/journey-service/documentation).
J-S follows the W3C standard [Trace Context](https://www.w3.org/TR/trace-context) which helps operating in distributed systems.

A word about GET and POST:
* J-S offers **genererally idempotent GET** requests (since "Journey-Planners" are mostly information providing systems). However for technical reasons (like very long URL) a few APIs are specificied according to [SBB RESTful Principles: GET with BODY](https://schweizerischebundesbahnen.github.io/api-principles/restful/principles/#get-with-body) or [Zalando RESTful](https://opensource.zalando.com/restful-api-guidelines/#get-with-body). Both variants are implemented according to this rule:
1. **GET** with **complex parameters enforce JSON-Objects** (as consumer you will have to URL encode such JSON objects to prevent transmitting marshalling faults)
2. **POST** replacements like /v3/trips/* are still to be understood as an **idempotent** GET request, the API description contains a hint like _Idempotent GET with Body_.

| Aspect | /v2 | /v3 |
|--------|-----------------------|-----|
|arg defaulting|better defaulting (for minimal performance)|dito|
|Realtime handling|less but well calculated attributes → consumer needs to analyze much less or nothing at all|dito

### Response
#### Error-handling
See [Problem-Manual](../Problem-Manual.md)

#### Deserialize JSON tolerant
Make sure you always deserialize J-S JSON responses tolerant (we might release new properties in a steady pace), for e.g. in Java:
```
final ObjectMapper mapper = Jackson2ObjectMapperBuilder.json()
        ..
        .featuresToDisable(DeserializationFeature.ADJUST_DATES_TO_CONTEXT_TIME_ZONE)
        .build();

// needs jackson-datatype-jsr310
mapper.registerModule(new JavaTimeModule());

// tolerant (at least on PRODuction environment) !!!
boolean strict = false; 
mapper.configure(SerializationFeature.FAIL_ON_SELF_REFERENCES, strict);
mapper.configure(SerializationFeature.FAIL_ON_UNWRAPPED_TYPE_IDENTIFIERS, strict);
mapper.configure(SerializationFeature.FAIL_ON_EMPTY_BEANS, strict);
mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, strict);
mapper.configure(DeserializationFeature.FAIL_ON_NULL_FOR_PRIMITIVES, strict);
```

#### Caching
Several endpoints provide responses which can be cached by the client. The caching is implemented according to [RFC-7232](https://www.rfc-editor.org/rfc/rfc7232) (`ETag`) and [RFC-7234](https://www.rfc-editor.org/rfc/rfc7234) (`Cache-Control`). 

The cacheable responses are recognizable by the `Cache-Control`-header which describes the kind of the cache and how long it endures. The `Cache-Control`-header complies with RFC-7234 and therefore will not be described further and we refer to the official [documentation](https://www.rfc-editor.org/rfc/rfc7234#section-5.2).

Implementation:
1. Make a request to a cacheable endpoint and read in the `ETag`-header of the response. Store `ETag` and Response.

2. When you request the endpoint again, send your `ETag` in the `If-None-Match`-header.  
    
    2.1. When Server responds with `304 - Not Modified`, use your cached version.
    
    2.2. When Server returns `200 - Ok`, use response and update your cache.

Sample:
* see v3/service-calendar

## Backward compatibility
See [migration to newer versions of J-S](v2/J-S_Migration_V2_to_V3.md).

We have different concepts to maintain backward compatibility:

### URL versioning
Yes, we DO url versioning because of some reasons:
* technical: our APIM solution allows ONE JSON-specification without namespaces only
* historical standard changes: we prefer real breaking changes instead of semantic "overlaying" sometimes (and keep different versions parallel as compatible as possible)

All APIs **without** `INCUBATOR` in their URL may be considered as **stable**. Any breaking changes will be leaded by our consumer-management (to the e-Mail you registered at APIM registration process)!

Any **APIs with `/v*/INCUBATOR/*` in their URL are highly @Experimental and might be changed breakingly on a daily basis or might even be deleted without further notice** (if you use it, make sure you keep frequent communication with us).

### Deprecated stuff
Any APIs to be removed soon, will have a `@Deprecated` annotation and will be easily visible in the Swagger-UI (check any migration hints given and migrate as soon as possible to the suggested new approach).

Some **properties in response models might have a "description" like "@Deprecated use ... instead"**, to lead you into correcting steps.

Important:
* The **generated journey-service-client (see below) might not show `@Deprecated` stuff well enough**, therefore to check the SwaggerUI Doc carefully for your cases.
* Deprecated code will be removed in future versions finally.

### Model with version suffix
Why do we use some Models with a Version suffix, for e.g. TripV2, StopV2, ArrivalV3,..?

Unfortunately our APIM (3Scale) does not support multiple JSON definitions (multi version or namespace) though Swagger would support such a concept by grouping.
Therefore, we melt classes sometimes with the same name by adding different version suffixes per Swagger group and publish them as ONE JSON definition file.
  
## Generated ApiClient's
See [Using OpenApi 3 ApiClient](ApiClient.md)

## Testing
Please check our[Testing hints](Test your access.pdf)

### About parameter encoding
* Post body does not need encoded String's
* TripV2::reconstructionContext must not be encoded -> pass it as received
* do not encode JSON Lists resp. '[' or ']'
* OffsetDateTime
  See  [Support stricter encoding](https://github.com/spring-projects/spring-framework/issues/21577)
  400: ..?dateTime=2019-04-23T14:56:14+00:00
  OK: /b2c/v2/departures?originUIC=8503000&dateTime=2019-04-27T14%3A50%3A37.375%2B02%3A00

### Testing your APIM access
See: [getting started](https://developer-int.sbb.ch/getting-started)
[Getting started with APIM (for Intranet users only)](https://confluence.sbb.ch/pages/viewpage.action?spaceKey=AITG&title=Getting+started+with+APIM)
