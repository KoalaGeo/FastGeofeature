# FastGeofeature

FastGeofeature is a geospatial api to serve features from a mulititude of [PostGIS](https://github.com/postgis/postgis) enabled databases. FastGeofeature is written in [Python](https://www.python.org/) using the [FastAPI](https://fastapi.tiangolo.com/) web framework. 

---

**Source Code**: <a href="https://github.com/mkeller3/FastGeofeature" target="_blank">https://github.com/mkeller3/FastGeofeature</a>

---

## Requirements

FastGeofeature requires PostGIS >= 2.4.0.

## Configuration

In order for the api to work you will need to edit the `config.py` file with your database connections.

Example
```python
DATABASES = {
    "data": {
        "host": "localhost", # Hostname of the server
        "database": "data", # Name of the database
        "username": "postgres", # Name of the user, ideally only SELECT rights
        "password": "postgres", # Password of the user
        "port": 5432, # Port number for PostgreSQL
    }
}
```

## Usage

### Running Locally

To run the app locally `uvicorn main:app --reload`

### Production
Build Dockerfile into a docker image to deploy to the cloud.

## API

| Method | URL                                                                              | Description                                             |
| ------ | -------------------------------------------------------------------------------- | ------------------------------------------------------- |
| `GET`  | `/api/v1/collections/collections`                                           | [Collections](#collections)               |
| `GET`  | `/api/v1/collections/{name}`                                                | [Feature Collection](#feature-collection)         |
| `GET`  | `/api/v1/collections/{name}/items`                                          | [Features](#features)    |
| `GET`  | `/api/v1/collections/{name}/items/{id}`                                     | [Feature](#feature) |
| `GET`  | `/api/v1/health_check`                                                           | Server health check: returns `200 OK`            |



## Collections
Collection endpoint returns a list of all available tables to query.

Collections endpoint is available at `/api/v1/collections`

```shell
curl http://localhost:8000/api/v1/collections
```

Example Response
```json
[
    {
        "id": "data.public.zip_centroids",
        "title": "zip_centroids",
        "description": "zip_centroids",
        "keywords": [
            "zip_centroids"
        ],
        "links": [
            {
                "type": "application/json",
                "rel": "self",
                "title": "This document as JSON",
                "href": "http://127.0.0.1:8000/api/v1/collections/data.public.zip_centroids"
            }
        ],
        "extent": {
            "spatial": {
                "bbox": [
                    -180,
                    -90,
                    180,
                    90
                ],
                "crs": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
            }
        },
        "itemType": "feature"
    },
  {},...
```

## Feature Collection
Feature Collection endpoint returns information about a single table.

Collections endpoint is available at `/api/v1/collections/{item}`

```shell
curl http://localhost:8000/api/v1/collections/data.public.zip_centroids
```

Example Response
```json
{
    "id": "data.public.zip_centroids",
    "title": "data.public.zip_centroids",
    "description": "data.public.zip_centroids",
    "keywords": [
        "data.public.zip_centroids"
    ],
    "links": [
        {
            "type": "application/json",
            "rel": "self",
            "title": "Items as GeoJSON",
            "href": "http://127.0.0.1:8000/api/v1/collections/data.public.zip_centroids/items"
        }
    ],
    "extent": {
        "spatial": {
            "bbox": [
                -180,
                -90,
                180,
                90
            ],
            "crs": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
        }
    },
    "itemType": "feature"
}
```

## Features
Features endpoint returns a geojson feature collection for a feature collection.

Collections endpoint is available at `/api/v1/collections/{item}/items`

```shell
curl http://localhost:8000/api/v1/collections/data.public.states/items
```

### Parameters
* `bbox=mix,miny,maxx,maxy` - filter features in response to ones intersecting a bounding box (in lon/lat or specified CRS). Ex. `17,-48,69,-161`
* `<propname>=val` - filter features for a property having a value.
  Multiple property filters are ANDed together.
* `filter=cql-expr` - filters features via a CQL expression.
* `properties=PROP-LIST`- return only specific properties (comma-separated).
  If PROP-LIST is empty, no properties are returned.
  If not present, all properties are returned.dinates to use N decimal places
* `sortby=PROP[A|D]` - sort the response items by a property (ascending (default) or descending).
* `limit=N` - limits the number of features in the response.
* `offset=N` - starts the response at an offset.

Example Response
```json
{
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "geometry": {
                "type": "MultiPolygon",
                "coordinates": [
                    ...
                ]
            },
            "properties": {
                "gid": 5,
                "state_name": "Nevada",
                "state_fips": "32",
                "sub_region": "Mountain",
                "state_abbr": "NV",
                "population": 2994047
            }
        },
        ...
    ]
}
```

## Feature

Feature endpoint returns a geojson feature collection for a single feature in a feature collection.

Collections endpoint is available at `/api/v1/collections/{item}/items/{id}`

```shell
curl http://localhost:8000/api/v1/collections/data.public.states/items/5
```

### Parameters
* `properties=PROP-LIST`- return only specific properties (comma-separated).
  If PROP-LIST is empty, no properties are returned.
  If not present, all properties are returned.dinates to use N decimal places

Example Response
```json
{
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "geometry": {
                "type": "MultiPolygon",
                "coordinates": [
                    ...
                ]
            },
            "properties": {
                "gid": 5,
                "state_name": "Nevada",
                "state_fips": "32",
                "sub_region": "Mountain",
                "state_abbr": "NV",
                "population": 2994047
            }
        }
    ]
}
```
