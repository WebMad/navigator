<template>
    <div id="mapContainer">
        <div ref="map" v-bind:style="{ width: width, height: height }"></div>
    </div>
</template>

<script>

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
        created() {
            this.platform = new H.service.Platform({
                "app_id": this.appId,
                "app_code": this.appCode
            });
        },
        mounted() {
            this.map = new H.Map(
                this.$refs.map,
                this.platform.createDefaultLayers().normal.map,
                {
                    zoom: 10,
                    center: { lng: this.lng, lat: this.lat }
                }
            );
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
