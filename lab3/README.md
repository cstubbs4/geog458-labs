# Lab 3: Regional Trails and Terrain
Cal Stubbs · GEOG 458

**Live map:** [calstubbs_lab3-2.html](https://cstubbs4.github.io/geog458-labs/lab3/calstubbs_lab3-2.html)
(Guided PM2.5 map: [calstubbs_lab3-1.html](https://cstubbs4.github.io/geog458-labs/lab3/calstubbs_lab3-1.html))

![Independent map](screenshots/lab3-2.png)

## Question and audience
**Which King County regional trails are flat, and which climb?**

The map is for walkers, runners and cyclists, including people who use wheelchairs or ride with kids. It helps them choose a route by seeing how much each trail rises and falls, before they go.

## How the raster and vector work together
The map answers one question in one place, using two layers for the same people:

- **The elevation raster shows the landscape.** Green marks low ground near sea level: the shorelines of Puget Sound and Lake Washington, and the Duwamish, Cedar and Green River valleys. Yellow, orange and brown mark the hills and plateaus rising up to about 400+ m.
- **The trail lines show where people can actually go.** Each purple line is a King County regional trail. Clicking it opens a popup with its name, surface and owner.

Read together, the colors under each trail show its terrain. The Burke-Gilman, Cedar River and Interurban trails follow green shorelines and valleys, so they are mostly flat. Trails that cross from yellow into orange or brown climb onto higher ground. Neither layer answers the question alone: the raster has no routes, and the trail data has no elevation. Overlaying them in the same study area connects the two.

## Study area
Seattle, Bellevue, Renton and Kent (−122.45 to −122.02 longitude, 47.30 to 47.78 latitude). This is where the elevation tile and King County's trail data overlap.

## Data
| Layer | Source | Notes |
|---|---|---|
| Elevation (raster) | NASA SRTM 1 arc-second, via USGS EarthExplorer | ~30 m pixels, meters |
| Trails (vector) | "Trails in King County," King County GIS Open Data | Filtered to regional trails: 122 trails, 384 segments |
| Basemap | My Mapbox Studio style, cloned from [Mapbox Outdoors] | © Mapbox © OpenStreetMap |

## Processing
1. **Basemap:**
   - In Mapbox Studio, hid the hillshade and contours so they wouldn't compete with the elevation colors.
   - Faded roads and paths to pale gray so the trails stand out.
   - Loaded the style into QGIS through WMTS and exported it with QMetaTiles to `assets/mapbox-independent`.
2. **Elevation:**
   - In QGIS, colored it green to brown from 0 to 400+ m.
   - Made sea level and water transparent.
   - Exported it with a transparent background to `assets/elevation`.
3. **Trails:**
   - Downloaded the regional trails as GeoJSON in WGS84 and saved them as `data/kc_trails.geojson`.
   - On the web map they're drawn as purple lines with popups.
4. **Tile export:** both tile sets use the same extent and zoom 10–14, as 256 px PNG tiles in XYZ format. The web page stacks them in this order: basemap, then elevation at 65% opacity, then trails.

## What the map can and can't show
- **The trail popups show real attributes** from King County's data.
- **The elevation layer is an image.** Its colors show height ranges, but clicking it can't return an exact elevation or a trail's total climb.
- **SRTM's ~30 m pixels smooth out small features** like bridges and short steep banks. The map is good for comparing trails at a regional scale, not for exact grades.

## Attribution
© Mapbox © OpenStreetMap · NASA SRTM / USGS EarthExplorer · King County GIS Open Data (King County Parks). Built with QGIS, QMetaTiles and MapLibre GL JS.
