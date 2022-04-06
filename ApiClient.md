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
* We customize our Jackson-Mapper for OffsetDateTime like:
  ObjectMapper mapper = Jackson2ObjectMapperBuilder.json().featuresToDisable(new Object[]{SerializationFeature.WRITE_DATES_WITH_ZONE_ID}).featuresToDisable(new Object[]{SerializationFeature.WRITE_DATES_AS_TIMESTAMPS}).featuresToDisable(new Object[]{DeserializationFeature.ADJUST_DATES_TO_CONTEXT_TIME_ZONE}).build();
* if you use the generated ApiClient declare something like this:

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

However, if the provided client does not work for you (for e.g. wrong Spring/SpringFox/Swagger-Annotation versions, ..) you may generate it yourself according to the [json-definitions](https://ki-journey-service.app.idefix.otc.sbb.ch/v2/api-docs?group=journey-service-api-2.0) related to the given contract:
1. Create a [TestCase](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-boot/src/test/java/ch/sbb/ki/journeyservice/web/SwaggerDefinitionModelGeneratorTest.java) instantiating J-S and download the swagger-json-definition, for e.g. like
2. Generate client (we use `swagger-codegen-maven-plugin` in [pom.xml](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/journey-service-client/pom.xml) for e.g.)
3. Be aware of known bugs we fixed: [OffsetDateTime problem](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/commits/312479191d4d500922a533fbdd4e357d7d139e21), [Encoding](https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/commits/b7934e8c54ef2c83b7a9a8d03d38ff0a78697b4d)
