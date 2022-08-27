# Solar-estimator
Solar estimator is a tool to assess the feasibility of placing solar panels on the roofs of municipal buildings. Ultimately, an API will be created that will give the ability to perform analysis for a selected area of Warsaw. Solar estimator takes advantage of open source data, although commercial software is being used (ArcGis Pro). This repository is a showcase of project's progress, current state as well as plans for the future. 

The project is carried out by members of Smart City Student Association located at Warsaw University of Technology.

## Goals
- Incentive to invest in solar panels
- Popularization of renewable energy sources
- Partial replacement of an energy audit
- Make use of open source data
- Exchange knowledge and gain experience in developing APIs and small information systems

## Data
Footprints and features of buildings in Warsaw were fetched from [Land and building register (EGiB)](https://www.geoportal.gov.pl/dane/dane-ewidencyjne). In order to get the rooftops shape and aspect we processed Digital Terrain Model (DTM) tiles, that are available at [polish geoportal](https://mapy.geoportal.gov.pl/imap/Imgp_2.html?gpmap=gp0). The DTM tiles come as point clouds, which are a result of aerial lidar scan. Resolution of these point clouds is about 12 points per square meter. Point clouds were filtered to get rid of outliers and abnormal measurements and then saved in PostgreSQL database. 

Additionally, automatically generated solar radiations maps were verified against measurement results provided by [the City Hall of Warsaw](http://mapa.um.warszawa.pl/mapaApp1/mapa?service=mapa_oze&L=en&X=7500996.700511402&Y=5787301.194500495&S=12&O=0&T=402180041a0000x01&komunikat=off). 

![image](https://user-images.githubusercontent.com/50464859/144933003-470858c5-34d7-4471-b92e-5f48164b47db.png)

*Footprint example: Warsaw University of Technology, the main campus*

## How does it work?
### Evaluating a rooftop
Evaluation of a rooftop is based on 3 main criteria:
- the aspect of a rooftop
- the slope of a rooftop
- the insolation of a rooftop

The combination of these criteria allows a fairly good assessment of what percentage of the roof area is suitable for a photovoltaic investment, but they do not provide information on the exact number of panels and their potential location on the roof. That is why we introduced an algorithm which optimizes the process of installing solar panels.

### Panel location optimization
Panel location optimization algorithm finds the optimal number of panels that can be installed on a rooftop and finds their position. We took into consideration a size of the panel, cost of installing one and time after which the investment would be profitable.
 
The algorithm takes a 2D list representing the surface on which we are considering installing solar panels. Each index in the list corresponds to small part (e.g. 2.5 square meters, but it depends on the data resolution) of the considered surface, and each value represents a certain level of solar radiation. The algorithm searches the surface to find the sunniest areas, which potentially bring the greatest return of invest (ROI). After finding the sunniest area, the algorithm continues the search for other areas and checks whether the ROI will increase after putting there solar panel. If so, the algorithm marks the position of another panel and searches for the next one. If the algorithm does not find any more profitable areas to put the panels, it ends its work.

![surface](https://user-images.githubusercontent.com/45266568/182501667-ddf80515-6eec-41d4-9503-f0e06bd1c7d6.PNG)

### Summary
Whole algorithm can be describe as below:
1. Create a processing extent that contains the input building footprint along with a small buffer area around it.
2. Clip the DTM to the processing extent.
3. Calculate solar radiation maps from the DTM.
4. Calculate aspect for each point of a surface.
5. Calculate slope (gradient) for each point of a surface.
6. Reclassify and combine the results from steps 3, 4 and 5.
7. Apply panel location algorithm to the result of step 6.

## Results
The result of the algorithm are numerical values, describing the number of panels and the power that can be generated.

![widok2d](https://user-images.githubusercontent.com/50464859/145687981-17f75e76-6ffa-462a-95b6-e6ef4c4c3957.PNG)
*Two buildings in the main campus of WUT: solar radiation map with orthophoto in the background*

![image](https://user-images.githubusercontent.com/50464859/145689939-6c641e8d-6126-4b74-a120-c309f4a866e3.png)
*Two buildings in the main campus of WUT: combination of slope, isolation and aspect. As you can see the results are fragmented and require further processing and refinement.*

| Building's ID | Number of solar panels | Area of the roof | Energy from solar panels |
| ------------- | ---------------------- | ---------------- | ------------------------ |
| 146510        | 70                     | 2372 m<sup>2</sup>         | 79 329 kWh/year          |

*Sample numerical results for one of the buildings in the picture above (note that these results are only example!).*


## API & Backend
The algorithm will be made available through the API. In addition, we will provide a user interface with a map where you can indicate the building for which the analysis will be performed. The source data (i.e. DTM and building footprints) will be placed in a database for better performance. Communication between endpoint and backend will be done via message broker, RabbitMQ.

![image](https://user-images.githubusercontent.com/50464859/143936376-16188782-da4f-46cc-962a-85cbb110ee85.png)
*Architecture diagram*

### Demo 
![FotoDemo](https://user-images.githubusercontent.com/45266568/182405527-dbf1effc-5f18-47a3-9dbc-31e6a4f57123.GIF)
*Demo of gui and api behind user interface for only one building*

## Further development
- [ ] Prepare the first stable version of the API
- [ ] Improve the algorithm results (better visualization and reliability)
- [ ] Add new criteria to the analysis (e.g. taking into account the material the roof is made of)
- [ ] Replace ArcPy with open source software


## Similar works
- [Project Sunroof](https://sunroof.withgoogle.com)


## Contributors
- Antoni Gołoś ([MrSquidward](https://github.com/MrSquidward)) - project coordinator
- Ola Jamróz ([Yamroza](https://github.com/Yamroza))
- Kacper Tomczykowski ([Tomczykowski](https://github.com/Tomczykowski))
- Maciek Dąbkowski ([dawerte](https://github.com/dawerte))
- Mateusz Czarnecki ([czaacza](https://github.com/czaacza))
