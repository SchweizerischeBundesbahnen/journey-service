#Using OpenAPi 3 ApiClient

## Generating your own ApiClient to access the REST-API

Usually each Consumer will implement its own caller client classes for out of the downloaded **json-definitions** [API Specification File](https://developer.sbb.ch/apis/journey-service/documentation) for:
* Model deserialization
* Request support

In case you are using a framework, please check:
* support of Header-fields (needed for e.g. "v2/trips Scroll-Context")
* OffsetDateTime handling especially for journey-planner aspects:
    * <ins>We highly recommend requesting serialized instances as **UTC with a recognizable offset**</ins> for e.g. "2020-12-18T14:55:00**+01:00**" or "2020-07-21T14:55:00**+02:00**" (where both correspond to Switzerland once in wintertime and once in summertime)
    * If you use "Z"(ulu) notation the same samples above would serialize to "2020-12-18T13:55:00**Z**" or "2020-07-21T12:55:00**Z**". In such a case Journey-Service has no chance to determine the intended timezone and as a fallback we interprete all such data as "Europe/Zurich" timezone and might lead to unexpected results on the caller side!

Remark:
* For internal consumers the [JSON definition may also be downloaded here]8https://journey-service.sbb-cloud.net/v3/api-docs/journey-service-api-management) (instead of developer.sbb.ch) 
* We customize our Jackson-Mapper for OffsetDateTime like:
  ObjectMapper mapper = Jackson2ObjectMapperBuilder.json().featuresToDisable(new Object[]{SerializationFeature.WRITE_DATES_WITH_ZONE_ID}).featuresToDisable(new Object[]{SerializationFeature.WRITE_DATES_AS_TIMESTAMPS}).featuresToDisable(new Object[]{DeserializationFeature.ADJUST_DATES_TO_CONTEXT_TIME_ZONE}).build();
* If you use the generated ApiClient declare something like this:

    ```
    @Bean
    public ApiClient apiClient() {
        //TODO replace by WebClient
        RestTemplate apiClientRestTemplate = new RestTemplate();
        apiClientRestTemplate.getInterceptors().add(new ApimRequestInterceptor(this));
        // make sure TIMEZONE offset is not Z(ulu) resp. UTC
        MappingJackson2HttpMessageConverter mappingConverter = new MappingJackson2HttpMessageConverter();
        mappingConverter.setObjectMapper(Jackson2ObjectMapperBuilder
            .json()
            .featuresToDisable(DeserializationFeature.ADJUST_DATES_TO_CONTEXT_TIME_ZONE)
            .build());
        apiClientRestTemplate.getMessageConverters().add(0, mappingConverter);
        ApiClient client = new ApiClient(apiClientRestTemplate);
        client.setBasePath(this.getEndpoint());
        return client;
    }
    ```
* Here our API generation with a Maven plugin:

    ```
            <plugin>
                <groupId>org.openapitools</groupId>
                <!-- see https://openapi-generator.tech/docs/generators/java/, https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator-maven-plugin-->
                <artifactId>openapi-generator-maven-plugin</artifactId>
                <version>5.2.1</version>
                <executions>
                    <execution>
                        <id>journey-service-client-v3</id>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                        <configuration>
                            <inputSpec>
                                ${project.basedir}/openapi3/api-docs.json
                            </inputSpec>
                            <generatorName>java</generatorName>
                            <!-- perhaps do not validate JSON/yml because of ServiceConfiguration "EcoMap" -->
                            <skipValidateSpec>false</skipValidateSpec>
                            <modelPackage>ch.sbb.ki.journeyservice.client.v3.model</modelPackage>
                            <apiPackage>ch.sbb.ki.journeyservice.client.v3.api</apiPackage>
                            <generateApis>true</generateApis>
                            <apisToGenerate>
                                ArchiveV3,PlacesV3,SchedulesV3,TripsRealtimeEventsV3,TripsV3,VehicleJourneysV3
                            </apisToGenerate>
                            <generateApiTests>false</generateApiTests>
                            <!--generateApiDocumentation></generateApiDocumentation-->
                            <!--modelNamePrefix>v3</modelNamePrefix-->
                            <!--modelNameSuffix></modelNameSuffix-->
                            <generateModels>true</generateModels>
                            <!--generateModelDocumentation></generateModelDocumentation-->
                            <modelsToGenerate>
                                Problem,TransportModeEnum,VehicleModeEnum,RealtimeModeEnum,NoticeAttributeEnum,PlaceResponse,Place,AddressPlace,StopPlace,PoiPlace,PoiCategory,Position,Point,LineString,Links,StopAttributeEnum,StopPlaceDetailedResponse,StopPlaceDetailed,TariffZone,Quay,VehicleMode,DatedVehicleJourneyResponse,DatedVehicleJourney,TripsByOriginAndDestinationRequestBody,TripsByLegRequestBody,TripMobilityFilter,PTViaReference,PTViaNotReference,PTViaNoChangeAtReference,LineReference,ScheduledStopPointReference,OptimisationMethod,TripResponse,Trip,TripStatus,TripSummary,Leg,PTRideLeg,ScheduledStopPoint,AccessLeg,AccessEnd,PTConnectionLeg,ConnectionEnd,AlternativeModeLeg,Operator,Notice,PTSituationMessage,EcoBalance,ServiceCalendar,OperatingPeriod,Direction,ServiceJourney,ServiceProduct,ServiceAlteration,DepartureResponse,Departure,ArrivalResponse,ArrivalV3,LinkedText,LinkedTextMap,TripSubscriptionRequestBody,SubscriptionPeriod,Hysteresis,TripSubscriptionResponse,TripSubscriptionStatusResponse,TripSubscriptionDeletionResponse,PaginationCursor,AccessibilityEnum,AlternateMatchEnum
                            </modelsToGenerate>
                            <generateModelTests>false</generateModelTests>
                            <generateSupportingFiles>true</generateSupportingFiles>
                            <!--Spring 5 WebClient + Jackson-->
                            <library>webclient</library>
                            <configOptions>
                                <!-- see https://openapi-generator.tech/docs/generators/java/ Java 8 native JSR310 (preferred for jdk 1.8+) - note: this also sets "java8" to true -->
                                <dateLibrary>java8</dateLibrary>
                                <!-- generated client argument order will follow exactly Java contract, therefore change of signature impacts generated client 1:1 -->
                                <sortParamsByRequiredFlag>false</sortParamsByRequiredFlag>
                                <booleanGetterPrefix>is</booleanGetterPrefix>
                                <!-- Probably no impact with Jackson: Make API response's headers case-insensitive. Available on okhttp-gson, jersey2 libraries -->
                                <caseInsensitiveResponseHeaders>true</caseInsensitiveResponseHeaders>
                            </configOptions>
                            <importMappings>
                                <importMapping>LocalTime=java.time.LocalTime</importMapping>
                                <importMapping>LocalDate=java.time.LocalDate</importMapping>
                            </importMappings>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
    ```

## journey-service-client (SBB staff only)

Be aware that the devOps team provides a **generated ApiClient to perform requests and map response models** for convenience reasons:
* SBB Bitbucket [journey-service-client](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-client)
* A [showcase](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-integration/src/test/java/ch/sbb/ki/journeyservice/showcase/client) demonstrates its usage with minimal developing effort.

SBB Artifactory dependency:

    <dependency>
        <groupId>ch.sbb.ki.journey-service</groupId>
        <artifactId>journey-service-client</artifactId>
        <version>${journey-service.version}</version>
    </dependency>

Important versions:
* until v2.19.12: Swagger2 ApiClient
* from v2.20.x OpenAPI 3 ApiClient (consumers using Swagger2 ApiClient will have some code adaptions when upgrading)

However, if the provided client does not work for you (for e.g. wrong Spring/SpringFox/Swagger-Annotation versions, ..) you may generate it yourself according to the [json-definitions -> Download](https://developer-int.sbb.ch/apis/journey-service/documentation) related to the given contract:
1. Create a [TestCase](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-boot/src/test/java/ch/sbb/ki/journeyservice/web/SwaggerDefinitionModelGeneratorTest.java) instantiating J-S and download the swagger-json-definition, for e.g. like
2. Generate client (we use `swagger-codegen-maven-plugin` in [pom.xml](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-client/pom.xml) for e.g.)
3. Be aware of known bugs we fixed: [OffsetDateTime problem](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/commits/312479191d4d500922a533fbdd4e357d7d139e21), [Encoding](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/commits/b7934e8c54ef2c83b7a9a8d03d38ff0a78697b4d)
