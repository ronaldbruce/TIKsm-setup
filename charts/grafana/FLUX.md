# Flux language setup
## Manual Setup
- [Flux](https://docs.influxdata.com/flux/v0.12/) is still in beta, so this information may be out of date
- [Enable](https://docs.influxdata.com/flux/v0.12/introduction/installation) Flux by setting the `flux-enabled` option to `true` under the `[http]` section of the influxdb.conf
- Follow instructions laid out by [Flux Datasource for Grafana](https://grafana.com/plugins/grafana-influxdb-flux-datasource)

## Cronus-Monitoring Setup
- Run the init.sh script in the scripts directory and the included helm chart will enable flux automatically
    - Flux is enabled during pre-configuration of InfluxDB via the [values.yaml](https://github.com/Eliforbes42/cronus-monitoring/blob/master/charts/influxdb/values.yaml) `[http]` section with the item `flux_enabled` with the value `true`

    - Flux is enabled during pre-configuration of Grafana via the [values.yaml](https://github.com/Eliforbes42/cronus-monitoring/blob/master/charts/grafana/values.yaml) under the `datasources` section, under the item `type` with the value `grafana-influxdb-flux-datasource`
    
## Helpful information
- Read more about [Flux](https://github.com/influxdata/platform/tree/master/query)

## How to begin writing queries in grafana
1. Open the intended dashboard, then create a panel and open that panel's editor
2. Open the `Data Sources` drop down menu, and select `influx-flux` as the current panel's `Data Source` 
3. The query editor will switch to a flux IDE
    > Note: Pressing the Enter key will cause the current query to run. Use shift+enter to produce a return character. Additionally, the Grafana IDE's autocompletion is very aggressive, with a press of the tab key autocompleting several lines of a query at a time

## Testing
- Flux has built in testing, but currently only [`assertEquals()`](https://docs.influxdata.com/flux/v0.12/functions/tests/assertequals/) to test queries inline, this can only be done at runtime of the flux query. This may be able to be automated via url queries to influxdb, but further investigation will need to be done

## Tips and Tricks
- Use the [`limit()`](https://docs.influxdata.com/flux/v0.12/functions/transformations/limit/) function to reduce the number of rows queried from InfluxDB, this should be done because the current iteration of the Grafana's Flux Plugin does not handle large quantities of rows
- [`join()`](https://docs.influxdata.com/flux/v0.12/functions/transformations/join) is not a pipe function and does not use the pipe forward operator, `join()` instead is a start point for that line of the query
- Avoid pressing the tab key while in the editor as it will autocomplete code that may not be intended for execution
- Leave Grafana's pre-made query `from(bucket: "undefined")` until the query is ready to be executed. This forces the query to run on an undefined database in InfluxDB, so that mistakenly running the query before it is ready won't overload the Grafana IDE 
- If using windowed data, the `window(every: inf)` query must be used to turn data back into a time series for use with grafana
