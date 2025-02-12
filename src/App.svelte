<script>

import { onDestroy } from 'svelte';
//import { browser } from '$app/environment';
import * as markerIcons from './markers.js';

let map;
let mapElement;
let L;
let markerLayers;

const initialView = [39.8283, -98.5795];

const markerLocations = [
    [29.8283, -96.5795],
    [37.8283, -90.5795],
    [43.8283, -102.5795],
    [48.40, -122.5795],
    [43.60, -79.5795],
    [36.8283, -100.5795],
    [38.40, -122.5795],
];


function markerIcon(count) {
    let html = `<div class="map-marker"><div>${markerIcons.library}</div><div class="marker-text">${count}</div></div>`;
    return L.divIcon({
        html,
        className: 'map-marker'
    });
}

async function startMap() {
    try {
        L = window.L;
        map = createMap(mapElement);
        markerLayers = L.layerGroup();
        markerLayers.addTo(map);
    } catch (error) {
        console.error(error);
    }
}

function createMap(container) {
    try {
        let m = L.map(container, { preferCanvas: true }).setView(initialView, 15);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution:
                '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(m);

        return m;
    } catch (error) {
        console.error(error);
    }
}

function createMarker(loc) {

    let count = Math.ceil(Math.random() * 25);
    let icon = markerIcon(count);

    let marker = L.marker(loc, {icon})
        .addTo(map)
        .bindPopup('popup text')
    return marker;
}

function resizeMap() {
    if (map) {
        map.invalidateSize();
    }
}

$: if (map && initialView && markerLayers) {
    try {
        const marker = createMarker(initialView);
        markerLayers.addLayer(marker);
    } catch (error) {
        console.error(error);
    }
}

</script>

<svelte:window on:resize={resizeMap} />

<svelte:head>
    <script src="https://unpkg.com/leaflet@1.6.0/dist/leaflet.js" on:load={startMap}></script>
    <link
        rel="stylesheet"
        href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css"
        integrity="sha512-xwE/Az9zrjBIphAcBb3F6JVqxf46+CDLwfLMHloNu6KEQCAWi6HcDUbeOfBIptF7tcCzusKFjFw2yuvEpDL9wQ=="
        crossorigin=""
    />
</svelte:head>

<div class="map" bind:this={mapElement}></div>

<style>

    .map {
        width: 100%;
        height:100%;
        text-align: center;
        font-weight: 600;
        background-color: #444;
        color: #eee;
        border-radius: 0.5rem;
    }

    /*
    .map {
        width: 30px;
        transform: translateX(-50%) translateY(-25%);
    }
    */

</style>

