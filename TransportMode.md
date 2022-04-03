# TransportMode Overview

The underlying Public-Transportation Router may distinct 10 Product categories which are described by **SBB P Data-Management** below (Data may change over time, consider this as guideline and not as current exact match).

Important:
* relates to [v580 de:06 Harmonisierung Verkehrsmittel](https://www.allianceswisspass.ch/de/tarife-vorschriften/uebersicht/V580/Produkte-der-V580-FIScommun-1) resp.  [FIScommun 06 Harmonisierung Verkehrsmittel (download)](https://www.allianceswisspass.ch/de/asp/Downloadsindex.php?section=downloads&download=14462)
* relates to Opentransportdata.swiss [**Transportmodes** (de:Verkehrsmittel) and **Transport Subomodes**](https://opentransportdata.swiss/de/dataset/verkehrsmittellisten) which implements v580 de:Verkehrsmittel
* relates to [**Transport Mode** and **Service-Brands**](https://unioninternationalcheminsdefer.github.io/OSDM/spec/catalog-of-code-lists/) resp. [Service Brand code List](https://uic.org/passenger/passenger-services-group/article/service-brand-code-list) also related to TAP-TSI, Merits.

## Routable Product-categories
Allocation to the different classes:

### Train categories (NeTEx "rail", J-S VehicleMode "TRAIN")
v580 de:Verkehrsträger Schiene

#### Class "ICE/TGV/RJX" (SBB class=0)
Contains Transport "Service-Brands":
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

#### Class "EC/IC" (SBB class=1)
Contains Transport Submodes:
* EC - EuroCity
* IC - InterCity
* ICN - IC-tilting train
* IN - InterCityNight
* TAL - Talgo

#### Class "IR/PE" (SBB class=2)
Contains Transport Submodes:
* IR - InterRegio
* PE - PanoramaExpress

#### Class "RE" (SBB class=3)
Contains Transport Submodes:
* AIR - aircraft
* CAT - City Airport Train
* D - Fast train
* IRE - Interregio-Express
* RB - Regionalbahn
* RE - RegioExpress

#### Class "S/SN/R" (SBB class=5)
Contains Transport Submodes:
* E - Semi fast train
* R - Regio
* S - Urban train
* SN - Night-urban train
* STB - Stadtbahn
* TER - Train Express Regional

#### Class "Car Train/Special event train" (SBB class=8)
Contains Transport Submodes:
* AG - Agencytrain
* ARZ - Car-carrying train
* EXT - Special train
* UEX - UrlaubsExpress
* WB - Westbahn
* ZUG - Train category unknown

### Tram category (NeTEx "tram") 
v580 de:Verkehrsträger Tram

#### Class Tram (SBB class=9)
* T - Tramway

(At SBB might contain METRO as well.)

### Metro category (NeTEx "metro")
v580 de:Verkehrsträger Metro

(Not available yet, see "Tram")

#### Class Metro (SBB not supported as own class)
* M - Metro
	
#### Class Cog railway
v580 Verkehrsmittel Zahnradbahn

By definition **use category Funicular** to search for such journeys within Journey-Service.

### Road categories (NeTEx "bus")
v580 de:Verkehrsträger Strasse

#### Class "Bus" (SBB class=6)
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

#### Class "Taxi"
(Not available yet, see "Bus")

### Cableway (NeTEx "cableway, funicular")
v580 de:Verkehrsträger Seil(-bahn)

#### Class Cableway/Funicular (SBB class=7)
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

#### Class "Ship" (SBB class=4)
Contains Transport Submodes:
* BAT - Ship
* FAE - Ferry-boat
* KAT - Katamaran

### Air category
v580 de:Verkehrsträger Luft

(Not available yet)
