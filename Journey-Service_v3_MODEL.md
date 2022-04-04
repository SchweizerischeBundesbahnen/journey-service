# Journey-Service (J-S)

This is about the **implemented routing exchange model** provided by **J-S v3**.

## Routing Standards

The main reference is Transmodel [**TRM6-v56** (downloads and tutorials)](https://www.transmodel-cen.eu/downloads/), though Transmodel is rather a **conceptual specification** and the J-S Team did not find a satisfying reference-implementation yet. 

However our team is convinced to take this as the main guideline for J-S v3, even with the risk, that might have added some simplifications or added our own flavor (based at Journey-Planner experience within SBB P division in the last 5 years with plenty of strategic consumers).
### Transmodel



**Standards** considered:
* **CEN EUROPEAN REFERENCE DATA MODEL FOR PUBLIC TRANSPORT INFORMATION [EN 12896](https://en.wikipedia.org/wiki/Transmodel)**
* J-S v3 is heavily based on the **conceptual [Transmodel](https://www.transmodel-cen.eu/) TRM6-v56**. See [Model download, Glossary, Tutorials](https://www.transmodel-cen.eu/downloads/), mainly on the following parts:
    * Part 1: Commons Concepts
    * Part 2: Public Transport Network
    * Part 3: Timing Information and Vehicle Scheduling
    * Part 4: Operations and Monitoring Control
    * (Part 5: Fare Management)
    * Part 6: Passenger Information
    * (Part 7: Driver Management)
    * (Part 8: Management Information & Statistics)

Beyond Transmodel Standard yet:
* See [Tutorial Part 8, chapter 6](https://www.transmodel-cen.eu/wp-content/uploads/2015/01/TUTORIAL_Part8_v2.1-1.pdf)
    * Service safety: this function considers all the information to evaluate the safety of the PT service in terms of accidents occurred, etc;
    * Service pollution emissions: this function considers all the information to evaluate environment impacts due to the emission of pollutants;

Comparison to [OpenJourneyPlaner OJP](https://www.transmodel-cen.eu/ojp-standard/ojp/):
* J-S v3 provides similar functionality as OJP (places, trips, ..), but J-S v3 references the newer terminology and model in TRM6-v56 and is simplified according to SBB Personenverkehr experience, therefore bridging/adapting between these 2 API sets is possible but needs some mapping effort.
* J-S is rather SBB private, but Swiss SKI OJP is for public use, see [OJP Switzerland](https://opentransportdata.swiss/de/cookbook/open-journey-planner-ojp/).
* Classic Standards in J-S are more abstracted, where the following are more obvious for e.g. by OJP schema references or namespaces:
    * [NeTex](https://www.transmodel-cen.eu/netex-standard/): Network Timetable Exchange (Fare Management & Passenger Information)
    * [Siri](https://www.transmodel-cen.eu/siri-standard/): Realtime Information
    * [IFOPT](https://www.transmodel-cen.eu/ifopt-standard/): Identification of fixes Objects in Public Transport

Comparison to [ODSM](https://app.swaggerhub.com/apis-docs/schlpbch/uic-90918_10_osdm/1.4.0)
* ODSM is an OJP orientied Ticketing specification among european members and UIC.
* ODSM also has Journey-Planner API's with to focus to distribute international tickets (for e.g. among brokers).
* ODSM is a planned facade in front of NOVA (Switzerland).
