# Project brief

## The question

Which health facilities in the Kaï-Kaï, Vélé and Yagoua health districts, Far North Cameroon, were flooded or cut off during the 2024 and 2025 flood seasons, how many children under five lost walking access to care as a result, and did this change between the two years?

The question breaks down into four parts:

1. Which health facilities were inside the flood extent in 2024 and in 2025, and for roughly how long?
2. How many children under five lived in flooded areas in each season?
3. How many children under five were more than 60 minutes' walk from the nearest functioning health facility during the flood, compared with the dry season?
4. Which facilities and areas were affected in both years, and which in only one?

## Why this question

Flooding along the Logone river is recurrent in Mayo-Danay division. In 2024, close to 200,000 people were affected in the division, and floods returned in 2025. Humanitarian reports give figures at division or subdivision level. They do not say which health facilities were under water or which communities could not reach care. This project produces those estimates for three neighbouring health districts, using open data and methods that can be repeated each season.

## Study area

Three neighbouring health districts of Mayo-Danay division, Far North Region, Cameroon: Kaï-Kaï, Vélé and Yagoua. They form a continuous strip along the Logone floodplain (Kaï-Kaï borders Vélé, which borders Yagoua), and all three were flooded in 2024 and 2025.

| Health district | Health facilities (2024 census) |
|---|---|
| Kaï-Kaï | 4 |
| Vélé | 10 |
| Yagoua | 26 |
| Total | 40 |

A single district would have been too small. Kaï-Kaï alone has four facilities, which is not enough to compare two flood seasons. A continuous area of three districts also limits edge effects in the accessibility analysis, because people do not stop at district boundaries when they seek care. Facilities within 10 km outside the study area, including those of Maga health district, are kept in the AccessMod network for the same reason. They are not counted in the exposure results.

Maga health district (12 facilities) is left out of the main study area. Lake Maga and the SEMRY rice perimeters make it the hardest part of the corridor to interpret with radar. It can be added later if needed.

## Study period

- Flood seasons: 1 August to 30 November 2024, and 1 August to 30 November 2025.
- Radar reference: the same window in every year from 2017 to 2025, excluding the season being analysed.
- Dry-season scenario for accessibility: February to April, before the rains.

## The data I need

| Dataset | Use in the project | Source | Format, resolution |
|---|---|---|---|
| Sentinel-1 GRD (VV and VH) | Flood detection | Google Earth Engine, `COPERNICUS/S1_GRD` | Raster, 10 m |
| Sentinel-2 surface reflectance | Visual checks; digitising rice perimeters | Google Earth Engine, `COPERNICUS/S2_SR_HARMONIZED` | Raster, 10 m |
| JRC Global Surface Water | Mask of permanent and regular seasonal water | Google Earth Engine, `JRC/GSW1_4/GlobalSurfaceWater` | Raster, 30 m |
| Copernicus EMS activation EMSR772 | Validation of the 2024 flood map | https://mapping.emergency.copernicus.eu/activations/EMSR772 | Vector |
| National census of health facilities, 2024 | Location and type of every facility (CSI, CMA, hospitals) | General Census of Health Facilities (Recensement général des formations sanitaires), 2024, conducted by the Directorate of Care Organisation and Health Technologies (Direction de l'organisation des soins et des technologies sanitaires, DOSTS), Ministry of Public Health (MINSANTE), Cameroon | Point |
| Health district and health area boundaries | Study area; results per health area | Ministry of Public Health (MINSANTE) | Polygon |
| WorldPop age and sex structures | Under-five population (age classes 0-1 and 1-4) | https://hub.worldpop.org | Raster, about 100 m |
| Copernicus GLO-30 DEM | Slope mask; terrain for AccessMod | OpenTopography, https://portal.opentopography.org | Raster, 30 m |
| ESA WorldCover | Land cover and walking speeds in AccessMod | https://esa-worldcover.org | Raster, 10 m |
| Roads and tracks | Travel network in AccessMod | OpenStreetMap via QuickOSM (QGIS), `highway=*` | Line |
| Administrative boundaries | Map context | GADM, https://gadm.org/download_country.html | Polygon |
| CHIRPS monthly rainfall | Context only: local rainfall anomaly per season | https://data.chc.ucsb.edu/products/CHIRPS-2.0/africa_monthly/tifs/ | Raster, about 5 km |

## Method

### Step 1. Flood mapping (Google Earth Engine)

I use Sentinel-1 images from a single relative orbit, apply a speckle filter, and compare each image in the flood season with the reference median for the same weeks. Pixels that become much darker than the reference are classified as water, with the threshold set by Otsu's method. I then remove permanent water (JRC), the digitised SEMRY rice perimeters, and slopes steeper than 5 %.

This step produces three layers per season: maximum flood extent, the number of acquisitions in which each pixel was flooded, and a layer of pixels flooded in both 2024 and 2025.

The 2024 map is validated against the EMSR772 flood delineation, using the acquisition dates closest to the Copernicus images. I report overall accuracy and the omission and commission errors.

### Step 2. Exposure

- Health facilities: a facility counts as flooded if a 150 m buffer around its point intersects the flood extent. The buffer allows for GPS error. For each flooded facility, I estimate how long it was under water from the number of consecutive flooded acquisitions.
- Children under five: the WorldPop under-five raster is summed inside the flood extent, by health area.

### Step 3. Accessibility (AccessMod)

I run two walking scenarios with the same land cover, road network and speeds:

- Dry season: all facilities in the census are functioning.
- Flood: flooded areas are impassable, and flooded facilities are removed.

The output is the number of children under five more than 60 minutes' walk from a functioning facility in each scenario, by health area. The difference between the two scenarios is the flood effect.

### Step 4. Comparison between 2024 and 2025

I compare the lists of flooded facilities, the under-five counts and the accessibility results between the two seasons.

## Expected outputs

- Flood extent and flood frequency maps for 2024 and 2025
- A table of health facilities with their flood status and estimated days flooded in each season
- Under-five population exposed and under-five population losing access, by health area and by year
- The Google Earth Engine script and the AccessMod project, so that the analysis can be rerun for a new season

## Limitations and known biases

This is a volunteer training project with no field validation. The results are estimates, and the biases below will be reported with them.

- The radar reference contains flood years, so moderate floods may be underestimated.
- Sentinel-1 detects open water well but misses water under dense vegetation, and buildings can create false signals.
- Sentinel-1C was launched in December 2024, so 2025 may have more acquisitions than 2024. Flood duration is only comparable between years if the same number of acquisitions is used.
- Much of the Logone's flow comes from upstream catchments outside the study area. Local rainfall (CHIRPS) therefore explains only part of the flooding, and I use it as context, not as a predictor.
- WorldPop gives modelled residential population before the flood. It does not account for people displaced to relocation sites.
- The 2024 census does not reflect facilities opened, closed or damaged after it was carried out. The accuracy of its coordinates is not known.
- The AccessMod walking speeds are assumptions, and travel by boat is not modelled.

## Out of scope

- Hydraulic or hydrological flood modelling. I will add it only if remote sensing proves insufficient.
- Schools. I dropped them because no reliable, up-to-date school location data exists for the area.
- Field data collection.
