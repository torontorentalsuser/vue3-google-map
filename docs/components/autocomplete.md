<script setup>
import { GoogleMap, Marker } from '@lib'
import { apiPromise } from '@docs/shared'

const center = { lat: 40.689247, lng: -74.044502 }
</script>

# Marker

Use the `Marker` component to draw markers, drop pins or any custom icons on a map.

## Options


```vue
<script setup>
import { GooglePlaceAutocomplete } from 'vue3-google-map'
</script>

<template>
  <GooglePlaceAutocomplete
    api-key="YOUR_GOOGLE_MAPS_API_KEY"
    place_changed="myCallback"
    :component-restrictions="{ country: 'CA' }"
    :fields="['address_components', 'geometry']"
    :types="['address']"
  ></GooglePlaceAutocomplete>
</template>
```

## Events
