# Welcome to Journey-Service

This is a short introduction for new consumers to the SBB Journey-Service.

# About
The Journey-Service is *a stateless RESTful web-service for public transportation routing* as given in SBB Journey-Planner applications such as https://www.sbb.ch/ or *SBB Mobile App*. This is our strategic service-contract for timetable based routing enquiries for SBB internal consumers or accredited partners.

This document should help you through the registration process and provide all information needed to integrate the Journey-Service capabilities. 
* Access has to be approved by SBB and is provided by SBB developer portal by an API Management Platform. The registering process is described below.
* After successful registration we expect you develop and test againt INTegration before you run your application against PRODuction environment.

Please study this information carefully!

Contact/support: **journey-service@sbb.ch**


# Registration process
The registration consists of *two steps*:
* **fill in a questionnaire** and
* **register at the API-Management (2 separate credentials: https://developer.sbb.ch/home PRODuction, https://developer-int.sbb.ch/home INTegration)**

## Step 1: Completion of the questionaire
==**You can find the Questionnaire [here](https://sbb.sharepoint.com/:w:/r/teams/297/Oeffentlich/S3_Programm/Anwendungen/Oeffentlich/KIP/Journey-Service/Operation%20Management/Consumer-Mgmt/Questionnaire-ConsumerRegistration.docx?d=w79205a803e234d4ca190dbed259ed1ed&csf=1&e=BXEkdk).**== 
 
The questionnaire contains three sections:
1. company/personal information (generally we do not grant access to private persons (such as trial users, students,..) or non-SBB related companies,..)
2. journey-planner use cases by your application. Please think about a unique application-name, to be distinguished by SBB devOps team ("My Journey-Service-Client" would be a bad sample).
3. technical information (non functional requirements)

In the first section we ask you to provide **electronic contact information**, such as telephone number and e-mail to contact (see section 1.3 of the questionnaire) to get in touch with your officials or devOps members. In addition we need a general mail (e.g. team mailbox) to which we can send our release notes, which will provide you with an overview of new functionalities (see section 1.4 of the questionnaire) or address breaking  changes.

In the second section we need detailed **information about your application type** (for e.g. backend, browser or mobile App) and what part of the Journey-Service contract you are interested in (see section 2.1 of the questionnaire) and what kind of **use cases** you are going to implement (see section 2.4 questionnaire).

In the third section we need **technical information**. We need this information to assess the integration-impacts of your application related to Journey-Service or its underlying architecture (see section 3.1 of the questionnaire).
To gain a better understanding on the capabilities provided by Journey-Service, please consult the swagger-apidoc for detailed information about request parameters or response models (see https://developer.sbb.ch/apis/journey-service/documentation) and select your desired APIs (see section 3.2). 

### Step 2: Signing-up on the specific usage plan on the developer site 

If you are ready to sign-up, please visite the site developer-sbb for [PROD](developer.sbb.ch) or [INT](developer-int.sbb.ch) to sign-up for the integration. 
See page https://developer.sbb.ch/apisjourney-service.sbb.ch to find further information for the registration of the API Management. Different **usage plans** are available, which differ in the number of requests/minute per API.
Note: integrate the INT-environment first and afterwards the PROD environment.  

For detailed information on getting around on the developer-site, please read the available information in section **Finding your way around on the developer site***  carefully. 

### Step 3: Submitting the questionnaire to SBB
When the questionnaire is finalized, please *convert it into a PDF* and send it to journey-service@sbb.ch with the title *“New Consumer Registration request- <YOUR APPLICATION NAME>”*.

### Step 4: Wait for the final approval by SBB
It might take some time to validate and decide access for Journey-Service on our side (max. 2 weeks). We will communicate asap and inform you about further steps that need to be taken to go live with Journey-Service. 
Please note that you need to apply separately for each of your journey-planning applications, which means that you need an individual questionnaire for each of the services that you would like to integrate. 

 
# Developing your consumer-application

## Testing token mechanism APIM
Use Postman or the like to get a bearer token and request a query (/v2/info is any easy API without request parameters)

## Technical Details 
A good start is our Developer-Manual (see https://github.com/SchweizerischeBundesbahnen/journey-service-b2c/blob/master/Developer-Manual.md).

# Operation
## RSS-feed
To stay up to date about new features or occurring disturbances we highly advise you to use the RSS feed. Please follow the following instruction to insert the RSS Feed of the Journey-Service Blog: [Instruction RSS Feed](RSS%20Subscription-Instruction.pdf)







   
