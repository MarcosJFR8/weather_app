<script setup lang="ts">
    // Import necessary functions and types from Vue
    import { ref, onMounted } from 'vue';
    import type { Ref } from 'vue';

    // Define a custom TypeScript type for geographic coordinates
    type GeoLocation = {
        latitude: number; // Latitude of the location
        longitude: number; // Longitude of the location
    };

    // Create a reactive reference to store the geographic coordinates
    // Initially, it is set to `undefined` because no coordinates are available yet
    const coords: Ref<GeoLocation | undefined> = ref(undefined);

    // Create a reactive reference to track if the user has blocked geolocation access
    // Initially, it is set to `false` (geolocation is not blocked)
    const geolocationBlockByUser: Ref<boolean> = ref(false);

    // Define an asynchronous function to fetch the user's geolocation
    const getGeolocation = async (): Promise<void> => {
        // Use the browser's Geolocation API to get the current position
        navigator.geolocation.getCurrentPosition(
            // Success callback: triggered when the user's location is successfully retrieved
            async (position: { coords: GeoLocation }) => {
                // Update the `coords` reactive reference with the retrieved coordinates
                coords.value = position.coords;
            },
            // Error callback: triggered if the user denies geolocation access or an error occurs
            (error: { message: string }) => {
                // Log the error message to the console
                console.error(error.message);
                // Update the `geolocationBlockByUser` reactive reference to `true`
                geolocationBlockByUser.value = true;
            }
        );
    };

    // Use the `onMounted` lifecycle hook to trigger the `getGeolocation` function
    // when the component is mounted (i.e., added to the DOM)
    onMounted(async () => {
        // Call the `getGeolocation` function and wait for it to complete
        await getGeolocation();
    });
</script>

<template>
    <div v-if="coords && !geolocationBlockByUser">
        {{ coords.latitude }}
        {{ coords.longitude }}
    </div>
    <div v-if="geolocationBlockByUser">
        <p>Geolocation blocked by user</p>
    </div>
</template>

<style scoped>
</style>