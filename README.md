# Earthquake Extension Specification

- **Title:** Earthquake
- **Identifier:** <https://stac-extensions.github.io/earthquake/v1.0.0/schema.json>
- **Field Name Prefix:** eq
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @emmanuelmathot @fabricebrito

This document explains the Earthquake Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.
The specification is largely inspired by the existing earthquakes catalogs such as
the [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php) and
the [European-Mediterranean Seismological Centre](https://www.emsc-csem.org/).

A specific [section](#mappings-with-existing-catalogs) gives the translation between the existing data models and the STAC model.

- Examples:
  - [Item example](examples/item.json): Shows the basic usage of the extension in a STAC Item
  - [Generic moment magnitude example](examples/official20041226005853450_30.json): Preserves the USGS `mw` magnitude type
  - [Collection example](examples/collection.json): Shows the basic usage of the extension in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## Fields

The fields in the table below can be used in these parts of STAC documents:

- [ ] Catalogs
- [ ] Collections
- [x] Item Properties (incl. Summaries in Collections)
- [ ] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [ ] Links

| Field Name        | Type                           | Description                                                                                                                    |
| ----------------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| eq:magnitude      | \[number]                      | **REQUIRED**. The magnitude for the event. See also magnitude_type.                                                            |
| eq:magnitude_type | string                         | The type of magnitude. Default to `mww`.                                                                                       |
| eq:felt           | number                         | The number of felt reports.                                                                                                    |
| eq:status         | string                         | Indicates whether the event has been reviewed by a human. Possible value: `automatic`, `reviewed`, `deleted`                   |
| eq:tsunami        | boolean                        | This flag is set to `true` for large events in oceanic regions and `false` otherwise.                                          |
| eq:sources        | \[[source object](#eqsources)] | **REQUIRED**. A unique identifier for the event. This is the current preferred id for the event, and may change over time, the |
| eq:depth          | number                         | The depth of the event in kilometers.                                                                                          |

### Additional Field Information

#### eq:magnitude_type

The `eq:magnitude_type` field is a string that describes the type of magnitude. The default value is `mww` (moment magnitude).
`mw` denotes generic moment magnitude and preserves the source designation without specifying a measurement method.
When a source reports `mw`, retain it rather than substituting a method-specific type such as `mww`, `mwc`, or `mwb`.
The following summary is based on the official [USGS magnitude types documentation](https://www.usgs.gov/programs/earthquake-hazards/magnitude-types).
The minimum and maximum columns describe approximate working magnitude ranges, not validation bounds for `eq:magnitude`.
“Not specified” means USGS gives no numeric endpoint; it does not imply a physical limit.

| Type / common codes | Name or method | Typical minimum | Typical maximum | Interpretation / qualification |
|---|---|---:|---:|---|
| **ML / Ml / ml** | Local—Richter | ≈2.0 | ≈6.5 | USGS working range; smaller events are measurable. |
| **MLv** | Vertical-component local magnitude | Variable | Variable | Local calibration and implementation determine applicability. |
| **MLh** | Horizontal-component local magnitude | Variable | Variable | SeisComP implementation follows Swiss standards. |
| **MLc** | Custom local magnitude | Variable | Variable | Configurable local-event calibration. |
| **mb** | Short-period body-wave magnitude | ≈4.0 | ≈6.5 | Saturates around the upper end. |
| **mB** | Longer-period / broadband body-wave magnitude | Variable | Variable | Distinct from `mb`; depends on the particular formulation. |
| **mB_BB** | Broadband body-wave magnitude | Variable | Variable | SeisComP uses `mB` as a synonym. |
| **Ms / MS** | Surface-wave magnitude, generic family | Variable | Variable | For the common 20-second implementation, see below. |
| **Ms20 / Ms_20** | 20-second surface-wave magnitude | ≈5.0 | ≈8.5 | Saturation begins around 8.3. |
| **Ms(BB)** | Broadband surface-wave magnitude | Variable | Variable | Processing and period range determine applicability. |
| **mb(Lg) / mbLg / mb_Lg / MLg** | Regional Lg-wave magnitude | ≈3.5 | ≈7.0 | USGS working range. |
| **MN / Mn** | Nuttli regional magnitude | Variable | Variable | Related to the Lg family; regional calibration matters. |
| **Md / MD / md** | Duration magnitude | Variable | ≈4.0 | Commonly used for small earthquakes. |
| **Mc / MC** | Coda magnitude | Variable | Variable | Closely related to duration magnitude; calibration-specific. |
| **Mw** | Moment magnitude, generic scale | Variable | No fixed ceiling | Applicability depends on how seismic moment is estimated. |
| **Mww** | W-phase moment magnitude | ≈5.0 | No fixed ceiling | USGS operational guidance. |
| **Mwc** | Centroid moment magnitude | ≈5.5 | No fixed ceiling | Long-period surface-wave inversion. |
| **Mwb** | Body-wave moment magnitude | ≈5.5 | ≈7.0 | Operational applicability range. |
| **Mwr** | Regional moment magnitude | ≈4.0 | ≈6.5 | Dense networks may reach ≈3.5. |
| **Mwp / Mi*** | Integrated P-wave magnitude | ≈5.0 | ≈8.0 | Rapid moment estimate. |
| **Mwpd** | P-wave duration–amplitude moment magnitude | Variable | No fixed ceiling | Developed for large events; published testing included magnitude 9 earthquakes. |
| **Mw(mB)** | Moment magnitude converted from `mB` | Variable | Variable | Validity depends on the conversion relationship. |
| **Mw(Mwp)** | Moment magnitude converted from `Mwp` | Variable | Variable | Validity depends on the conversion relationship. |
| **Me / ME** | Energy magnitude | ≈3.5 | No fixed ceiling | Based on radiated seismic energy. |
| **Mj / MJMA / Mjma** | Japan Meteorological Agency magnitude | Variable | Variable | JMA procedure; a universal hard range is inappropriate. |
| **Mt** | Tsunami magnitude | Variable | Variable | Derived from tsunami amplitudes; method-specific. |
| **Mfa** | Felt-area magnitude | Variable | Variable | Historical observational estimate. |
| **Mint** | Intensity-derived magnitude | Variable | Variable | Estimated from reported intensity. |
| **Mh** | Nonstandard magnitude designation | Variable | Variable | Meaning requires the reporting agency’s documentation. |
| **M** | Generic or summary magnitude | Variable | Variable | May mean unspecified magnitude or a software-computed summary. |

Earthquake magnitudes [can be negative](https://www.usgs.gov/faqs/how-can-earthquake-have-a-negative-magnitude).
Zero is a reference level on a logarithmic scale, not a minimum.
For local magnitude (ML), under comparable measurement conditions:

| Signal amplitude relative to magnitude 0 | Magnitude |
|---|---|
| 10 times larger | 1 |
| Same amplitude | 0 |
| 10 times smaller | −1 |
| 100 times smaller | −2 |

Negative magnitudes describe very small earthquakes, generally detectable only by instruments and not felt by people.
USGS explicitly confirms that negative magnitudes are valid.

#### eq:sources

The `eq:sources` field is an object that contains information about the sources that contributed to the event.

| Field Name | Type   | Description                                                                                              |
| ---------- | ------ | -------------------------------------------------------------------------------------------------------- |
| name       | string | **REQUIRED**. The name identifying a data contributor in the source catalog                              |
| code       | string | **REQUIRED**. An identifying code assigned by - and unique from - the corresponding source for the event |
| catalog    | string | The name of the catalog that the source belongs to. See [Catalogs](#catalogs) for the possible names     |

The order of the sources in the list is important. The first source is considered the preferred source of information for the event.
For each catalog, one or more links should be provided to the related event items (see [Relation types](#relation-types)).

##### Catalogs

Several sources are cataloging seismic events. The `catalog` field may be used to reference the catalog that the source belongs to
so that multiple sources can be linked to the same event.

The following table list the catalogs that are currently identified:

| Catalog Name | Description                                                                         |
| ------------ | ----------------------------------------------------------------------------------- |
| `USGS`       | [USGS Earthquake Hazards Program](https://www.usgs.gov/programs/earthquake-hazards) |
| `EMSC`       | [European-Mediterranean Seismological Centre](https://www.emsc-csem.org/)           |
| `FDSN`       | [International Federation of Digital Seismograph Networks](https://www.fdsn.org/)   |

#### keywords

The [`keywords`](https://github.com/radiantearth/stac-spec/blob/master/commons/common-metadata.md#basics) field should be used to store
the list of strings that reference locations in relation with the event.
It is recommended to use [GeoNames](http://www.geonames.org/) dataset to reference populated places that are in close proximity to a seismic event.
If there is no nearby city within 300 kilometers (or if the nearby cities database is unavailable for some reason),
the [Flinn-Engdahl (F-E)](https://www.isc.ac.uk/standards/FEregions/) seismic and geographical regionalization scheme is used.

## Mappings with existing catalogs

### USGS Earthquake GeoJSON Summary Feed

The following table gives the mapping between the fields in the 
[USGS Earthquake GeoJSON Summary Feed](https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php) and the STAC model.

| ComCat Field Name                                                                                                                                   | STAC Field Name      | Description                                                                                                                              |
| --------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [latitude](https://earthquake.usgs.gov/data/comcat/index.php#latitude) and [longitude](https://earthquake.usgs.gov/data/comcat/index.php#longitude) | `geometry`           | The coordinates of the event.                                                                                                            |
| [title](https://earthquake.usgs.gov/data/comcat/index.php#title)                                                                                    | `title`              | A brief description of the event.                                                                                                        |
| [mag](https://earthquake.usgs.gov/data/comcat/index.php#mag)                                                                                        | `eq:magnitude`       | The magnitude for the event.                                                                                                             |
| [magType](https://earthquake.usgs.gov/data/comcat/index.php#magType)                                                                                | `eq:magnitude_type`  | The type of magnitude.                                                                                                                   |
| [time](https://earthquake.usgs.gov/data/comcat/index.php#time)                                                                                      | `datetime`           | The time of the event converted from milliseconds since the epoch to ISO8601                                                             |
| [updated](https://earthquake.usgs.gov/data/comcat/index.php#updated)                                                                                | `updated`            | The time when the event was last converted transformed to ISO8601                                                                        |
| [place](https://earthquake.usgs.gov/data/comcat/index.php#place)                                                                                    | `keywords`           | The list of places referencing locations in relation with the event                                                                      |
| [url](https://earthquake.usgs.gov/data/comcat/index.php#url)                                                                                        | `links`              | The URL to the event page on the USGS website. The link must have the relationship `related` and the mime-type `text/html`               |
| [detail](https://earthquake.usgs.gov/data/comcat/index.php#detail)                                                                                  | `links`              | The URL to the event detail page on the USGS website. The link must have the relationship `related` and the mime-type `application/json` |
| [felt](https://earthquake.usgs.gov/data/comcat/index.php#felt)                                                                                      | `eq:felt`            | The number of felt reports.                                                                                                              |
| [status](https://earthquake.usgs.gov/data/comcat/index.php#status)                                                                                  | `eq:status`          | Indicates whether the event has been reviewed by a human.                                                                                |
| [tsunami](https://earthquake.usgs.gov/data/comcat/index.php#tsunami)                                                                                | `eq:tsunami`         | This flag is set to `true` for large events in oceanic regions and `false` otherwise.                                                    |
| [net](https://earthquake.usgs.gov/data/comcat/index.php#net)                                                                                        | `eq:sources[0].name` | The network code of the preferred source of information for the event.                                                                   |
| [code](https://earthquake.usgs.gov/data/comcat/index.php#code)                                                                                      | `eq:sources[0].code` | The event code of the preferred source of information for the event.                                                                     |
| [sources](https://earthquake.usgs.gov/data/comcat/index.php#sources)                                                                                | `eq:sources[*].name` | The network code of the other sources of information for the event.                                                                      |
| [ids](https://earthquake.usgs.gov/data/comcat/index.php#ids)                                                                                        | `eq:sources[*].code` | The event code of the other sources of information for the event.                                                                        |

### EMSC Earthquake Catalog

The following table gives the mapping between the fields in the 
[EMSC FDSN-EVENT web service](https://www.emsc-csem.org/Files/epos/specifications/Specs_fdsnevent-WS.pdf) and the STAC model.

| EMSC Field Name | STAC Field Name         | Description                               |
| --------------- | ----------------------- | ----------------------------------------- |
| Lat, Lon        | `geometry`              | Coordinates of the epicenter              |
| Mag             | `eq:magnitude`          | Magnitude of this event                   |
| MagType         | `eq:magnitude_type`     | Magnitude type of this event              |
| Time            | `datetime`              | Origin time of the earthquake             |
| Depth           | `eq:depth`              | Depth of the epicenter                    |
| source_id       | `eq:sources[*].code`    | EMSC event identifier                     |
| source_catalog  | `eq:sources[*].catalog` | Catalog identifier (e.g. "EMSC-RTS")      |
| Auth            | `eq:sources[*].name`    | Author of this event                      |
| flynn_region    | `keywords`              | Region name from the Flynn-Engdahl naming |
| lastupdate      | `updated`               | Date time of the time of publication      |
| etype           | `title`                 | ISF event type                            |

## Relation types

The following types should be used as applicable `rel` types in the
[Link Object](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#link-object).

| Type   | Description                                                |
| ------ | ---------------------------------------------------------- |
| source | This link points to a source of information for the event. |

### Additional fields

| Field Name | Type   | Description                                                                                                    |
| ---------- | ------ | -------------------------------------------------------------------------------------------------------------- |
| source     | string | The name of the source of information for the event. See [`eq:sources.name`](#eqsources) for more information. |

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```
