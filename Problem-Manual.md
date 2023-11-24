# Problems
**Error-handling** by Journey-Service (J-S) means, each API will return a **[`Problem`](https://developer.sbb.ch/apis/journey-service/documentation) object in a failure case with further description**.

**Contact <journey-service@sbb.ch>** for further support about a specific `Problem` **if you don't find the answer below**.

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
     "status": 400
     "type": "<explanation>",
     "title": "Not supported: EVA",
     "detail": "The underlying system maintains UIC station codes only.",
     "instance": "v3/places"
    }
    
Remarks:
* J-S extension Problem::traceId (makes it easier to find logged references)

See also:
* **Problem** JSON object, see [Zalando Problem schema](https://opensource.zalando.com/problem/schema.yaml)
* [HttpStatus](https://opensource.zalando.com/restful-api-guidelines/#150)

## J-S HttpStatus explained

Header values:
* `Content-Type` is set to `application/problem+json`.
* `Content-Language` is in most cases set to `en` (however might come in rare cases translated).
* `Request-ID` The HTTP request ID can help you diagnose problems by correlating log entries for a given web request across many log files, which otherwise would not contain a common piece of information. Your value in the request should be replied in the J-S response, even in Problem case.

Problem properties:
* In most cases the `title` and `detail` will contain a usefule hint.
* `instance` should point to the J-S API involved.


For example, an HTTP response carrying JSON `Problem`:
    HTTP/1.1 404 Not Found
    Content-Type: application/problem+json
    Content-Language: en
    Request-ID: ClientId_abc_UseCase27
    {
      "status": 404
      "type": "<pointer to this page>",
      "title": "No entity/resource found (in Backend)",
      "detail": "There was no matching Trip found for your query arguments.",
      "instance": "/v3/trips/{id}"
    }

### 200 OK
#### V3
J-S was able to return a proper single Object.

Be aware:
* For v2 the following convention was made historically: 200 with an emptyList body "[]" for API's returning List<T> where no hits were found.

### 400 Bad Parameter
J-S considers this as a caller side problem.
    
Remark:
* **Check the OpenAPI description on each API and its Parameters.** We spend a lot of effort to improve the J-S Service-Contract, which is relevant for validations.
* Swagger-Users may not even reach the J-S Backend (in such cases no error-body is returned sometimes. Please check the Swagger-UI carefully.)

### 404 Object not found
If an expected object cannot be found, for e.g. /v3/trips/{id} which may not resolve    
    
### 500 Internal Server Error
    
### 501 Not Implemented
Possible reasons:
* Some early INCUBATOR feature is not implemented but forecasted for experimental reasons.
    
Please keep patient, might work with a next release of J-S.
    
### 502 Bad Gateway
Possible reasons:
* Send originally by underlying system.
* J-S considers a used Backend to have some kind of developer fault.   
    
### 503 Service Unavailable
Since J-S has a complicated integration-architecture, various Network/IP, Broken-Pipe or Backend-System faults may occur by chance.

Often a retry after a short time might solve the problem.
    
### 504 Timeout
J-S defines generally a timeout of about 30s for any underlying backend requests, however some routing requests might be tricky for the underlying router and might take longer.
    
Besides timeouts declared by underlying systems are out of control by J-S.
    
### Errors given by APIM
If there is an error by the "security-layer" errors might result directly from APIM (J-S will be behind and therefore not be the direct source of the Problem).

#### Too many calls
If the assigned plan limit is reached (requests/minute) an error like this might result:  
    {"timestamp":"2021-03-17T06:39:51.654+0000","status":429,"error":"Too Many Requests","message":"Limit exceeded: Path: '/b2c/v2/info', Query: 'null', Method: 'GET'","path":"/b2c/v2/info"}
