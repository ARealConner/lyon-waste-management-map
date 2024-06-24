<script>
	import { onMount } from 'svelte';
	import 'leaflet/dist/leaflet.css';

	let L;
	let map;

	onMount(async () => {
		if (typeof window !== 'undefined') {
			// Dynamically import Leaflet on the client-side
			L = await import('leaflet');

			// Initialize the map
			map = L.map('map', {
				preferCanvas: true // Enable canvas rendering
			}).setView([45.75, 4.85], 13);

			// Add a tile layer
			L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
				attribution: '&copy; OpenStreetMap contributors'
			}).addTo(map);

			// Fetch GeoJSON data
			const compostBins = await (
				await fetch(
					'https://data.grandlyon.com/geoserver/metropole-de-lyon/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=metropole-de-lyon:gic_collecte.bornecompost&outputFormat=application/json&SRSNAME=EPSG:4171&startIndex=0&sortBy=gid'
				)
			).json();

			const garbageBins = await (
				await fetch(
					'https://data.grandlyon.com/geoserver/metropole-de-lyon/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=metropole-de-lyon:gin_nettoiement.gincorbeille&outputFormat=application/json&SRSNAME=EPSG:4171&startIndex=0&sortBy=gid'
				)
			).json();

			const wasteCollectionCenters = await (
				await fetch(
					'https://data.grandlyon.com/geoserver/metropole-de-lyon/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=metropole-de-lyon:gip_proprete.gipdecheterie_3_0_0&outputFormat=application/json&SRSNAME=EPSG:4171&startIndex=0&sortBy=gid'
				)
			).json();

			const bottleCollection = await (
				await fetch(
					'https://data.grandlyon.com/geoserver/metropole-de-lyon/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=metropole-de-lyon:eco_ecologie.ecorebooteille&outputFormat=application/json&SRSNAME=EPSG:4171&startIndex=0&sortBy=gid'
				)
			).json();

			const glassBins = await (
				await fetch(
					'https://data.grandlyon.com/geoserver/metropole-de-lyon/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=metropole-de-lyon:gic_collecte.siloverre&outputFormat=application/json&SRSNAME=EPSG:4171&startIndex=0&sortBy=gid'
				)
			).json();

			const householdWasteBins = await (
				await fetch(
					'https://data.grandlyon.com/geoserver/metropole-de-lyon/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=metropole-de-lyon:gic_collecte.siloverre&outputFormat=application/json&SRSNAME=EPSG:4171&startIndex=0&sortBy=gid'
				)
			).json();

			const recyclingDropOff = await (
				await fetch(
					'https://data.grandlyon.com/geoserver/metropole-de-lyon/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=metropole-de-lyon:gic_collecte.collecteselective&outputFormat=application/json&SRSNAME=EPSG:4171&startIndex=0&sortBy=gid'
				)
			).json();

			// Create a canvas renderer for the markers
			const canvasRenderer = L.canvas();

			// Create marker layers for each category
			const compostMarkers = L.geoJSON(compostBins, {
				pointToLayer: (feature, latlng) =>
					L.circleMarker(latlng, {
						renderer: canvasRenderer,
						color: 'green',
						fillColor: 'green',
						fillOpacity: 0.7
					}),
				onEachFeature: (feature, layer) => {
					// Create a popup with the feature properties
					const popupContent = Object.entries(feature.properties)
						.map(([key, value]) => `<b>${key}:</b> ${value}`)
						.join('<br>');
					layer.bindPopup(popupContent);
				}
			});

			const garbageMarkers = L.geoJSON(garbageBins, {
				pointToLayer: (feature, latlng) =>
					L.circleMarker(latlng, {
						renderer: canvasRenderer,
						color: 'red',
						fillColor: 'red',
						fillOpacity: 0.7
					}),
				onEachFeature: (feature, layer) => {
					// Create a popup with the feature properties
					const popupContent = Object.entries(feature.properties)
						.map(([key, value]) => `<b>${key}:</b> ${value}`)
						.join('<br>');
					layer.bindPopup(popupContent);
				}
			});

			const wasteCollectionCenterMarkers = L.geoJSON(wasteCollectionCenters, {
				pointToLayer: (feature, latlng) =>
					L.circleMarker(latlng, {
						renderer: canvasRenderer,
						color: 'blue',
						fillColor: 'blue',
						fillOpacity: 0.7
					}),
				onEachFeature: (feature, layer) => {
					// Create a popup with the feature properties
					const popupContent = Object.entries(feature.properties)
						.map(([key, value]) => `<b>${key}:</b> ${value}`)
						.join('<br>');
					layer.bindPopup(popupContent);
				}
			});

			const bottleCollectionMarkers = L.geoJSON(bottleCollection, {
				pointToLayer: (feature, latlng) =>
					L.circleMarker(latlng, {
						renderer: canvasRenderer,
						color: 'orange',
						fillColor: 'orange',
						fillOpacity: 0.7
					}),
				onEachFeature: (feature, layer) => {
					// Create a popup with the feature properties
					const popupContent = Object.entries(feature.properties)
						.map(([key, value]) => `<b>${key}:</b> ${value}`)
						.join('<br>');
					layer.bindPopup(popupContent);
				}
			});

			const glassBinMarkers = L.geoJSON(glassBins, {
				pointToLayer: (feature, latlng) =>
					L.circleMarker(latlng, {
						renderer: canvasRenderer,
						color: 'purple',
						fillColor: 'purple',
						fillOpacity: 0.7
					}),
				onEachFeature: (feature, layer) => {
					// Create a popup with the feature properties
					const popupContent = Object.entries(feature.properties)
						.map(([key, value]) => `<b>${key}:</b> ${value}`)
						.join('<br>');
					layer.bindPopup(popupContent);
				}
			});

			const householdWasteBinMarkers = L.geoJSON(householdWasteBins, {
				pointToLayer: (feature, latlng) =>
					L.circleMarker(latlng, {
						renderer: canvasRenderer,
						color: 'brown',
						fillColor: 'brown',
						fillOpacity: 0.7
					}),
				onEachFeature: (feature, layer) => {
					// Create a popup with the feature properties
					const popupContent = Object.entries(feature.properties)
						.map(([key, value]) => `<b>${key}:</b> ${value}`)
						.join('<br>');
					layer.bindPopup(popupContent);
				}
			});

			const recyclingDropOffMarkers = L.geoJSON(recyclingDropOff, {
				pointToLayer: (feature, latlng) =>
					L.circleMarker(latlng, {
						renderer: canvasRenderer,
						color: 'yellow',
						fillColor: 'yellow',
						fillOpacity: 0.7
					}),
				onEachFeature: (feature, layer) => {
					// Create a popup with the feature properties
					const popupContent = Object.entries(feature.properties)
						.map(([key, value]) => `<b>${key}:</b> ${value}`)
						.join('<br>');
					layer.bindPopup(popupContent);
				}
			});

			// Create a layer control and add it to the map
			const layerControl = L.control
				.layers(null, {
					'Compost Bins': compostMarkers,
					'Garbage Bins': garbageMarkers,
					'Waste Collection Centers': wasteCollectionCenterMarkers,
					'Bottle Collection': bottleCollectionMarkers,
					'Glass Bins': glassBinMarkers,
					'Household Waste Bins': householdWasteBinMarkers,
					'Recycling Drop-off': recyclingDropOffMarkers
				})
				.addTo(map);

			// Add the layers to the map
			compostMarkers.addTo(map);
			garbageMarkers.addTo(map);
			wasteCollectionCenterMarkers.addTo(map);
			bottleCollectionMarkers.addTo(map);
			glassBinMarkers.addTo(map);
			householdWasteBinMarkers.addTo(map);
			recyclingDropOffMarkers.addTo(map);
		}
	});
</script>

<div id="map"></div>

<style>
	#map {
		height: 100vh;
	}
</style>
