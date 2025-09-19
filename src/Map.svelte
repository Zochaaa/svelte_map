<script>
    import { onMount } from 'svelte';
  
    /* global google */              // mówi linterowi/TS, że "google" jest globalne
  
    /** @type {HTMLDivElement | null} */
    let mapDiv = null;               // JSDoc daje TS/VSCode typ zamiast "any"
  
    onMount(async () => {
      try {
        await waitForGoogleMaps();   // poczekaj aż loader załaduje google.maps.importLibrary
        const { Map } = await google.maps.importLibrary('maps');
        const { Marker } = await google.maps.importLibrary('marker');
  
        const map = new Map(mapDiv, {
          center: { lat: 52.2297, lng: 21.0122 },
          zoom: 12
        });
  
        new Marker({
          position: { lat: 52.2297, lng: 21.0122 },
          map,
          title: 'Warszawa'
        });
      } catch (err) {
        console.error('Google Maps failed to load:', err);
      }
    });
  
    function waitForGoogleMaps(timeout = 10000) {
      return new Promise((resolve, reject) => {
        if (typeof window.google !== 'undefined' && window.google.maps && window.google.maps.importLibrary) {
          resolve();
          return;
        }
        const start = Date.now();
        const id = setInterval(() => {
          if (typeof window.google !== 'undefined' && window.google.maps && window.google.maps.importLibrary) {
            clearInterval(id);
            resolve();
          } else if (Date.now() - start > timeout) {
            clearInterval(id);
            reject(new Error('Timed out waiting for Google Maps'));
          }
        }, 100);
      });
    }
  </script>
  
  <div bind:this={mapDiv} class="map"></div>
  
  <style>
    .map {
      width: 100%;
      height: 100%;
      min-height: 400px;
    }
  </style>
  