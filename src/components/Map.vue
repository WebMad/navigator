
<template>
    <div id="mapContainer">
        <div class="controls">
            <div class="search">
                <input v-model="searchText" ref="search" type="text">
                <ul v-if="searchText" class="search-results">
                    <li @click="setPlace(result)" v-for="result in resultsSearch" class="search-result">{{ result.Location.Address.Label }}
                    </li>
                </ul>
            </div>
        </div>
        <div style="width: 100%; height: 500px" class="map" ref="map"></div>
        <div ref="placeInfo" class="placeInfo">

        </div>

    </div>
</template>

<script>

    import {debounce} from "debounce"; //для ограничения по времени вызова функции запроса к api

    export default {
        name: "Map",
        data() {
            return {
                map: {},
                platform: {},
                searchText: '',
                behavior: {},
                ui: {},
                geocoder: {},
                resultsSearch: Object,
                marker: Array,
            }
        },
        props: {
            appId: String,
            appCode: String,
            lat: String,
            lng: String,
            width: String,
            height: String
        },
        methods: {
            searchPlace() {
                if(this.searchText !== '') {
                    this.geocoder.geocode({
                        searchText: this.searchText,
                    }, (data) => {

                        console.log(data);
                        this.resultsSearch = data.Response.View[0].Result;

                    }, function (e) {
                        alert(e);
                    });
                }
                else {
                    this.resultsSearch = {};
                }
            },

            setPlace(place) {

                //this.map.removeObject(this.marker);
                //console.log();
                    //this.map.removeObjects();

                this.marker = new H.map.Marker({
                    lat: place.Location.DisplayPosition.Latitude,
                    lng: place.Location.DisplayPosition.Longitude,
                });

                this.map.addObject(this.marker);

                //TODO remove last object

                this.$refs.placeInfo.innerHTML = 'Отмечено: ' + place.Location.Address.Label;

                this.searchText = '';
                this.resultsSearch = {};

            },

            setCurrentPosition() {
                navigator.geolocation.getCurrentPosition((data) => {

                    this.map.setCenter({
                        lat: data.coords.latitude,
                        lng: data.coords.longitude,
                    });

                    this.map.setZoom(20);

                    let bearsMarker = new H.map.Circle(
                        // The central point of the circle
                        {
                            lat: data.coords.latitude,
                            lng: data.coords.longitude,
                        },
                        // The radius of the circle in meters
                        1,
                        {
                            style: {
                                strokeColor: 'rgb(138, 200, 255)', // Color of the perimeter
                                lineWidth: 5,
                                fillColor: 'rgb(86, 176, 255)'  // Color of the circle
                            }
                        }
                    );
                    this.map.addObject(bearsMarker);

                });
            },
        },
        watch: {
            searchText: function (newData, oldData) {
                this.debouncedGetSearchResults();
            }
        },
        created() {
            this.platform = new H.service.Platform({
                "apikey": 'brm9oXsSTBFbLRv1Y-so7W8Q1TNs8dH0EeXvDEHuRcw',
                "app_id": this.appId,
                "app_code": this.appCode
            });

            this.debouncedGetSearchResults = debounce(this.searchPlace, 500)
        },
        mounted() {
            let defaultLayers = this.platform.createDefaultLayers();

            this.map = new H.Map(
                this.$refs.map,
                defaultLayers.vector.normal.map,
                {
                    zoom: 10,
                    center: {lng: this.lng, lat: this.lat},
                    pixelRatio: window.devicePixelRatio || 1,
                }
            );

            window.addEventListener('resize', () => this.map.getViewPort().resize());

            this.behavior = new H.mapevents.Behavior(new H.mapevents.MapEvents(this.map));

            this.ui = H.ui.UI.createDefault(this.map, defaultLayers);

            this.geocoder = this.platform.getGeocodingService();

            this.setCurrentPosition();
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
