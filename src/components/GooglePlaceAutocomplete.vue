<script lang="ts">
import { defineComponent, PropType, ref, onMounted, onBeforeUnmount, provide, markRaw } from "vue";
import { autocompleteSymbol, apiSymbol } from "../shared/index";
import { Loader, Library } from "@googlemaps/js-api-loader";

let loaderInstance: Loader | undefined;

export default defineComponent({
  emits: ["place_changed"],
  props: {
    apiPromise: {
      type: Promise as PropType<Promise<typeof google>>,
    },
    apiKey: {
      type: String,
      default: "",
    },
    version: {
      type: String,
      default: "weekly",
    },
    region: {
      type: String,
      required: false,
    },
    language: {
      type: String,
      required: false,
    },
    nonce: {
      type: String,
      default: "",
    },
    // Field options
    name: {
      type: String,
      required: false,
    },
    placeholder: {
      type: String,
      required: false,
    },
    modelValue: {
      type: String,
      required: false,
    },
    inputEvents: {
      type: Object as PropType<Record<string, (e: Event) => void>>,
      default: {},
      required: false,
    },
    // Autocomplete options:
    bounds: {
      type: Object as PropType<google.maps.LatLngBounds | google.maps.LatLngBoundsLiteral>,
      required: false,
    },
    componentRestrictions: {
      type: Object as PropType<google.maps.places.ComponentRestrictions>,
      required: false,
    },
    fields: {
      type: Array as PropType<string[]>,
      required: false,
    },
    strictBounds: {
      type: Boolean,
      required: false,
    },
    types: {
      type: Array as PropType<string[]>,
      required: false,
    },
  },
  setup(props, { emit }) {
    const autocompleteRef = ref<HTMLInputElement>();
    const ready = ref(false);
    const modelValue = ref('');

    const autocomplete = ref<google.maps.places.Autocomplete>();
    const api = ref<typeof google.maps>();

    provide(autocompleteSymbol, autocomplete);
    provide(apiSymbol, api);

    const loadMapsAPI = () => {
      try {
        const { apiKey, region, version, language, nonce } = props;
        const libraries = ["places"];
        loaderInstance = new Loader({ apiKey, region, version, language, libraries: libraries as Library[], nonce });
      } catch (err) {
        // Loader instantiated again with different options, which isn't allowed by js-api-loader
        console.error(err);
      }
    };

    const resolveOptions = (): google.maps.places.AutocompleteOptions => {
      const options: google.maps.places.AutocompleteOptions = { ...props };
      const keys = Object.keys(options) as (keyof google.maps.places.AutocompleteOptions)[];

      // Strip undefined keys. Without this Map.setOptions doesn't behave very well.
      keys.forEach((key) => {
        if (options[key] === undefined) delete options[key];
      });

      return options;
    };

    const setupAutocomplete= (_google: typeof google) => {
      api.value = markRaw(_google.maps);
      autocomplete.value = markRaw(new _google.maps.places.Autocomplete(autocompleteRef.value as HTMLInputElement, resolveOptions()));

      autocomplete.value?.addListener("place_changed", () => {
        emit("place_changed", autocomplete.value?.getPlace())
      });

      ready.value = true;
    };

    onMounted(() => {
      if (props.apiPromise && props.apiPromise instanceof Promise) {
        props.apiPromise.then(setupAutocomplete);
      } else {
        loadMapsAPI();

        (loaderInstance as Loader).load().then(setupAutocomplete);
      }
    });

    onBeforeUnmount(() => {
      if (autocomplete.value) api.value?.event.clearInstanceListeners(autocomplete.value);
    });

    const attributes = {
      class: "google-autocomplete-input",
      name: props.name || "google-autocomplete-input",
      placeholder: props.placeholder || "",
      type: "text",
      value: modelValue.value || "",
    };

    return {
      api,
      attributes,
      autocomplete,
      autocompleteRef,
      ready,
    };
  },
})
</script>

<template>
  <div>
    <input ref="autocompleteRef" v-bind="attributes" v-on="$props.inputEvents" />
    <slot v-bind="{ ready, autocomplete, api }" />
  </div>
</template>
