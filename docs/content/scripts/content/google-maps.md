---
title: Google Maps
description: A simple and performant Google Maps component.
links:
  - label: Source
    icon: i-simple-icons-github
    to: https://github.com/nuxt/scripts-and-assets/blob/main/modules/nuxt-third-party-capital/src/runtime/components/GoogleMaps.ts
    size: xs
---

The `<GoogleMaps>` component uses the [Maps JavaScript API](https://developers.google.com/maps/documentation/javascript) to load a map to your page.

::callout
You need an API key to use Google Maps.
::

## Example with Query

```vue
<template>
  <div>
    <GoogleMaps
      api-key="API_KEY"
      width="600"
      height="400"
      q="Space+Needle,Seattle+WA"
    />
  </div>
</template>
```

## Example with Coordinates

```vue
<template>
  <div>
    <GoogleMaps
      api-key="API_KEY"
      width="600"
      height="400"
      :center="{ lat: 47.62065090386302, lng: -122.34932031714334 }"
    />
  </div>
</template>
```

## Props

- `apiKey`: The [API key](https://developers.google.com/maps/documentation/javascript/get-api-key) to use Google Maps API.
  - **type**: `string`
  - **required**
- `q`: A query to search for.
  - **type**: `string`
- `center`: Coordinates to set the map to
  - **type**: `LatLng`
- `zoom`: Zoom value for the map
  - **type**: `number`
- `width`: Width of the player
  - **type**: `string`
- `height`: Height of the player
  - **type**: `string`
- `params`: [Parameters](https://developers.google.com/maps/documentation/javascript/load-maps-js-api#optional_parameters) for the map.

Either a query (q) or coordinates (center) are needed for the map to function properly.