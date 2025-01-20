# TransportMode Overview

## About Standardisation
Important aspects:
* For Switzerland we consider **[v580 de:06 Harmonisierung Verkehrsmittel](https://www.allianceswisspass.ch/de/tarife-vorschriften/uebersicht/V580/Produkte-der-V580-FIScommun-1) resp.  [FIScommun 06 Harmonisierung Verkehrsmittel (download)](https://www.allianceswisspass.ch/de/asp/Downloadsindex.php?section=downloads&download=14462)** as master of TransportMode's and Transport-Submodes
* Opentransportdata.swiss [**Transportmodes** (de:Verkehrsmittel) and **Transport Subomodes**](https://data.opentransportdata.swiss/de/dataset/vm-liste) implements v580 TransportModes 
    * NeTEx related terms are also mapped within those files 
* [Transmodel](https://www.transmodel-cen.eu/) names `TransportMode` as **`VehicleMode`**
    * therefore **J-S v3 uses `VehicleMode`** as well (for e.g. in `PTRideLeg::mode`)
    * According to OJP als VehicleMode is used, for e.g. [SIRI:VehicleModeEnum](https://jmaerki.github.io/OJP/generated/OJP.html#ModeGroup)
* relates to [**Transport Mode** and **Service-Brands**](https://unioninternationalcheminsdefer.github.io/OSDM/spec/catalog-of-code-lists/) and [Service Brand code List](https://uic.org/passenger/passenger-services-group/article/service-brand-code-list) also related to **TAP-TSI, Merits**.

## Concrete TransportMode aka VehicleMode aka de:Verkehrsmittelkategorie and its sub-modes

v580 defines in general the following categories (de:Verkehrsträger):
* de:Verkehrsträger Schiene
* de:Verkehrsträger Strasse
* de:Verkehrsträger Seil(-bahn)
* de:Verkehrsträger Wasser
* de:Verkehrsträger Luft

Each category may have concrete TransportModes and its submodes.

The underlying SBB Public-Transportation router may distinct 10 product-categories (aka de:Gattungen) which are managed by **SBB P Data-Management** (Data may change over time, consider this mapping as a guideline and not as current exact match).


### Mode:Train (NeTEx "rail", J-S VehicleMode "TRAIN")
v580 de:Verkehrsträger Schiene

#### Submode "ICE/TGV/RJX" (SBB class=0)
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

#### Submode "EC/IC" (SBB class=1)
Contains Transport Submodes:
* EC - EuroCity
* IC - InterCity
* ICN - IC-tilting train
* IN - InterCityNight
* TAL - Talgo

#### Submode "IR/PE" (SBB class=2)
Contains Transport Submodes:
* IR - InterRegio
* PE - PanoramaExpress

#### Submode "RE" (SBB class=3)
Contains Transport Submodes:
* AIR - aircraft
* CAT - City Airport Train
* D - Fast train
* IRE - Interregio-Express
* RB - Regionalbahn
* RE - RegioExpress

#### Submode "S/SN/R" (SBB class=5)
Contains Transport Submodes:
* E - Semi fast train
* R - Regio
* S - Urban train
* SN - Night-urban train
* STB - Stadtbahn
* TER - Train Express Regional

#### Submode "Car Train/Special event train" (SBB class=8)
Contains Transport Submodes:
* AG - Agencytrain
* ARZ - Car-carrying train
* EXT - Special train
* UEX - UrlaubsExpress
* WB - Westbahn
* ZUG - Train category unknown

### Mode:Tram (NeTEx "tram", J-S v3 VehicleMode "TRAMWAY") 
v580 de:Verkehrsträger Tram

#### Submode "Tram" (SBB class=9)
* T - Tramway

(At SBB might contain METRO as well.)

### Mode:Metro (NeTEx "metro", J-S v3 VehicleMode "METRO") )
v580 de:Verkehrsträger Metro

(Not available yet, see "Tram")

#### Submode Metro (SBB not supported as own class)
* M - Metro
	
### Mode:Cog railway (NeTEx "??", J-S v3 VehicleMode "COG_RAILWAY")
v580 Verkehrsmittel Zahnradbahn

Remark:
* **Category Funicular** might be useful to search within Journey-Service.

### Mode: Bus (NeTEx "bus", J-S v3 "BUS")

#### Submode "Bus" (SBB class=6)
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

#### Submode "Taxi"
(Not available yet, see "Bus")

### Mode:Cableway (NeTEx "cableway, funicular", J-S v3 VehicleMode "CABLEWAY")

#### Submode Cableway/Funicular (SBB class=7)
Contains Transport Submodes:
* ASC - Lift
* CC - Rack-railroad
* FUN - Funicular
* GB - Gondola lift
* PB - aerial tramway
* SL - Chairlift

### Mode:ASC
v580 de:Verkehrsmittelkategorie Aufzug

(Not available yet)

### Mode:Ship (NeTEx "water", J-S v3 "SHIP")

#### Submode "Ship" (SBB class=4)
Contains Transport Submodes:
* BAT - Ship
* FAE - Ferry-boat
* KAT - Katamaran

### Mode:Air category
v580 de:Verkehrsträger Luft

(Not available yet)
