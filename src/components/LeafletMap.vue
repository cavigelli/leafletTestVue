<script>
    import "leaflet/dist/leaflet.css"
    import L from "leaflet"
    import { austriaFederalStates } from "../austriaFederalStates"
    import { electionResults2019 } from "../electionResults2019"

    export default {
        name: "LeafletMap",
        data() {
            return {
                center: [47.697,13.3465],
                zoomFactor: 7,
                propertiesToShow: [
                    "elligible",
                    "valid",
                    "invalid" 
                ],
                politicalParties: [
                    "oevp",
                    "spoe",
                    "fpoe",
                    "gruene"
                ],
                austriaFederalStates: austriaFederalStates,
                electionResults2019: electionResults2019,
                selectedState: 'Burgenland',
                colors : [
                    "#f7fcfd",
                    "#e5f5f9",
                    "#ccece6",
                    "#99d8c9",
                    "#66c2a4",
                    "#41ae76",
                    "#238b45",
                    "#006d2c",
                    "#00441b",
                    ]
            }
        },
        methods: {
            setupMap: function() {
                const map = L.map("map").setView(this.center,this.zoomFactor)
                L.geoJSON(austriaFederalStates, {
                    onEachFeature: (feature,layer) => {
                        let string = "test"
                        let propertiesOfFeature = this.electionResults2019.data.filter(state => {
                            return state.name === feature.properties.name
                        })
                        console.log(propertiesOfFeature[0])
                        //let data = this.electionResults2019.data.filter(state )
                        for (const party of this.politicalParties) {
                            string += party + ": " + propertiesOfFeature[0][party] + "<br>" 
                        }
                        layer.bindPopup(
                            string
                            )
                    }
                }).addTo(map)
            },
        },
        computed: {
            states() {
                return this.austriaFederalStates.features
            },
            resultsOfselectedState() {
                const selectedStateData = this.electionResults2019.data.filter(state => {
                    return state.name === this.selectedState
                })
                const result = {}
                for (const party of this.politicalParties) {
                    console.log(party)
                    Object.assign(result, {[party]: selectedStateData[0][party]})
                }
                return result
            }
        },
        mounted() {
            this.setupMap()
        }
    }
</script>

<template>
    <div id="map-container">
        <div id="map">
            Map
        </div>
        <div id="controls">
            <ul>
                <li v-for="state in states">
                    <button>
                    {{ state.properties.name }}
                    </button>
                </li>
            </ul>
            <div>
                Results for {{ selectedState }}:
                <div v-for="votes, party in resultsOfselectedState">
                    {{ party }} : {{ votes }}
                </div>
            </div>
        </div>
    </div>
</template>


<style>
#map {
    width: 70%;
    height: 50vh;
}

#map-container {
    display: flex;
}
</style>