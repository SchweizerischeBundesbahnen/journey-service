# Problems
**Error-handling** by Journey-Service (J-S) means, each API will return a **`Problem` object in a failure case with further description** about the resulting error out of a caller request.

**Contact <journey-service@sbb.ch>** for further support about a specific `Problem`.

## Standard used

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
      "status": 404
      "type": "<pointer to this manual>",
      "title": "No entity/resource found (in Backend)",
      "detail": "There was no matching Trip found for your query arguments.",
      "instance": "/v3/trips/{id}"
    }

See also:
* **Problem** JSON object, see [Zalando Problem schema](https://opensource.zalando.com/problem/schema.yaml)
* [HttpStatus](https://opensource.zalando.com/restful-api-guidelines/#150)
* SBB staff: see also [error-handling](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-b2c/V2_Error-Handling.md)

#### 200 for emptyList (V2 DEPRECATED APIs only)

For v2 (mostly deprecated) the following convention was made historically:
* 200 with an emptyList body "{}" for API's returning List<T> where no hits were found

#### 400
J-S considers this as a caller side problem.    
    
Remark:
* **Check the OpenAPI description on each API and its Parameters.** We spend a lot of effort to improve the J-S Service-Contract, which is relevant for validations.
* Swagger-Users may not even reach the J-S Backend (in such cases no error-body is returned sometimes. Please check the Swagger-UI carefully.)

#### 404 Object not found
If an expected object cannot be found, for e.g. /v3/trips/{id} which may not resolve    
    
### 500
    
### 501
Possible reasons:
* Some early INCUBATOR feature is not implemented but forecasted for experimental reasons.
    
Please keep patient, might work with a next release of J-S.
    
### 502
Possible reasons:
* Send originally by underlying system.
* J-S considers a used Backend to have some kind of developer fault.   
    
### 503
Since J-S has a complicated integration-architecture, various Network/IP, Broken-Pipe or Backend-System faults may occur by chance.

Often a retry after a short time might solve the problem.
    
### 504
J-S defines generally a timeout of about 30s for any underlying backend requests, however some routing requests might be tricky for the underlying router and might take longer.
    
Besides timeouts declared by underlying systems are out of control by J-S.
    
### Errors given by APIM
If there is an error by the "security-layer" errors might result directly from APIM.

#### Too many calls
If the assigned plan limit is reached (requests/minute) an error like this might result:  
    {"timestamp":"2021-03-17T06:39:51.654+0000","status":429,"error":"Too Many Requests","message":"Limit exceeded: Path: '/b2c/v2/info', Query: 'null', Method: 'GET'","path":"/b2c/v2/info"}
