<template>

    <v-card-text>
        <v-flex xs12>
            <v-card flat>
                <v-card-text>

                <div style="margin-bottom: 15px">
                    <img style="width: 160px; position: absolute; opacity: 0.2; bottom: 70px; right: 5px;" src="../assets/logo2.png" alt="">
                    <span class="title" style="color: #1e2f4d;">Information support for field use recommendations</span>
                </div>                    
                <p>
                    Today, climate change is affecting most areas in Europe. The magnitude of the changes
                    is not yet fully known, however, the variability over the past 10 to 30 years is rather precisely understood.
                    This system will allow an assessment of trends, frequency distribution and extremes
                    for the major weather-related variables (temperature, precipitation, evapotranspiration, soil moisture)
                    for agricultural zones. It can link to the other system.
                    The differences to existing climate databases will be the following:
                </p>
                <p>
                    <ul>
                    <li>Analysis will be focussed on crop land (filter).</li>
                    <li>Analysis will be focussed on cropping seasons (filter applicable for
                        different crops, e.g. summer crops, winter crops, perennial crops).</li>
                    <li>Analysis will allow users to upload own records (e.g. temperature
                        measurements) to benchmark regional model data.</li>
                    <li>The service can (but does not have to) link to existing climate
                        change models which quantify future changes.</li>
                    </ul>
                </p>
                <p style="margin-bottom: 15px;">
                    This service will create a unique ability to assess potential impact of climate change on local production.
                </p>
                <p>
                    <span class="title" style="color: #37aa48; font-size 12px;">
                    Specifications
                    </span>
                </p>
                <p>
                    These extremes change over time, and the mapping exercise would consist of defining long-term indices for
                    change (frequency of frost or heat, heavy rainfall or drought, storms) and plotting them on maps.
                    Local detail is not of such concern in these maps, since large-scale frequency changes occur over longer periods
                    of time and larger areas and are not subject to local micro-climate.
                    Details must still be worked out, because this pattern change so far only uses weather input and does not
                    yet connect this input with other data. Options to enhance the service:
                </p>
                <p style="margin-bottom: 5px;">
                    <ul>
                    <li>Upload weather station data (for 1-2 years) to validate sources for long-term data.</li>
                    <li>Downscale simulations using topography/slope.</li>
                    <li>Integrate climate risk with soil data, e.g. impact of drought on soils with san, silt, clay, or peat content.</li>
                    <li>Insert crop growth thresholds to show impact of climate variations on different crops.</li>
                    <li>Simulate effect of climate on different crops (link to scenario 2).</li>
                    </ul>
                </p>
                </v-card-text>

            </v-card>
        </v-flex>
    </v-card-text>

</template>

<script>
export default {
  name: "StartDialog",
  props: {}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>