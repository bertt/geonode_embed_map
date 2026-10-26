# Embed a GeoNode map in an external site

This repository shows how to embed a map created in [GeoNode](https://geonode.org) in an external static HTML page. The examples use map [Entidades_PVCV](https://stable.demo.geonode.org/catalogue/#/map/19196) from the public demo instance `stable.demo.geonode.org`.

Live demos:

- Overview: https://bertt.github.io/geonode_embed_map/
- Option 1 (iframe): https://bertt.github.io/geonode_embed_map/iframe.html
- Option 2 (MapLibre, WMS + GetFeatureInfo): https://bertt.github.io/geonode_embed_map/maplibre-popup.html

> Demo instances are reset periodically. If the example map is gone, pick another one from https://stable.demo.geonode.org/catalogue/#/maps and update the URL in `iframe.html` and `maplibre-popup.html`.

## Option 1: iframe (simplest)

Live example: https://bertt.github.io/geonode_embed_map/iframe.html (source: [`iframe.html`](iframe.html))

![GeoNode map embedded with an iframe](images/iframe-example.png)

GeoNode provides an embed URL for every map:

```
https://YOUR-GEONODE/maps/<MAP_ID>/embed
```

```html
<iframe
  src="https://YOUR-GEONODE/maps/<MAP_ID>/embed"
  style="width:100%;height:600px;border:0"
  allowfullscreen>
</iframe>
```

Steps:

1. In GeoNode, make the map publicly viewable (map page → *Share* / *Permissions* → allow *Anyone* to view). Make sure the layers used in the map are public too.
2. Take the map ID from the map URL (e.g. `/catalogue/#/map/<MAP_ID>` in GeoNode 4.x+, or `/maps/<MAP_ID>/` in older versions) and append `/embed` to `https://YOUR-GEONODE/maps/<MAP_ID>`.
3. Put the iframe in your HTML page (see [`iframe.html`](iframe.html)).
4. Your GeoNode must be served over **HTTPS**, otherwise browsers block it as mixed content on an HTTPS website.
5. If the iframe stays empty, GeoNode may forbid framing. In `local_settings.py`/environment, allow it, e.g. `X_FRAME_OPTIONS = "ALLOWALL"` or a CSP header such as `frame-ancestors https://YOUR-SITE`.

## Option 2: custom map with MapLibre GL JS

Use the OGC services of the layers in your map. This example shows the layer as a WMS raster source and queries the attributes of a clicked feature with WMS GetFeatureInfo:

```js
map.addSource('geonode', {
  type: 'raster',
  tileSize: 256,
  tiles: ['https://YOUR-GEONODE/geoserver/ows?service=WMS&version=1.1.1&request=GetMap' +
    '&layers=geonode:LAYER_NAME&styles=&format=image/png&transparent=true' +
    '&srs=EPSG:3857&width=256&height=256&bbox={bbox-epsg-3857}']
});
map.addLayer({ id: 'geonode', type: 'raster', source: 'geonode' });
```

Live example (WMS layer with click popups using GetFeatureInfo): https://bertt.github.io/geonode_embed_map/maplibre-popup.html (source: [`maplibre-popup.html`](maplibre-popup.html))

![GeoNode layer in a MapLibre GL JS map](images/maplibre-example.png)

GetFeatureInfo requests from your page are cross-origin: enable **CORS** for your site's origin (e.g. `https://YOUR-SITE`) on GeoServer/GeoNode (the demo already does).
