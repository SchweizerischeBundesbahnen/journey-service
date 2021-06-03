# ProductCategory Overview

The underlying Public-Transportation Router may distinct 10 Product categories which are described by **SBB P Data-Management** below (Data may change over time, consider this as guideline and not as current exact match).

Important:
* relates to v580 [FIScommun 06 Harmonisierung Verkehrsmittel](https://www.allianceswisspass.ch/de/asp/Downloadsindex.php?section=downloads&download=14462)
* relates to Opentransportdata.swiss [**Transportmodes** (de:Verkehrsmittel) and **Transport Subomodes**](https://opentransportdata.swiss/de/dataset/verkehrsmittellisten)

## Routable Product-categories
Allocation to the different classes:

### Train categories (NeTEx "rail")
v580 de:Verkehrsträger Schiene

#### Class "ICE/TGV/RJX" 
Contains Transport Submodes:
* ARC - Arco
* ATR - Altaria
* AVE - Alta Velocidad ES
* EM - Euromed
* EN - EuroNight
* EST - Eurostar
* FA - Frecciargento
* FB - Frecciabianca
* FR - Frecciarossa
* ICE - InterCityExpress
* NJ - nightjet
* NZ - Night train
* RJ - Railjet
* RJX - railjet xpress
* TGV - Train à grande vit.
* THA - Thalys
* TLK - Twoje Linie Kolejowe
* UUU - Unbekannte Art
* X2 - X2000 tilting train

#### Class "EC/IC"
Contains Transport Submodes:
* EC - EuroCity
* IC - InterCity
* ICN - IC-tilting train
* IN - InterCityNight
* TAL - Talgo

#### Class "IR/PE"
Contains Transport Submodes:
* IR - InterRegio
* PE - PanoramaExpress

#### Class "RE"
Contains Transport Submodes:
* AIR - aircraft
* CAT - City Airport Train
* D - Fast train
* IRE - Interregio-Express
* RB - Regionalbahn
* RE - RegioExpress

#### Class "S/SN/R"
Contains Transport Submodes:
* E - Semi fast train
* R - Regio
* S - Urban train
* SN - Night-urban train
* STB - Stadtbahn
* TER - Train Express Regional

#### Class "Car Train/Special event train" 
Contains Transport Submodes:
* AG - Agencytrain
* ARZ - Car-carrying train
* EXT - Special train
* UEX - UrlaubsExpress
* WB - Westbahn
* ZUG - Train category unknown

### Tram category (NeTEx "tram")
v580 de:Verkehrsträger Tram

#### Class Tram
* T - Tramway

### Metro category (NeTEx "metro")
v580 de:Verkehrsträger Metro

#### Class Metro
* M - Metro
	
#### Class Cog railway
v580 Verkehrsmittel Zahnradbahn

By definition **use category Funicular** to search for such journeys within Journey-Service.

### Road categories (NeTEx "bus")
v580 de:Verkehrsträger Strasse

#### Class "Bus"
Contains Transport Submodes:
* B - Bus
* BN - Nightbus
* BP - Panorama bus
* CAR - National long-distance bus
* CAX - International long-distance bus
* EV - Replacement service
* EXB - Express bus
* KB - Minibus
* NB - Night-Bus
* RUB - On-call bus
* TX - Taxi

### Cableway (NeTEx "cableway, funicular")
v580 de:Verkehrsträger Seil(-bahn)

#### Class Cableway/Funicular 
Contains Transport Submodes:
* ASC - Lift
* CC - Rack-railroad
* FUN - Funicular
* GB - Gondola lift
* PB - aerial tramway
* SL - Chairlift

### ASC
v580 de:Verkehrsmittelkategorie Aufzug

(Not available yet)

### Water categories (NeTEx "water")
v580 de:Verkehrsträger Wasser

#### Class "Ship"
Contains Transport Submodes:
* BAT - Ship
* FAE - Ferry-boat
* KAT - Katamaran

### Air category
v580 de:Verkehrsträger Luft

(Not available yet)
