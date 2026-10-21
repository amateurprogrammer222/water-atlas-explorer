# Water Atlas static explorer

Open index.html directly in a modern browser, or serve this folder with any static host. No API key, Python server, account, or network request is needed to explore the data. The verified station extract and chart code are embedded.

Choose county, waterbody, source/station, parameter, unit and depth. Chart controls include dates, decimal places, and scale. Changing the outer selection resets chart filters. Only waterbodies with loaded records appear. The Manatee County pilot includes WIN_21FLMANA|UM2 only. No automatic refresh or full Atlas catalog is claimed.

## County expansion

The source project's data/counties.json maps county IDs and names to reviewed CSV extracts and per-extract provenance manifests. Adding reviewed station extracts to a county automatically expands its waterbody and station lists on rebuild. Adding a county entry provides another county choice without editing the chart logic. County changes clear prior selections; identical station IDs in different counties or sources are kept separate.

Each dataset manifest must record its original download URL, request ID, retrieval date, checksum, station key and record count. A dataset is currently one source/station extract. Assign counties using verified geographic metadata, not solely the Atlas site name; watershed and Atlas boundaries may cross county lines.

As of October 21, 2026, the official waterbody search endpoint returned a missing-database-procedure error, the documented water-quality trends station endpoint reported not implemented, and the legacy county-filter download form returned an unexpected error. Accordingly, this release adds the county-first workflow but does not claim countywide data completeness. Additional stations require verified exports before they become selectable.

## Free GitHub Pages deployment

Create a public repository, upload index.html and .nojekyll at its root, then choose Settings → Pages → Deploy from a branch → main / root. No build workflow is necessary. README.md is optional. Public publishing has not been performed by the build script.

## Rebuild

Run `python build_static.py` in the source project after preparing the verified station extract. Python's standard library is sufficient for this build. The generator reuses seasonal_template.html, annual_boxplots.py, seasonal_boxplots.py, the generated lake_manatee_heatmap.html, explorer_shell.html and explorer.js. Run `python heatmap.py` first after changing the heatmap source.

Data source: https://manatee.wateratlas.usf.edu/datadownload/downloads/DataDownload-989861.xlsx

Provider: Manatee County Environmental Management Sampling Data. Aggregator: USF Water Atlas. The official multi-station workbook supplied 920 WIN_21FLMANA|UM2 records on 85 sampling dates, January 17, 2018–May 5, 2026. Source code and station ID are preserved as strings. Unit and depth groups remain separate. Qualified and nonnumeric results are excluded from charts, retained in full source downloads. Values are not rounded for calculations or downloads. Changing display precision is cosmetic.

Source-record download uses the outer parameter/unit/depth selection, retaining all dates; the chart's own CSV download uses its inner date filters and includes only numeric chart records. Repeated observations are not deduplicated. Methods are not independently validated. These charts do not establish safety or causation.
