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
Building footprints and attributes were fetched from [Land and building register (EGiB)](https://www.geoportal.gov.pl/dane/dane-ewidencyjne). Files in shapefile format are used by the algorithm in order to detect buildings' borders. Digital Terrain Model (DTM) tiles were downloaded from [geoportal](https://mapy.geoportal.gov.pl/imap/Imgp_2.html?gpmap=gp0) via QGis as .laz files. They represent lidar data as a point cloud. All of these were filtered to get rid of outliers and abnormal records. Necessary data is stored in PostgreSQL database. Additionally, the generated (for the sake of this algorithm) solar radiations maps were verified against measurement results provided by [the City Hall of Warsaw](http://mapa.um.warszawa.pl/mapaApp1/mapa?service=mapa_oze&L=en&X=7500996.700511402&Y=5787301.194500495&S=12&O=0&T=402180041a0000x01&komunikat=off). 

r![image](https://user-images.githubusercontent.com/50464859/144933003-470858c5-34d7-4471-b92e-5f48164b47db.png)

*Footprint example: Warsaw University of Technology, the main campus*

## Algorithm
### Calculating solar-radiation
The algorithm is based on 3 main criteria:
- the aspect of the rooftop
- the slope of the rooftop
- the insolation of the rooftop

The combination of these three criteria allows a fairly good assessment of what percentage of the roof area is suitable for a photovoltaic investment, but they do not provide information on the exact number of panels and their potential location on the roof. After certain areas are excluded (e. g. roofs with northern exposure), additional steps are required, such as object clustering and spatial optimization as we want to achieve maximum power with minimum number of panels. The result of the algorithm are numerical values, describing the number of panels and the power that can be generated and visualizing the arrangement of the panels.

The algorithm is implemented in Python 3.10 using the tools available in the Arcpy library. 
Steps of the algorithm:
1. Create a processing extent that contains the input building footprint along with a small buffer area around it.
2. Clip the DTM to the processing extent.
3. Calculate solar radiation maps from the DTM.
4. Calculate aspect for each point of a surface.
5. Calculate slope (gradient) for each point of a surface.
6. Reclassify and combine the results.
7. Group the pixels and average the amount of solar radiation falling on them.

### Panel optimization

#### Preliminary assumptions

Having a surface composed of points containing a numerical representation of the solar radiation level, we have created an algorithm which optimizes the process of installing solar panels. The algorithm finds the number of panels that are profitable to install and finds their position on the received surface. For the algorithm to work, we assume that the period of calculating savings, the size of one panel, and the cost of installing one panel are fixed.

#### Operating principle

The algorithm takes an object representing the surface on which we are considering solar panel installation. The object is made up of points, each of which represents a certain level of solar radiation (0–100). It searches the surface to find the sunniest areas, which potentially bring us the greatest savings. After finding the sunniest area, the algorithm continues the search for other areas and checks whether the savings resulting from their position will increase. If so, the algorithm marks the position of another panel and searches for the next one. If the algorithm does not find any more profitable areas to put the panels, it ends its work.

![surface](https://user-images.githubusercontent.com/45266568/182410328-2130bbd1-a1a7-4acc-9f75-96e7e4cec03e.png)

## Results
![widok2d](https://user-images.githubusercontent.com/50464859/145687981-17f75e76-6ffa-462a-95b6-e6ef4c4c3957.PNG)
*Two buildings in the main campus of WUT: solar radiation map with orthophoto in the background*

![image](https://user-images.githubusercontent.com/50464859/145689939-6c641e8d-6126-4b74-a120-c309f4a866e3.png)
*Two buildings in the main campus of WUT: raster with the final results. As you can see the results are fragmented and require further processing and refinement.*

| Building's ID | Number of solar panels | Area of the roof | Energy from solar panels |
| ------------- | ---------------------- | ---------------- | ------------------------ |
| 146510        | 70                     | 2372 m<sup>2</sup>         | 79 329 kWh/year          |

*Sample numerical results for one of the buildings in the picture above (note that these results are only example!).*

## DEMO
![FotoDemo](https://user-images.githubusercontent.com/45266568/182405527-dbf1effc-5f18-47a3-9dbc-31e6a4f57123.GIF)
*Gif showing how the program works*

## API & Backend
The algorithm will be made available through the API. In addition, we will provide a user interface with a map where you can indicate the building for which the analysis will be performed. The source data (i.e. DTM and building footprints) will be placed in a database for better performance. Communication between endpoint and backend will be done via message broker, RabbitMQ.

![image](https://user-images.githubusercontent.com/50464859/143936376-16188782-da4f-46cc-962a-85cbb110ee85.png)
*Architecture diagram*

## Further development
- [ ] Prepare the first stable version of the API
- [ ] Insert all source data in the database
- [ ] Improve the algorithm results (better visualization and reliability)
- [ ] Add new criteria to the analysis (e.g. taking into account the material the roof is made of)
- [ ] Replace ArcPy with pyQGIS


## Similar works
- [Project Sunroof](https://sunroof.withgoogle.com)


## Contributors
- Antoni Gołoś ([MrSquidward](https://github.com/MrSquidward)) - project coordinator
- Ola Jamróz ([Yamroza](https://github.com/Yamroza))
- Kacper Tomczykowski ([Tomczykowski](https://github.com/Tomczykowski))
- Maciek Dąbkowski ([dawerte](https://github.com/dawerte))
- Mateusz Czarnecki ([czaacza](https://github.com/czaacza))
