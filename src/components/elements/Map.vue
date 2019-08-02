<template>
    <div id="mapContainer">
        <div class="controls">
            <div class="route">
                <div class="form-group">
                    <div class="additional-buttons">
                        <span>Текущая точка</span>
                        <span @click="setRoute(true, $refs.fromPlace)">Мое местоположение</span>
                    </div>
                    <div class="search-group">
                        <span>Откуда: </span><input type="text" ref="fromPlace">
                    </div>
                </div>
                <div class="form-group">
                    <span>Текущая точка</span>
                    <div class="search-group">
                        <span>Куда: </span><input type="text" ref="toPlace">
                    </div>
                </div>
                <div class="form-group">
                    <input type="button" value="проложить">
                </div>
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
                behavior: {},
                ui: {},
                geocoder: {},
                markers: Object,
                routeObjects: Object,
                currentPosition: Object,
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
            setRoute(type, field) {
                if(type) {

                }
                else {
                    let reverseGeocodingParameters = {
                        prox: this.currentPosition.lat + ',' + this.currentPosition.lng + '',
                        mode: 'retrieveAddresses',
                        maxresults: 1
                    };
                    this.geocoder.reverseGeocode(reverseGeocodingParameters, (data) => {
                    });
                }
            },

            setPlace(place) {

                this.markers.removeAll();

                this.markers.addObject(new H.map.Marker({
                    lat: place.Location.DisplayPosition.Latitude,
                    lng: place.Location.DisplayPosition.Longitude,
                }));

                this.map.addObject(this.markers);

                this.map.setCenter({
                    lat: place.Location.DisplayPosition.Latitude,
                    lng: place.Location.DisplayPosition.Longitude,
                });

                this.$refs.placeInfo.innerHTML = 'Отмечено: ' + place.Location.Address.Label;

            },
            setPeopleOnMap () {
                navigator.geolocation.getCurrentPosition((data) => {
                    this.currentPosition = {
                        'lat': data.coords.latitude,
                        'lng': data.coords.longitude,
                    };
                    let bearsMarker = new H.map.Circle(
                        // The central point of the circle
                        this.currentPosition,
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
                    this.map.setCenter(this.currentPosition);
                });
            },
            showPeopleOnMap () {
                this.setPeopleOnMap();
                this.map.setZoom(20);
            }
        },
        watch: {
            currentPosition: () => {
                //console.log(this.currentPosition)
            }
        },
        created() {
            this.platform = new H.service.Platform({
                "apikey": 'brm9oXsSTBFbLRv1Y-so7W8Q1TNs8dH0EeXvDEHuRcw',
                "app_id": this.appId,
                "app_code": this.appCode
            });

        },
        mounted() {
            let defaultLayers = this.platform.createDefaultLayers();

            this.map = new H.Map(
                this.$refs.map,
                defaultLayers.raster.normal.map,
                {
                    zoom: 10,
                    center: {lat:52, lng:5},
                    pixelRatio: window.devicePixelRatio || 1,
                }
            );

            this.setPeopleOnMap();

            //console.log(this.currentPosition);

            window.addEventListener('resize', () => this.map.getViewPort().resize());

            this.behavior = new H.mapevents.Behavior(new H.mapevents.MapEvents(this.map));

            this.ui = H.ui.UI.createDefault(this.map, defaultLayers);

            this.geocoder = this.platform.getGeocodingService();
            //
            this.markers = new H.map.Group();
            //
            this.$on('setPlace', this.setPlace)

            this.showPeopleOnMap();
            //
            //this.map.setCenter(this.currentPosition);
            //
            // this.map.setZoom(20);


            /*let bearsMarker = new H.map.Circle(
                // The central point of the circle
                this.currentPosition,
                // The radius of the circle in meters
                1,
                {
                    style: {
                        strokeColor: 'rgb(138, 200, 255)', // Color of the perimeter
                        lineWidth: 5,
                        fillColor: 'rgb(86, 176, 255)'  // Color of the circle
                    }
                }
            );*/
            // this.map.addObject(bearsMarker);

            //this.setCurrentPosition();
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .controls {
        display: flex;
        justify-content: space-between;
    }

    .route {
        display: flex;
        justify-content: space-between;
        flex-direction: column;
        width: 300px;
    }

    .route .form-group {
        display: flex;
        flex-direction: column;
    }

    .additional-buttons {
        display: flex;
        justify-content: space-between;
    }

    .search-group {
        display: flex;
        justify-content: space-between;
    }

    .search-group input {
        width: 200px;
    }

</style>
