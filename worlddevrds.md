# World Development Indicators Data

`worlddevrds` is an RDS serialization of World Bank World Development
Indicators data. The source project is the [World Development
Indicators](https://datatopics.worldbank.org/world-development-indicators/)
collection.

## File and dimensions

- File: `worlddevrds`
- Format: RDS, readable with R's `readRDS()` function
- R object type: `data.frame`
- Rows: 396,970
- Columns: 70
- Countries or entities: 265 unique `Country Code` values
- Indicators: 1,498 unique `Indicator Code` values
- Annual observations: 1960 through 2025

The row count is consistent with one row for each country or entity and
indicator combination: 265 x 1,498 = 396,970. The data is in **wide format**:
years are columns rather than values in a single year column.

## Column structure

### Identifier columns

| Column | Type | Description |
| --- | --- | --- |
| `Country Name` | character | Country or other reporting entity name |
| `Country Code` | character | Three-letter entity code |
| `Indicator Name` | character | Human-readable measure name |
| `Indicator Code` | character | World Bank indicator code |

### Year columns

The remaining 66 columns are named with four-digit years: `1960`, `1961`,
through `2025`. They are numeric measurements for the country/indicator pair.
The meaning and units depend on the selected indicator. For example, an
indicator whose name ends in `(% of population)` contains percentage values.

Values are commonly missing (`NA`), especially for older years or years that
are not yet available for a particular indicator. Missing values should not be
treated as zero.

## Loading the data

Run R from the repository directory and load the object:

```r
worlddev <- readRDS("worlddevrds")
```

From another working directory, use the full path:

```r
worlddev <- readRDS("/workspaces/my-noble-r/worlddevrds")
```

Useful checks after loading:

```r
class(worlddev)
dim(worlddev)
names(worlddev)
head(worlddev)
```

## Example queries

Select all rows for one country:

```r
subset(worlddev, `Country Code` == "USA")
```

Select one indicator for one country and inspect recent years:

```r
usa_electricity <- subset(
  worlddev,
  `Country Code` == "USA" & `Indicator Code` == "EG.ELC.ACCS.ZS"
)

usa_electricity[c("Country Name", "Indicator Name", "2020", "2021", "2022", "2023", "2024")]
```

Find the available values for an indicator across entities in 2023:

```r
electricity_2023 <- subset(
  worlddev,
  `Indicator Code` == "EG.ELC.ACCS.ZS",
  select = c("Country Name", "Country Code", "2023")
)
```

## Converting to long format

Long format is often easier for plotting and grouped analysis. With base R:

```r
year_columns <- grep("^[0-9]{4}$", names(worlddev), value = TRUE)

worlddev_long <- reshape(
  worlddev,
  varying = year_columns,
  v.names = "value",
  timevar = "year",
  times = year_columns,
  direction = "long"
)

worlddev_long$year <- as.integer(worlddev_long$year)
```

The resulting `value` column contains the measurement, while `year` identifies
the observation year. The four identifier columns remain available for grouping
and filtering.

## Interpretation notes

- `Country Name` includes entities that may be regions, income groups, or
  aggregates, not only sovereign countries.
- `Indicator Code` is the most reliable field for selecting a specific measure;
  names can be long and may vary in wording.
- Measurements are not necessarily comparable across indicators because their
  units, definitions, coverage, and estimation methods differ.
- Check the World Bank indicator metadata before making substantive claims
  about a variable.