# Problems
**Error-handling** is an important topic.

It is part of the service-contract to define how an underlying system should provide further information in case of an erroneous request or backend action.

**https://tools.ietf.org/html/rfc7807** says:
* **Header**: HttpStatus resp. high-level error class
* **Body**:  finer-grained details of the problem (machine-readable format, the client can treat it appropriately)
** section 3.1 declares members of a Problem Details (**problem+json**) Object: **status, type, title, detail, instance**

For example, an HTTP response carrying JSON problem details:

    HTTP/1.1 404 Not Found
    Content-Type: application/problem+json
    Content-Language: en
    
    {
     "type": "https://ki-journey-service.app.ose.sbb-cloud.net/sbb/v2/trips",
     "title": "Not supported: EVA",
     "detail": "The underlying system maintains UIC station codes only.",
     "instance": "/location"
    }

## J-S::B2C v2
### Properly handled errors by J-S
**Error** JSON object

See [J-S Swagger2 API](https://developer.sbb.ch/apis/journey-service/documentation) of each API concerning known or expectable HttpStatus (for e.g. 400 BAD_REQUEST).

In most cases the body will contain a detailed error, according to [RFC-7807](https://tools.ietf.org/html/rfc7807):
* Header: HttpStatus resp. high-level error class
* Body: finer-grained details of the problem (machine-readable format, the client can treat it appropriately) ** section 3.1 declares members of a Problem Details (problem+json) Object: status, type, title, detail, instance
Error texts in "title" or "detail" will be given according to "CONTENT-LANGUAGE" (though mostly in english) and:
* are not meant to display to end-users 1:1, proper error handling and displaying is the responsibility of the UI developer
* can be used for consumer logging and might be helpful in postponed analysis tasks
* the contents of such errors relate to underlying system and what J-S thinks is appropriate from the viewpoint of its layer

For example, an HTTP response carrying JSON problem details:
    HTTP/1.1 404 Not Found
    Content-Type: application/problem+json
    Content-Language: en
    Log-Context: <your value replied>
    {
      "type": "https://ki-journey-service.app.ose.sbb-cloud.net/sbb/v2/trips/{reconstructionContext}",
      "title": "No entity/resource found (in Backend)",
      "detail": "There was no trip found for your query arguments.",
      "instance": "/v2/trips/{reconstructionContext}"
    }

SBB staff: see also [error-handling](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-b2c/V2_Error-Handling.md)

#### 200 for emptyList, 404 for not found Object?
v2 is based mostly on HTTP GET (idempotent), therefore various variants are technically possible to signal "No hits found"!

The J-S team thinks it is best to return:
* 200 with an emptyList body "{}" for API's returning List<T> where no hits were found
* 404 with an optional Error body as described above if an expected object cannot be found, for e.g. /v2/trips/{reconstructionContext} which may not resolve

#### 400
Swagger annotations are heavily used to validate the API. In such cases no error-body is returned sometimes. Please check the Swagger-UI carefully.

### Errors given by APIM
If there is an error by the "security-layer" errors might result directly from APIM.

#### Too many calls
If the assigned plan limit is reached (requests/minute) an error like this might result:
    {"timestamp":"2021-03-17T06:39:51.654+0000","status":429,"error":"Too Many Requests","message":"Limit exceeded: Path: '/b2c/v2/info', Query: 'null', Method: 'GET'","path":"/b2c/v2/info"}

## J-S v3 (TODO coming soon)
**Problem** JSON object, see [Zalando Problem schema](https://opensource.zalando.com/problem/schema.yaml)

[HttpStatus](https://opensource.zalando.com/restful-api-guidelines/#150)
