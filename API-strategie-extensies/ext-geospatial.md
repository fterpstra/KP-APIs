## Geospatial

<p class='warning'>This extension is in development and may be modified at any time.</p>

REST APIs for handling geospatial features may provide spatial filtering. There is a distinction between retrieving geometries in the result (response) and supplying a spatial filter in the call (request). When requesting parcel information, users do not necessarily require the geometry. A name or parcel ID may be sufficient.

<div class="rule" id="api-34">
  <p class="rulelab"><strong>API-34</strong>: Support GeoJSON for geospatial APIs</p>
  <p>For geospatial APIs, preferably use the GeoJSON format [[rfc7946]].</p>
</div>

### Result (response)

In a JSON API the geometry is returned as GeoJSON, wrapped in a separate GeoJSON object.

<div class="rule" id="api-35">
  <p class="rulelab"><strong>API-35</strong>: Embed GeoJSON as part of the JSON resource</p>
  <p>When a JSON (<code>application/json</code>) response contains a geometry, represent it in the same way as the <code>geometry</code> object of GeoJSON.</p>
  <pre>
  {
    "naam": "Paleis Soestdijk",
    "locatie":  {
      "type": "Point",
      "coordinates": [125.6, 10.1]
    }
  }
  </pre>
</div>

### Call (requests)

A spatial filter can be complex and large. It is best practice to supply complex queries in the body, not in the request URI. Since `GET` may not have a payload (although supported by some clients) use a `POST` request to a separate endpoint. For example, a GEO query to all *panden* where the geometry in the field `_geo` (there may be multiple geometry fields) contains a GeoJSON object (in this case a `Point`, so one coordinate pair):

<div class="rule" id="api-36">
  <p class="rulelab"><strong>API-36</strong>: Provide a <code>POST</code> endpoint for geo queries</p>
  <p>Spatial queries are sent in a <code>POST</code> to a dedicated endpoint.</p>
  <pre>
  // POST /api/v1/panden/_zoek with request body:
  {
    "_geo": {
      "contains": {
        "type": "Point",
        "coordinates": [5.9623762, 52.2118093]
      }
    }
  }
  </pre>
  <p>Other geospatial operators like <code>intersects</code> or <code>within</code> can be used as well.</p>
</div>

<div class="rule" id="api-37">
  <p class="rulelab"><strong>API-37</strong>: Support mixed queries at <code>POST</code> endpoints</p>
  <p>The <code>POST</code> endpoint is preferably set up as a generic query endpoint to support combined queries:</p>
  <pre>
  // POST /api/v1/panden/_zoek with request body:
  {
    "_geo": {
      "contains": {
        "type": "Point",
        "coordinates": [5.9623762, 52.2118093]
      }
    },
    "status": "Actief"
  }
  </pre>
</div>

<div class="rule" id="api-38">
  <p class="rulelab"><strong>API-38</strong>: Put results of a global spatial query in the relevant geometric context</p>
  <p>In case of a global query <code>/api/v1/_zoek</code>, results should be placed in the relevant geometric context, because results from different collections are retrieved. Express the name of the collection to which the results belongs in the singular form using the property <code>type</code>. For example:</p>
  <pre>
  // POST /api/v1/_zoek:
  {
    "currentPage": 1,
    "nextPage": 2,
    "pageSize": 10,
    "_embedded": {
      "items": [
        {
          "type": "enkelbestemming",
          "_links": {
            "self": {
              "href": "https://api.example.org/v1/enkelbestemmingen/1234"
            }
          }
        },
        {
          "type": "dubbelbestemming",
          "_links": {
            "self": {
              "href": "https://api.example.org/v1/dubbelbestemmingen/8765"
            }
          }
        }
      ]
    }
  }
  </pre>
</div>

### CRS negotiation

The default CRS (Coordinate Reference System) for GeoJSON is WGS84. This is the global coordinate reference system that can be applied world-wide. Due the datum and the tectonic displacements it is not accurate enough for local coordinate reference systems like ETRS89 (EPSG:4258, European), or RD/Amersfoort (EPSG:28992, Dutch).

Since most client-side mapping libraries use WGS84, the W3C/OGC working group *Spatial Data on the Web* recommends to use this as the default coordinate reference system. Thus, spatial data can be mapped without any complex transformations. The API strategy caters for this supporting not only ETRS89 and RD/Amersfoort, but also WGS84 and Web Mercator (EPSG:3857).

<div class="rule" id="api-39">
  <p class="rulelab"><strong>API-39</strong>: Use ETRS89 as the preferred coordinate reference system (CRS)</p>
  <p>General usage of the European ETRS89 coordinate reference system (CRS) is preferable, but is not necessarily the default CRS. Hence, the CRS has to be explicitly included in each request.</p>
</div>

The CRS can be specified for request and response individually using custom headers: RD/Amersfoort, ETRS89, WGS84, and Web Mercator.

The guiding priciples for CRS support:

- Source systems record coordinates as they enter the system (legal context);
- Define a default CRS in the API, if the consumer does not specify the CRS it is assumed it uses the default.
- Coordinate reference systems API strategy: request/response in RD; ETRS89; WGS84; Web Mercator;
- Consider no-regret: record both in ETRS89 and RD/Amersfoort instead of on-the-fly transformation;
- Use RDNAPTRANS™ 2018 to transform RD/Amersfoort to ETRS89 (correction grid);
- Presentation depending on context (e.g. user requirements);
- Exchange format (notation) ETRS89 and WGS84 X Y in decimal degrees: DD.ddddddddd (for example: `5.962376256, 52.255023450`)
- Exchange format (notation) RD and Web Mercator X Y in meters: `195427.5200 311611.8400`

<div class="rule" id="api-40">
  <p class="rulelab"><strong>API-40</strong>: Pass the coordinate reference system (CRS) of the request and the response in the headers</p>
  <p>The coordinate reference system (CRS) for both the request and the response are passed as part of the request headers and reponse headers. In case this header is missing, send the HTTP status code <code>412 Precondition Failed</code>.</p>
</div>

The following headers are purely meant for negotiation between the client and the server. Depending on the application, the request not only contains geometries but also specific meta data, e.g. the original realistion including the collection date.

Request and response may be based on another coordinate reference system. This applies the HTTP-mechanism for content negotiation. The CRS of the geometry in the request (request body) is specified using the header `Content-Crs`.

|HTTP header|Value|Explanation|
|-|-|-|
|`Content-Crs`|EPSG:4326|WGS84, global|
|`Content-Crs`|EPSG:3857|Web Mecator, global|
|`Content-Crs`|EPSG:4258|ETRS89, European|
|`Content-Crs`|EPSG:28992|RD/Amersfoort, Dutch|

The preferred CRS for the geometry in the response (response body) is specified using the header `Accept-Crs`.

|HTTP header|Value|Explanation|
|-|-|-|
|`Accept-Crs`|EPSG:4326|WGS84, global|
|`Accept-Crs`|EPSG:3857|Web Mercator, global|
|`Accept-Crs`|EPSG:4258|ETRS89, European|
|`Accept-Crs`|EPSG:28992|RD/Amersfoort, Dutch|

### CRS transformation

Certified software is available to the national government to transform between coordinate reference systems.

<div class="rule" id="api-41">
  <p class="rulelab"><strong>API-41</strong>: Use content negotiation to serve different CRSs</p>
  <p>The CRS for the geometry in the response body is defined using the <code>Accept-Crs</code> header. In case the API does not support the requested CRS, send the HTTP status code <code>406 Not Acceptable</code>.</p>
</div>
