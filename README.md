# Solar-estimator
Solar estimator is a tool to assess the feasibility of placing solar panels on the roofs of municipal buildings. Ultimately, an API will be created that will give the ability to perform analysis for a selected area of Warsaw. Solar estimator takes advantage of open source data, although commercial software is being used (ArcGis Pro). This repository is a showcase of project's progress, current state as well as plans for the future. Source code will be released as soon as the first stable version will be finished. 

The project is carried out by members of Smart City Student Association located at Warsaw University of Technology.

## Goals
- Incentive to invest in solar panels
- Popularization of renewable energy sources
- Partial replacement of an energy audit
- Make use of open source data
- Exchange knowledge and gain experience in developing APIs and small information systems

## Data
Building footprints and attributes were fetched from [Land and building register (EGiB)](https://www.geoportal.gov.pl/dane/dane-ewidencyjne). Digital Terrain Model was downloaded manually for each tile from [geoportal](https://mapy.geoportal.gov.pl/imap/Imgp_2.html?gpmap=gp0). Additionally, the generated solar radiations maps were verified against measurement results provided by [the City Hall of Warsaw](http://mapa.um.warszawa.pl/mapaApp1/mapa?service=mapa_oze&L=en&X=7500996.700511402&Y=5787301.194500495&S=12&O=0&T=402180041a0000x01&komunikat=off). DTM consists of dozen .xyz files formatted as below:

    636615.00	484359.00	111.80
    636615.50	484359.00	111.88
    636616.00	484359.00	111.82
    636616.50	484359.00	111.85
    ...

XY coordinates are in the [EPSG:2180](https://spatialreference.org/ref/epsg/etrs89-poland-cs92/) spatial reference system. Building footprints are provided in shapefile format.


![image](https://user-images.githubusercontent.com/50464859/144933003-470858c5-34d7-4471-b92e-5f48164b47db.png)

*Footprint example: Warsaw University of Technology, the main campus*

## Algorithm


## Results
## API & Backend
## Further development
> QGIS instead of ArcGis
## Similar works
## Contributors
