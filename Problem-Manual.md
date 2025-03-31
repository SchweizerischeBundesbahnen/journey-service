# Problems

**Error-handling** in Journey-Service (J-S) is done by returning a **[`Problem`](https://developer.sbb.ch/apis/journey-service/documentation) object in a failure case with further description**, according to [RFC 7807](https://tools.ietf.org/html/rfc7807), with some extensions.

**Contact <journey-service@sbb.ch>** for further support about a specific `Problem` if you don't find the answer below.

## Structure

Header values:
* `Content-Type` is set to `application/problem+json`.
* `Content-Language` is in most cases set to the one requested in `Accept-Language`.
* `Request-ID` is set to the one requested. It can help you diagnose problems by correlating traceability and/or log entries for a given web request across many systems and log files.

Problem properties:
* The standard and extended fields are described in the `Problem` object of the [documentation](https://developer.sbb.ch/apis/journey-service/documentation) / API specification. It helps consumers to understand the problem and possibly correct it or output a user-friendly error message to the end-user.
* For the **developers**, the `code`, `title` (deduced from _code_) and `detail` help understand the problem.
* For **machine** interpretation, the `type` or `code` can be use to take correct countermeasure. The former is much more practical to use for machine. It is hierarchically built, so that a "start with" can be used. The current used codes are listed in [ProblemCode](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-b2c/src/main/java/ch/sbb/ki/journeyservice/b2c/error/ProblemCode.java). In the _type_, expect that the e-mail and date can change. 
* For **end users**, the `displayTitle`, `displayDetail`, `displayResolution` delivers ready messages in some cases. The texts are coordinates with SBB front-ends. For other texts, typically other languages, the `translationKey` is provided. Our current translations are in [German](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-b2c/src/main/resources/messages_de.properties) (main language), [French](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-b2c/src/main/resources/messages_fr.properties), [Italian](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-b2c/src/main/resources/messages_it.properties), [English](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-b2c/src/main/resources/messages_en.properties) (Italian and German not yet completed).

HTTP status code:

Returned according to standards. The [Restful API guidelines of Zalando about HTTP Status](https://opensource.zalando.com/restful-api-guidelines/#150)) were considered.

Example:

For example, an HTTP response carrying JSON problem details in German looks like

    HTTP/1.1 400 Bad Request
    content-type: application/problem+json
    content-language: de
    request-id: testing the documentation
    
    {
     "status": 400
     "code": "BAD_REQUEST_PARAM_TRIP_BAD_ORIGIN_DESTINATION_VIA_SAME",
     "type": "tag:journey-service@sbb.ch,2024:Problem:type:BAD_REQUEST_PARAM_TRIP_BAD_ORIGIN_DESTINATION_VIA_SAME",
     "title": "invalid value in a parameter",
     "detail": "Same location used in origin / destination / vias.",
     "instance": "/v3/trips/by-origin-destination",
     "traceId": "3f92fa9ea94566ffbf30e42564e62c65",
     "documentation": "https://github.com/SchweizerischeBundesbahnen/journey-service/blob/master/Problem-Manual.md",
     "translationKey": "BAD_REQUEST_PARAM_TRIP_BAD_ORIGIN_DESTINATION_VIA_SAME",
     "displayTitle": "Hoppla, keine Verbindung gefunden.",
     "displayDetail": "",
     "displayResolution": "Bitte passen Sie die Haltestellen an (mindestens eine Eingabe ist doppelt vorhanden)."
    }

## J-S HttpStatus explained

### 200 OK
J-S was able to return a proper response with some content (at least one).

Be aware:
* For v2 the following convention was made historically: 200 with an emptyList body "[]" for API's returning List<T> where no hits were found.

### 400 Bad Parameter
J-S considers this as a caller side problem.

Remark:
* **Check the OpenAPI description on each API and its Parameters.** We spend a lot of effort to improve the J-S Service-Contract, which is relevant for validations.
* Swagger-Users may not even reach the J-S Backend (in such cases no error-body is returned sometimes. Please check the Swagger-UI carefully.)

### 404 Object not found
If an expected object cannot be found, for e.g. /v3/trips/{id} which may not resolve. 

### 500 Internal Server Error
Generic problem, probably a bug, but can also be a bad parameter not properly validated.

### 501 Not Implemented
Possible reasons:
* Some early INCUBATOR feature is not implemented but forecasted for experimental reasons.

Please keep patient, might work with a next release of J-S.

### 502 Bad Gateway
Possible reasons:
* Send originally by underlying system.
* J-S considers a used Backend to have some kind of developer fault. 

### 503 Service Unavailable
The service has currently an availability problem for some requests. Often a retry after a short time might solve the problem. If becoming a lot of this status, then the system is overloaded.

Since J-S has a complicated integration-architecture, various Network/IP, Broken-Pipe or Backend-System faults may occur by chance.

### 504 Timeout
J-S defines generally a timeout of about 30s for any underlying backend requests, however some routing requests might be tricky for the underlying router and might take longer.

Besides timeouts declared by underlying systems are out of control by J-S.

### Errors given by APIM
If there is an error by the "security-layer" errors might result directly from APIM (J-S will be behind and therefore not be the direct source of the Problem).

#### Too many calls
If the assigned plan limit is reached (requests/minute) an error like this might result: 

    {"timestamp":"2021-03-17T06:39:51.654+0000","status":429,"error":"Too Many Requests","message":"Limit exceeded: Path: '/v3/service-calendar', Query: 'null', Method: 'GET'","path":"/v3/service-calendar"}
