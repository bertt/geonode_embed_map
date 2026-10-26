# Embed a GeoNode map in a GitHub Pages site

This repository shows how to embed a map created in [GeoNode](https://geonode.org) in a static HTML page hosted on GitHub Pages. The example uses a map from the public demo instance `stable.demo.geonode.org`.

Live demo: https://bertt.github.io/geonode_embed_map/

> Demo instances are reset periodically. If the example map is gone, pick another one from https://stable.demo.geonode.org/maps/ and update the URL in `index.html`.

## Option 1: iframe (simplest)

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
2. Take the map ID from the map URL (`/maps/<MAP_ID>/`) and append `/embed`.
3. Put the iframe in your `index.html`.
4. Your GeoNode must be served over **HTTPS**, otherwise browsers block it as mixed content on an HTTPS GitHub Pages site.
5. If the iframe stays empty, GeoNode may forbid framing. In `local_settings.py`/environment, allow it, e.g. `X_FRAME_OPTIONS = "ALLOWALL"` or a CSP header such as `frame-ancestors https://YOURNAME.github.io`.

## Option 2: custom map with Leaflet / OpenLayers / MapLibre

Use the OGC services of the layers in your map (WMS/WFS/WMTS):

```html
<div id="map" style="height:600px"></div>
<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css"/>
<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
<script>
  const map = L.map('map').setView([52.1, 5.3], 7);
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
  L.tileLayer.wms('https://YOUR-GEONODE/geoserver/ows', {
    layers: 'geonode:LAYER_NAME', format: 'image/png', transparent: true
  }).addTo(map);
</script>
```

WMS images usually work without extra setup; for WFS/GeoJSON requests enable **CORS** for `https://YOURNAME.github.io` on GeoServer/GeoNode.

## Publish on GitHub Pages

1. Create a repository and add `index.html` in the root.
2. *Settings → Pages → Build and deployment → Deploy from a branch* → `main` / `(root)` → Save.
3. The site is available at `https://YOURNAME.github.io/REPO/` after a minute or so.

Or with the GitHub CLI:

```sh
gh repo create geonode_embed_map --public --source . --push
gh api -X POST repos/YOURNAME/geonode_embed_map/pages -f "source[branch]=main" -f "source[path]=/"
```
