
Zum Ende der Metadaten springen
Erstellt von Känel Roland (IT-SCP-BP-VTP-MCS), zuletzt geändert von Hirzel Peter (IT-SCP-BP-VTP) am Feb 19, 2020Zum Anfang der Metadaten
Weitere Angaben
API Management
Release Notes
Zuständig: journey-service@sbb.ch

Weitere Angaben
How to: J-S::Client richtig anbinden
J-S::B2C Consumer Registration SBB API-Management
J-S::B2C Developer Manual
SBB interne Doc: https://confluence.sbb.ch/pages/viewpage.action?pageId=912509773

API Management
INTegration: https://developer-int.sbb.ch/api/76/journey-service

PRODuction: https://developer.sbb.ch/api/33/journey-service

journey-service-boot\pom.xml

<!-- Api Management, see:
       Involves Batch-job that runs every 15s
       [INFO] MonitoringService - Pushing Metrics to Prometheus
       [INFO] SyncCollector - collectiong sync metrics
      -->
<dependency>
   <groupId>ch.sbb.integration.api</groupId>
   <artifactId>apim-adapter</artifactId>
   <version>1.2.1</version>
</dependency>


Release Notes
S. https://code.sbb.ch/projects/KI_FAHRPLAN/repos/journey-service/browse/ReleaseNotes.md
