
<!-- README.md is generated from README.Rmd. Please edit that file directly and reknit -->

# tidygeocoder <a href='https://jessecambon.github.io/tidygeocoder/'><img src="man/figures/tidygeocoder_hex.png" align="right" height="139"/></a>

<!-- badges: start -->

[![Project Status: Active – The project has reached a stable, usable
state and is being actively
developed.](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active)
[![License:
MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/jessecambon/tidygeocoder/blob/master/LICENSE.md)
[![CRAN](https://www.r-pkg.org/badges/version/tidygeocoder)](https://cran.r-project.org/package=tidygeocoder)
[![CRAN Total
Downloads](http://cranlogs.r-pkg.org/badges/grand-total/tidygeocoder)](https://CRAN.R-project.org/package=tidygeocoder)
[![CRAN Downloads Per
Month](http://cranlogs.r-pkg.org/badges/tidygeocoder)](https://cran.r-project.org/package=tidygeocoder)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4448251.svg)](https://doi.org/10.5281/zenodo.4448251)
<!--[![Github Stars](https://img.shields.io/github/stars/jessecambon/tidygeocoder?style=social&label=Github)](https://github.com/jessecambon/tidygeocoder) -->

<!-- badges: end -->

## Introduction

Tidygeocoder makes getting data from geocoder services easy. Both
forward geocoding (providing addresses to obtain latitude and longitude)
and reverse geocoding (providing latitude and longitude to obtain
addresses) are supported. All results are returned in [tibble
format](https://tibble.tidyverse.org/) and the supported geocoder
services are listed below.

Batch geocoding (geocoding multiple addresses or coordinates per query)
is used by default if supported by the geocoder service when multiple
inputs (addresses or coordinates) are provided. Duplicate, missing/NA,
and blank input data is handled elegantly - only unique inputs are
passed to geocoder services, but the rows in the original data are
preserved by default.

In addition to the usage examples below you can refer to the following
references:

-   [Mapping European soccer club
    stadiums](https://jessecambon.github.io/2020/07/15/tidygeocoder-1-0-0.html)
-   [Mapping Washington, DC
    landmarks](https://jessecambon.github.io/2019/11/11/tidygeocoder-demo.html)
-   [Getting Started
    Vignette](https://jessecambon.github.io/tidygeocoder/articles/tidygeocoder.html)
    for more detailed and comprehensive usage examples. The last section
    of the vignette contains a [helpful
    reference](https://jessecambon.github.io/tidygeocoder/articles/tidygeocoder.html#api-reference-1)
    on geocoder service parameters.

## Installation

To install the stable version from CRAN (the official R package
servers):

``` r
install.packages('tidygeocoder')
```

Alternatively, you can install the latest development version from
GitHub:

``` r
if(!require(devtools)) install.packages("devtools")
devtools::install_github("jessecambon/tidygeocoder")
```

## Geocoder Services

The supported geocoder services are shown in the table below with their
geographic limitations, if they support batch geocoding (geocoding
multiple addresses or coordinates in a single query), if an API key is
required, and the usage rate limitations. Refer to the website for each
geocoder service for the most up-to-date details on costs, capabilities,
and usage limitations.

| Service                                                                              | Geography     | Batch Geocoding | API Key Required | Query Rate Limit        |
|--------------------------------------------------------------------------------------|---------------|-----------------|------------------|-------------------------|
| [US Census](https://geocoding.geo.census.gov/)                                       | US            | Yes             | No               | None                    |
| [Nominatim (OSM)](https://nominatim.org)                                             | Worldwide     | No              | No               | 1/second                |
| [Geocodio](https://www.geocod.io/)                                                   | US and Canada | Yes             | Yes              | 1000/minute (free tier) |
| [Location IQ](https://locationiq.com/)                                               | Worldwide     | No              | Yes              | 2/second (free tier)    |
| [Google](https://developers.google.com/maps/documentation/geocoding/overview)        | Worldwide     | No              | Yes              | 50/second               |
| [OpenCage](https://opencagedata.com)                                                 | Worldwide     | No              | Yes              | 1/second (free tier)    |
| [Mapbox](https://docs.mapbox.com/api/search/)                                        | Worldwide     | See Note        | Yes              | 10/second (free tier)   |
| [HERE](https://developer.here.com/products/geocoding-and-search)                     | Worldwide     | Yes             | Yes              | None                    |
| [TomTom](https://developer.tomtom.com/search-api/search-api-documentation/geocoding) | Worldwide     | Yes             | Yes              | 5/second (free tier)    |
| [MapQuest](https://www.mapquest.com/)                                                | Worldwide     | Yes             | Yes              | None                    |

Notes:

-   The US Census service supports street-level addresses only (ie. “11
    Wall St New York, NY” is OK but “New York, NY” is not).
-   The US Census and Geocodio services both support a maximum of 10,000
    addresses per batch query.
-   The US Census and OSM services are free while Geocodio, Location IQ,
    OpenCage, Mapbox, HERE, TomTom and MapQuest are commercial services
    that offer both free and paid usage tiers. The Google service [bills
    per
    query](https://developers.google.com/maps/documentation/geocoding/usage-and-billing).
-   The Census geocoder does not support reverse geocoding.
-   The Mapbox service is capable of performing batch geocoding when
    using the [permanent
    endpoint](https://docs.mapbox.com/api/search/geocoding/#batch-geocoding),
    but this capability is not currently implemented in tidygeocoder. If
    you’d like to add this capability to the package see [issue
    \#73](https://github.com/jessecambon/tidygeocoder/issues/73).

## Usage

In this example we will geocode a few addresses using the `geocode()`
function and plot them on a map with ggplot.

``` r
library(dplyr)
library(tibble)
library(tidygeocoder)

# create a dataframe with addresses
some_addresses <- tribble(
~name,                  ~addr,
"White House",          "1600 Pennsylvania Ave NW, Washington, DC",
"Transamerica Pyramid", "600 Montgomery St, San Francisco, CA 94111",     
"Willis Tower",         "233 S Wacker Dr, Chicago, IL 60606"                                  
)

# geocode the addresses
lat_longs <- some_addresses %>%
  geocode(addr, method = 'census', lat = latitude , long = longitude)
```

The `geocode()` function attaches latitude and longitude columns to our
input dataset of addresses. The US Census geocoder is used here, but
other services can be specified with the `method` argument. See the
`geo()` function documentation for details.

| name                 | addr                                       | latitude |  longitude |
|:---------------------|:-------------------------------------------|---------:|-----------:|
| White House          | 1600 Pennsylvania Ave NW, Washington, DC   | 38.89875 |  -77.03535 |
| Transamerica Pyramid | 600 Montgomery St, San Francisco, CA 94111 | 37.79470 | -122.40314 |
| Willis Tower         | 233 S Wacker Dr, Chicago, IL 60606         | 41.87851 |  -87.63666 |

Now that we have the longitude and latitude coordinates, we can use
ggplot to plot our addresses on a map.

``` r
library(ggplot2)
library(maps)
library(ggrepel)

ggplot(lat_longs, aes(longitude, latitude), color = "grey99") +
  borders("state") + geom_point() + 
  geom_label_repel(aes(label = name)) + 
  theme_void()
```

<img src="man/figures/README-usamap-1.png" style="display: block; margin: auto;" />

To return the full results from a geocoder service (not just latitude
and longitude) you can use `full_results = TRUE`. Additionally, for the
Census geocoder you can use `return_type = 'geographies'` to return
geography columns (state, county, Census tract, and Census block).

``` r
full <- some_addresses %>%
  geocode(addr, method = 'census', full_results = TRUE, 
          return_type = 'geographies')
```

| name                 | addr                                       |      lat |       long |  id | input\_address                                  | match\_indicator | match\_type | matched\_address                                | tiger\_line\_id | tiger\_side | state\_fips | county\_fips | census\_tract | census\_block |
|:---------------------|:-------------------------------------------|---------:|-----------:|----:|:------------------------------------------------|:-----------------|:------------|:------------------------------------------------|:----------------|:------------|:------------|:-------------|:--------------|:--------------|
| White House          | 1600 Pennsylvania Ave NW, Washington, DC   | 38.89875 |  -77.03535 |   1 | 1600 Pennsylvania Ave NW, Washington, DC, , ,   | Match            | Exact       | 1600 PENNSYLVANIA AVE NW, WASHINGTON, DC, 20500 | 76225813        | L           | 11          | 001          | 980000        | 1034          |
| Transamerica Pyramid | 600 Montgomery St, San Francisco, CA 94111 | 37.79470 | -122.40314 |   2 | 600 Montgomery St, San Francisco, CA 94111, , , | Match            | Exact       | 600 MONTGOMERY ST, SAN FRANCISCO, CA, 94111     | 192281262       | R           | 06          | 075          | 061101        | 2014          |
| Willis Tower         | 233 S Wacker Dr, Chicago, IL 60606         | 41.87851 |  -87.63666 |   3 | 233 S Wacker Dr, Chicago, IL 60606, , ,         | Match            | Exact       | 233 S WACKER DR, CHICAGO, IL, 60606             | 112050003       | L           | 17          | 031          | 839100        | 2008          |

To perform **reverse geocoding** (obtaining addresses from latitude and
longitude coordinates), we can use the `reverse_geocode()` function. The
arguments are similar to the `geocode()` function, but we now are
specifying the latitude and longitude columns in our dataset with the
`lat` and `long` arguments. The single line address is returned in a
column named by the `address` argument. See the `reverse_geo()` function
documentation for more details on reverse geocoding.

``` r
coordinates <- tibble(latitude = c(38.895865, 43.6534817, 35.0844),
                longitude = c(-77.0307713, -79.3839347, -106.6504))

rev1 <- coordinates %>%
  reverse_geocode(lat = latitude, long = longitude, method = 'osm',
                  address = address_found, full_results = TRUE)
```

| latitude |  longitude | address\_found                                                                                                                                     | place\_id | licence                                                                  | osm\_type |   osm\_id | osm\_lat           | osm\_lon            | tourism         | road                     | city        | state                | postcode   | country       | country\_code | boundingbox                                        | amenity           | house\_number | neighbourhood      | quarter           | state\_district  | building        | suburb               | county            |
|---------:|-----------:|:---------------------------------------------------------------------------------------------------------------------------------------------------|----------:|:-------------------------------------------------------------------------|:----------|----------:|:-------------------|:--------------------|:----------------|:-------------------------|:------------|:---------------------|:-----------|:--------------|:--------------|:---------------------------------------------------|:------------------|:--------------|:-------------------|:------------------|:-----------------|:----------------|:---------------------|:------------------|
| 38.89587 |  -77.03077 | L’Enfant’s plan, Pennsylvania Avenue, Washington, District of Columbia, 20045, United States                                                       | 302200006 | Data © OpenStreetMap contributors, ODbL 1.0. <https://osm.org/copyright> | way       | 899927546 | 38.895859599999994 | -77.0306779870984   | L’Enfant’s plan | Pennsylvania Avenue      | Washington  | District of Columbia | 20045      | United States | us            | 38.8957273 , 38.8959688 , -77.0311667, -77.0301895 | NA                | NA            | NA                 | NA                | NA               | NA              | NA                   | NA                |
| 43.65348 |  -79.38393 | Toronto City Hall, 100, Queen Street West, Financial District, Spadina—Fort York, Old Toronto, Toronto, Golden Horseshoe, Ontario, M5H 2N2, Canada | 137105159 | Data © OpenStreetMap contributors, ODbL 1.0. <https://osm.org/copyright> | way       | 198500761 | 43.6536032         | -79.38400547469666  | NA              | Queen Street West        | Old Toronto | Ontario              | M5H 2N2    | Canada        | ca            | 43.6529946 , 43.6541458 , -79.3848438, -79.3830415 | Toronto City Hall | 100           | Financial District | Spadina—Fort York | Golden Horseshoe | NA              | NA                   | NA                |
| 35.08440 | -106.65040 | Market Building, 301, Central Avenue Northwest, Downtown Albuquerque, Albuquerque, Bernalillo County, New Mexico, 87102-3116, United States        | 189380691 | Data © OpenStreetMap contributors, ODbL 1.0. <https://osm.org/copyright> | way       | 437189749 | 35.0846948         | -106.65064483238235 | NA              | Central Avenue Northwest | Albuquerque | New Mexico           | 87102-3116 | United States | us            | 35.08451 , 35.084941 , -106.6508398, -106.6504144  | NA                | 301           | NA                 | NA                | NA               | Market Building | Downtown Albuquerque | Bernalillo County |

For further documentation, refer to the [Getting Started
Vignette](https://jessecambon.github.io/tidygeocoder/articles/tidygeocoder.html)
and the [function
documentation](https://jessecambon.github.io/tidygeocoder/reference/index.html).

## Contributing

Contributions to the tidygeocoder package are welcome. File [an
issue](https://github.com/jessecambon/tidygeocoder/issues) for bug fixes
or suggested features. If you would like to add support for a new
geocoder service, reference [this
post](https://github.com/jessecambon/tidygeocoder/issues/62#issue-777707424)
for instructions.
