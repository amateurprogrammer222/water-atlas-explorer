# Water Atlas Explorer

Choose any of the 69 counties in the shared Water Atlas catalog (all 67 Florida counties and Baldwin and Escambia counties in Alabama), a named waterbody, and a source/station identity. Click
**Load available measurements** to request Water Atlas parameters. The first
parameter is plotted automatically; choosing another Water Atlas parameter
retrieves and plots it. Change the dates and click Load available measurements
to start a new date-range request. Chart views include seasonal boxes, annual
boxes, and a year/month heatmap, with 0–6 display decimal places.

## Discovery

The authoritative Water Atlas sampling-location GIS layer provides county,
waterbody, station, provider, coordinates, record count and coverage fields:
https://gis.waterinstitute.usf.edu/arcgis/rest/services/Maps/WaterAtlas_DataDownload/MapServer/0

The October 21, 2026 catalog contains 61,217 entries assigned to 69 counties.
Each county catalog loads on demand. Manatee and Hillsborough are also bundled
in the landing page. County assignments use the GIS COUNTY field; records with
missing or Not Applicable county values are excluded from county lists.
Named waterbody group totals can include groundwater or unmapped placeholders.
County catalog coverage does not guarantee observations for every parameter or date range.
Dedicated county atlas request settings are used when available; other counties
use the shared download service via the Tampa Bay portal (site 3). This is a
request gateway, not evidence that the station belongs to Tampa Bay. Pinellas's
retired site is replaced by the Tampa Bay portal. Station metadata and source IDs
remain visible for every county.

Refresh station catalog uses paginated GIS queries. If unavailable, the saved
snapshot remains. Names and county assignments come from source fields, not
inference from station names. Missing associations retain an unassigned group.

## Measurement retrieval

Historical Water Atlas graph retrieval uses create -> list?id -> update -> graph.
The obsolete POST datadownload/list endpoint is not used. A single source/station
is selected, and its returned metadata is checked before charting. Source URL,
request ID and retrieval time are shown. The graph endpoint omits quality flags
and sample depths, so these results cannot be filtered by qualifier or depth.
Charts and metadata explicitly disclose that limitation. Use the full official
row-format export for qualified/depth-specific analysis. Original UM2 verified
rows retain their full original fields and conservative qualifier filtering.

USGS stations also offer direct daily-value retrieval for 1/5/10 years. These
are daily statistics, not discrete sample observations. Parameter codes, units,
statistics and approval flags are retained. Only approved values are plotted
by default; provisional values can be enabled before loading.

Browser requests time out and show failures rather than silently drawing no
chart. Empty date ranges show guidance to widen the period. Selection changes
invalidate in-flight requests. Data stays in the browser tab until reload;
download the selected source records to keep it. Display rounding does not
change values used in calculations or downloads.

## Build and hosting

No continuous Python server, paid database, AI service or API key is required.
The index.html and catalog/ JSON files form a static browser application on GitHub Pages. The catalog and
UM2 extract are bundled; live refresh/retrieval needs network access and CORS.

Run `python refresh_discovery.py`, then `python build_static.py` in the source
project. Both use the standard library. Build dependencies include the chart
source templates/scripts and data/counties.json, data/discovery.json, plus the
verified station extracts. Upload the complete static_site folder, including catalog/, index.html and README.md to the
public repository. Publishing is separate from building. Avoid uploading local
environments or unrelated files. The browser app cannot run from a local file
with live retrieval if browser security blocks its requests; use the HTTPS site.

Checks: `node test_static.cjs` covers county selection, source/station identity,
GIS waterbodies, chart renderers, precision, Water Atlas create/list/update/graph,
empty-result recovery and original USGS qualifier handling.

## Graph images for presentations

Download graph PNG saves the currently displayed chart as a high-resolution
image with a white background. Download graph SVG saves a vector image. Both
formats preserve current chart dates, scale, decimal places, heatmap mode and
visible points, and include the county, waterbody, source/station identity,
source URL, retrieval time and quality/depth limitations. Insert the saved
image into PowerPoint using Insert > Pictures. CSV downloads are separate.
Tall heatmaps are scaled to keep the PNG below 16,000 pixels high; SVG has no
raster resolution limit.
