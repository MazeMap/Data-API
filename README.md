# [DEPRECATED / OUTDATED] 
Please refer to MazeMap Customer Success or Sales channels for newer documentation.
[MazeMap Contact Page](https://www.mazemap.com/contact)
---

MazeMap Data API v1.0
=====================

This document describes version 1.0 of the "Publicly available MazeMap Data API".

---

#####Disclaimer
MazeMap AS reserve the right to make changes to the API and it's documentation any time in the future. Contact us for agreements of API maintentance for specific API versions.

---

#####Questions, issues or problems?
If you have any issues, please contact us, by mail at support@mazemap.com or create an issue here in GitHub.


---

Index
====

[1. Getting buildings+floors for a specific campus](#getting-building-floors)
> [1.1 Building model properties](#building-model-properties)
> [1.2 Floor model properties](#floor-model-properties)

[2. Getting floor geometries for a specific campus](#getting-floor-geometries)
> [2.1 FloorOutline model properties](#flooroutline-model-properties)


[3. Getting POIs](#getting-pois)
> [3.1 Using POI id](#using-poi-id)
> [3.2 Using 'local' POI reference identifier](#using-poi-reference)
> [3.3 Using POI type id](#using-poi-type-id)
> [3.4 POI model properties](#poi-model-properties)

---

### 1. <a name="getting-building-floors"></a>Getting buildings+floors for a specific campus


```javascript
https://api.mazemap.com/api/buildings/?campusid=1&srid=4326
```

URL Parameter | value | description
--- | --- | ---
`campusid` | integer | Defines the campus for which you want the buildings
`srid` | 4326 or 900913 | Defines the projection format you want for the geometries in the building objects


Outputs a JSON object with an array of buildings.

Example:


```json
    {
      "buildings": [
        {
          "buildingNames": [
            {
              "lang": "en",
              "name": "Building 14",
            }
          ],
          "floors": [
            {
              "name": "1",
              "id": 427,
              "floorOutlineId": 997,
              "z": 1.0,
              "buildingId": 100
            }
          ],
          "campusId": 6,
          "simpleGeom": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -121.92923462256475,
                  37.40729174061478
                ],
                [
                  -121.92920441494438,
                  37.4075571005258
                ],
                [
                  -121.92860554798439,
                  37.407543499724135
                ],
                [
                  -121.92855995948898,
                  37.40718728129579
                ],
                [
                  -121.928609099326,
                  37.40673053548937
                ],
                [
                  -121.9292079662623,
                  37.40674413644115
                ],
                [
                  -121.92923462256475,
                  37.40729174061478
                ]
              ]
            ]
          },
          "id": 100,
          "name": "Building 14"
        }
      ]
    }
```

---

### <a name="building-model-properties"></a>1.1 Building model properties

Property | description
--- | --- | ---
`id` | The MazeMap id of the building.
`campusId` | The specific campusid the building belongs to
`name` | The primary/default name of the building
`buildingNames` | An array of all the names belonging to the building. Might contain more names in the future.
`floors` | An array of floor-model objects.
`simpleGeom` | A GeoJSON formatted geometry object that defines the *rough* outline of a building

---

### <a name="floor-model-properties"></a>1.2 Floor model properties

Property | description
--- | --- | ---
`id` | The MazeMap id of the floor.
`buildingId` | Reference to the id of the parent building for the floor
`name` | The primary/default name of the floor
`z` | Each floor has it's specific assigned z-value. Usually ground floors have z-value 1.0, and basement floors have negative values, but not necessarily.
`floorOutlineId` | An id referencing the outline geometry of the floor. These are fetched in a seperate API call.



---

2. <a name="getting-floor-geometries"></a>Getting floor geometries for a specific campus
-----------------------------------------
If you want to draw floor outlines, or need the geometric location of a floor, you need these geometries. Use the `floorOutlineId` property in the `floor` model to find the specific `floorOutline`.

```javascript
https://api.mazemap.com/api/flooroutlines/?campusid=6&srid=4326
```

URL Parameter | value | description
--- | --- | ---
`campusid` | integer | Defines the campus for which you want the floor geometries
`srid` | 4326 or 900913 | Defines the projection format you want for the geometries


Outputs a GeoJSON FeatureCollection object, where each Feature represents a floorOutline for a specific floor.

It is recommended to parse the object to make a dictionary, using the `id` property as key and `geometry` as the value

Example:


```json
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "id": 996,
          "properties": {
            "floorIds": [
              426
            ]
          },
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -121.92785096318727,
                  37.40612880508644
                ],
                [
                  -121.92789094367477,
                  37.406207044478194
                ],
                [
                  -121.92764327054981,
                  37.40629280530124
                ],
                [
                  -121.92735038833716,
                  37.40644024173961
                ],
                [
                  -121.92683331503694,
                  37.40569994518988
                ],
                [
                  -121.92771885028621,
                  37.40594480678254
                ],
                [
                  -121.92785096318727,
                  37.40612880508644
                ]
              ]
            ]
          }
        }
      ]
    }
```

---

### <a name="flooroutline-model-properties"></a>2.1 FloorOutline model properties

Property | description
--- | --- | ---
`type` | The feature type, according to the GeoJSON standard.
`id` | The MazeMap id of the floorOutline.
`geometry` | A GeoJSON formatted geometry object. Mostly (but not limited to) Polygon or MultiPolygon.

---

### <a name="getting-pois"></a>3. Getting POIs

A POI can be any point/area of interest, but in most cases, used to specify individual *rooms*.
Every POI has a unique MazeMap id, but can also be configured to have *local reference tags*. These can be set-up by for each specific campus. Example of such a *reference tag* could be "G301-211" which might be a unique room identifier string already in use by other systems at the campus.

---

### <a name="using-poi-id"></a>3.1 Using POI id
```javascript
http://api.mazemap.com/api/pois/{poiId}/?srid=4326
```

URL Parameter | value | description
--- | --- | ---
`{poiId}` | integer | Specifies the POI ID
`srid` | 4326 or 900913 | Defines the projection format you want for the geometries


Outputs a JSON object representing a POI model

Example:
```json
    {
      "poiId": 1337331,
      "campusId": 1337,
      "title": "Leet Room",
      "floorId": 429,
      "geometry": {
        "type": "Point",
        "coordinates": [
          37.40703563531919,
          -121.92904793892215
        ]
      }
    }
```
---

### <a name="using-poi-reference"></a>3.2 Using 'local' POI reference identifier

```javascript
http://api.mazemap.com/api/pois/?identifier=LEET-1337&campuscollectiontag=applecampuses&campusid=1337
```

URL Parameter | value | description
--- | --- | ---
`identifier` | string | Specifies the local poi identifier. Example: "LEET-1337" or "g-121".
`campuscollectiontag` | string | Specififes the collection of campuses for which to search for the identifier. Example: "applecampuses". Can be ommitted.
`srid` | 4326 or 900913 | Defines the projection format you want for the geometries
`campusid` | integer | Specififes the specific campus for which to search for the identifier. Can be ommitted.


Outputs a JSON object with an array of possible POIs. Has the same properties as the POI model.
The out

Example:
```json
    {
      "pois": [
        {
          "poiId": 71768,
          "campusId": 1,
          "title": "G101",
          "floorId": 155,
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  10.401808445444981,
                  63.418510637283134
                ],
                [
                  10.40178483194168,
                  63.41850571851106
                ],
                [
                  10.401790900368464,
                  63.41852715900365
                ],
                [
                  10.401808445444981,
                  63.418510637283134
                ]
              ]
            ]
          }
        }
      ]
    }
```
---


### <a name="using-poi-type-id"></a>3.3 Using POI type id
POIs can be assigned one or more *types*. Examples are *parking spaces*, *toilets*, etc. Each such *type* has a *type id*.
```javascript
http://api.mazemap.com/api/pois/?campusid=1337&typeid=37&srid=4326
```

URL Parameter | value | description
--- | --- | ---
`campusid` | integer | Specififes the specific campus for which to get POIs
`typeid` | integer | Specififes the type of POIs to get
`srid` | 4326 or 900913 | Defines the projection format you want for the geometries


Outputs a JSON object with an array of possible POIs. Has the same properties as the POI model.
The out

Example:
```json
    {
      "pois": [
        {
          "poiId": 39535,
          "campusId": 1,
          "title": "P-2",
          "floorId": 299,
          "geometry": {
            "type": "Point",
            "coordinates": [
              10.404756924531995,
              63.417539227268975
            ]
          }
        }
      ]
    }
```


### <a name="poi-model-properties"></a>3.4 POI model properties

Property | description
--- | --- | ---
`poiId` | The MazeMap id of the POI.
`campusId` | The parent campus of the POI
`title` | The primary/default name of the POI
`floorId` | The MazeMap ID of the floor the POI belongs to
`geometry` | A GeoJSON formatted geometry object

---

