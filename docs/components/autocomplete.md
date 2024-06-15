<script setup>
import { GoogleMap, Marker } from '@lib'
import { apiPromise } from '@docs/shared'

const center = { lat: 40.689247, lng: -74.044502 }
</script>

## Options

See the [AutocompleteOptions](https://developers.google.com/maps/documentation/javascript/reference/marker#MarkerOptions) for supported attributes.


```vue
<script setup>
import { GooglePlaceAutocomplete } from 'vue3-google-map'
</script>

<template>
  <GooglePlaceAutocomplete
    ref="address"
    api-key="YOUR_GOOGLE_MAPS_API_KEY"
    place_changed="placeChangedCallback"
    :component-restrictions="{ country: 'CA' }"
    :fields="['address_components', 'geometry']"
    :input-events="{
      'focus': onInputElementFocusCallback,
      'click': onInputClickCallback,
    }"
    :types="['address']"
  ></GooglePlaceAutocomplete>
</template>
```

## Events

You can listen for [the following events](https://developers.google.com/maps/documentation/javascript/reference/places-widget#Autocomplete-Events) on the `GooglePlaceAutocomplete` component.

Note that unlike the Google's implementation, the `place_changed` event will pass the the selected place as first (and only) argument.

The input's selected value can otherwhise be accessed like this: `this.$refs.address.autocomplete.getPlace();`

Additional native events can be bound to the input element using the `:input-events` attribute.
