# admin-areas
Geographic administrative areas

Geodata on the boundaries of administrative areas in different countries (e.g. regions, provinces, states, counties, cities). Collected from disparate sources online; usually each country's geoportal. File naming convention  is: <two-letter IBAN code>.geojson.

File format is GeoJSON (https://geojson.org/) meaning all files will habve a "geometry" column which specifies the actual shape of he administrative area in 2D Cartesian space (note: not a 3D sphere -- we're working with flat maps, not globes).

See https://postgis.net/ for more detail about "geometry" columns and implementation of that data.


## note on schema design

Important to call out that countries arrange their administrative areas differently from each other and differently within their own borders.

For example, Norway's largest administrative area are "counties", whereas in the US they are "states". And the UK, the largest area is a "nation" (England, Wales, Scotland, Norther Ireland).

Germany's largest administrative areas are *both* "states" and "city-states".

For this reason, the schema design includes columns to specify both the "level" of the area (is it the largest? the smallest?) and the "type" of area it is (a county, a state, a nation, etc.)


## schema design

```
- id [primary key]
    type: varchar
    description: unique id for the area; this is currently blank so the user can implement whatever id length/system they want.

- area_level
    type: varchar
    description: heirarchical designation describing the relative geographic size of the area (lvl1 = largest, lvl4 = smallest)

- area_type_en
    type: varchar
    description: the name of the type of area, in English (e.g. "state", "city")

- area_type
    type: varchar
    description: the name of the type of area, in native language and alphabet

- area_name_en
    type: varchar
    description: the actual name of the area, in English (e.g. "Bavaria","Munich")

- area_name
    type: varchar
    description: the actual name of the area, in the native language and alphabet (e.g. "Bayern", "München")

- country_code
    type: varchar
    description: the unique two-letter IBAN country code (https://www.iban.com/country-codes)

- geometry
    type: whatever JSON is
    description: the shape of he administrative area in 2D Cartesian space, used for geospatial calculations (see https://postgis.net/)
```

## recommended usage

Each country has its own file in this repo because of GitHub filesize limits, but the schema design is such that you could have three separate tables for three separate countries and `union all` without losing data in any three.

My recommendaton is to start with one file that contains all data for all countries and create downstream tasks that filter by country or administrative level.
