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

## Find stations by measurements

Common checkboxes use the authoritative Water Atlas parameter IDs: TN 915,
phosphorus IDs matching phosphorus/phosphate definitions (including total, filterable, organic and orthophosphate), and laboratory chlorophyll a 46 or 47. Corrected, uncorrected and
probe chlorophyll remain separate parameters for charts. More measurements
provides the full searchable inventory definition list. Match all requires
every selected group; match any requires at least one. Waterbody and station
lists apply these filters together with the county and text search.

`python refresh_parameters.py` prepares resumable batches of 500 station
identities using create -> list inventory. Add `--force` to recheck existing batches; changed station membership is refreshed automatically. The saved snapshot has 61,045
checked identities and 171 unconfirmed identities. Only exact requested
source/station identities are retained, even if a provider returns additional
metadata rows. Missing inventory is not interpreted as no measurements.
Filtered choices exclude unconfirmed inventory; coverage and live retry
controls show this limitation. Run the inventory refresh after discovery, then
build and publish all county JSON files as well as index.html.

Repeated-sampling coverage defaults to at least six distinct sampling dates per full year, with data in every quarter. Users can change the annual minimum or require every month. Each year is checked separately, and partial-year minimums are prorated by selected days and rounded up. Repeated measurements on one day count once. Phosphorus forms can jointly provide date coverage, while their values remain separate in charts.

Optional date checking queries one station at a time (two concurrent workers),
confirms its identity, and checks repeated sampling throughout the chosen
period for each requested group. Checks follow the current search. Results
are cached in memory by county, source/station, selection, match mode and dates.
Cancellation and selection changes invalidate pending results. API failures
remain unverified; they are never marked as a confirmed absence of data.
Matching measurements may be collected on different days within the period.
The graph API does not supply sample quality flags or depths. Live date checks
can be slow for large selections; narrow the search or stop and use completed
results. Selecting a station carries the checked period into graph retrieval.

## Waterbody aggregation

Choose county and waterbody first. Measurement checkboxes are optional and
filter stations within that waterbody; they do not remove the waterbody choice.
Choose either one specific station or aggregation across all matching source/
station identities. Aggregation is limited to the selected county and a named,
positive GIS waterbody ID. Alias names with the same ID are included; generic
unassigned/groundwater placeholders cannot be pooled.

The aggregator retrieves each station separately, confirms its exact source/
station identity and parameter inventory, then requests one parameter and date
range. Two workers run concurrently. Records retain source code, station ID,
provider, coordinates, original waterbody association, parameter ID, units,
request ID and source URL. Parameters and units remain separate in charts.
Graphs pool observations rather than averaging station means: frequently
sampled stations contribute more observations. Potentially overlapping legacy
and newer provider records are retained rather than silently deduplicated.

The manifest includes all eligible identities and reports loaded, not measured,
no observations in period, failed or not checked. Cancellation retains completed
records as an explicitly partial result. Partial results are disclosed in the
chart and exported images. The graph API omits quality flags and depths. These
are not reconstructed. Selected graph records, all loaded waterbody parameters,
and a station manifest can be downloaded separately. Loading another parameter
with the same station set and dates retains previously loaded parameters.
Selection or date-context changes isolate/reset those aggregate results.

Choose All cataloged measurements to process the known parameter union in sequence, or All selected measurement types when optional checkboxes narrow it. Cancelled batches retain completed observations and manifests; never assume every requested parameter completed after cancellation.

## Choose contributing stations and station colors

Analysis choices include all eligible stations, a custom multi-station checklist,
one specific station, or multiple entered station IDs. The checkbox selection
controls plot membership independently of downloaded records. Existing data
redraw without another request; adding a station without retrieved observations
requires loading data. The complete retrieved-data download retains all input
records, while selected-source CSVs reflect plot membership. The manifest marks
which station identities are currently selected.

Pasted IDs resolve within the selected county/waterbody and current parameter/
coverage filters. Accept newline, comma, semicolon or tab separators, with quoted
IDs supported. Exact SOURCE|StationID keys identify one source record. Bare IDs
with multiple providers are flagged as ambiguous and require explicit checkbox
selection. Leading zeros are preserved; duplicates are removed. Unmatched,
filtered and outside-waterbody IDs have separate diagnostics. No cross-waterbody
records are silently pooled.

Seasonal and annual points carry station identities and use consistent colors
from the complete waterbody source/station list, so subsets and parameters do
not reshuffle colors. Outliers use a dark outline rather than a different fill.
A station legend appears on the page and in PNG/SVG exports. Heatmap colors
continue to represent values/counts; cell observation tables show source/station
IDs with their station colors. Filtered chart CSVs also retain station IDs.
