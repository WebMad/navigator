<template>
    <div id="mapContainer">
        <div ref="map" v-bind:style="{ width: width, height: height }"></div>
    </div>
</template>

<script>

    /* eslint-disable */

    import currentPos from '../assets/currentPos.png'

    export default {
        name: "Map",
        data() {
            return {
                map: {},
                platform: {}
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
                defaultLayers.vector.normal.map,
                {
                    zoom: 10,
                    center: {lng: this.lng, lat: this.lat},
                    pixelRatio: window.devicePixelRatio || 1,
                }
            );

            window.addEventListener('resize', () => this.map.getViewPort().resize());

            let behavior = new H.mapevents.Behavior(new H.mapevents.MapEvents(this.map));

            let ui = H.ui.UI.createDefault(this.map, defaultLayers);

            this.setCurrentPosition();
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    h3 {
        margin: 40px 0 0;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #42b983;
    }
</style>
