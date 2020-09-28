<template>

    <div>
        <v-flex xs12 pl-2 row class="hidden-md-and-down">
            <v-layout row wrap>
            <p style="color: #27304c; font-size 6px;" class="pl-2 pr-2">
                Select an analisys to run for the area you are seeing in the map.
                Then click the "run" button to display the result.
                <a @click="infoDialog = true">More info.</a>
            </p>
            </v-layout>
        </v-flex>

        <v-flex xs12 pl-2 row>
            <v-layout row wrap class="pl-2 pr-2">

                <v-flex xs8 sm8 md6 lg8 xlg8 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="text" v-model="location_name" :value="location_name"
                    label="Diagram title"></v-text-field>
                </v-flex>

                <v-flex sm9 xs6 md6 lg4 xlg4 class="pl-3 pr-3">
                    <v-combobox
                    v-model="formatSelected"
                    :items="formatArr"
                    item-text="name"
                    item-value="value"
                    label="Select Diagram format"
                    color="green"
                    ></v-combobox>
                </v-flex>

                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="years_clima_start" :value="years_clima_start"
                    label="First year of past comparison period" title="First year of past comparison period, from 1985 onwards." :rules="inputNumRules"></v-text-field>
                </v-flex>

                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="years_clima_end" :value="years_clima_end"
                    label="Last year of past comparison period" title="Last year of past comparison period." :rules="inputNumRules"></v-text-field>
                </v-flex>

                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="years_actual_start" :value="years_actual_start"
                    label="First year of recent comparison" title="First year of recent comparison period, from 1985 onwards." :rules="inputNumRules"></v-text-field>
                </v-flex>
                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="years_actual_end" :value="years_actual_end"
                    label="years_actual_end" title="Last year of recent comparison period." :rules="inputNumRules"></v-text-field>
                </v-flex>

                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="month_start" :value="month_start"
                    label="Start month" :rules="inputNumRules"></v-text-field>
                </v-flex>
                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="month_end" :value="month_end"
                    label="End month" :rules="inputNumRules"></v-text-field>
                </v-flex>
                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="t_base" :value="t_base"
                    label="Base temp" :rules="inputNumRules"></v-text-field>
                </v-flex>
                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="t_max" :value="t_max"
                    label="Max temp" :rules="inputNumRules"></v-text-field>
                </v-flex>
                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="drought_threshold" :value="drought_threshold"
                    label="Drought threshold" :rules="inputNumRules"></v-text-field>
                </v-flex>
                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="drought_duration" :value="drought_duration"
                    label="Drought duration" :rules="inputNumRules"></v-text-field>
                </v-flex>
                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="frost_threshold" :value="frost_threshold"
                    label="Frost threshold" :rules="inputNumRules"></v-text-field>
                </v-flex>
                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="frost_duration" :value="frost_duration"
                    label="Frost duration" :rules="inputNumRules"></v-text-field>
                </v-flex>
                <v-flex xs3 class="pl-3 pr-3">
                    <v-text-field dense color="#77b942" type="number" v-model="soil_water_capacity" :value="soil_water_capacity"
                    label="Soil water capacity" :rules="inputNumRules"></v-text-field>
                </v-flex>

            </v-layout>
        </v-flex>

        <v-flex xs12 sm12 md12 lg12 class="text-xs-right" style="padding: 0px; margin-bottom: 5px;">
            <v-btn small round color="#27304c" :loading="isLoading" dark @click="runService()" title="Run service" >
                RUN
            </v-btn>
        </v-flex>

        <!------------ Info dialog ------------>
        <v-dialog v-model="infoDialog" max-width="800">
            <v-card>
                <v-card-title class="headline">
                    <img style="width: 155px;" src="../assets/logo_titulo.png" alt="">
                </v-card-title>
                <v-divider></v-divider>
                <v-card-text>
                    <v-flex xs12>
                        <v-card flat>
                            <v-card-text>
                                <div style="margin-bottom: 15px">
                                    <img style="width: 160px; position: absolute; opacity: 0.2; bottom: 20px; right: 5px;" src="../assets/logo2.png" alt="">
                                    <span class="title" style="color: #37aa48; font-size 12px;">Crop climate risk Comparison</span>
                                </div>
                                <p>The Crop Risk Monitoring diagram shows a weather variable behaviour during the current growing season of a selected crop. </p>
                                <p>The colored background represents the risk of occurrence of a weather event (drought, frost) in the selected location, for the specific crop, during its growing season.
                                The risk is calculated as the product of likelihood of the event (based on historical statistics for the selected time period or on the ratio between the current value and the maximum limit of the weather variable) and the impact on the crop (growing phase specific, based on empirical/literature assessment).
                                Crop growing phases are based on cumulated growing degree days (GDD).</p>
                                <p>The current season shows the weather variable behaviour from the beginning of the growing season up to today, plus 7 days forecast. The forecast is enveloped between the last 10 years maxima, minima and averages occurred at the correspondent GDD.
                                The current season is also compared to climate averages of two periods in the past.</p>
                            </v-card-text>
                        </v-card>
                    </v-flex>
                </v-card-text>
                <v-divider></v-divider>
                <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="green darken-1" dark small @click="infoDialog = false">
                    Got it!
                </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <!------------ /Info dialog ------------>

        <!------------ Output dialog ------------>
        <v-dialog v-model="outputDialog" max-width="800">
            <v-card v-if = !isLoading>
                <v-card-title class="headline">
                    <img style="width: 130px;" src="../assets/logo_titulo.png" alt="">
                    <v-spacer></v-spacer>
                    <v-btn icon title="Download graphic" @click="downloadGraph()">
                    <v-icon color="#27304c">archive</v-icon>
                    </v-btn>
                </v-card-title>

                <v-divider></v-divider>
                <v-card-text>
                    <v-flex xs12  class="pa-3" v-if="formatSelected='png'">
                    <v-img :src="grahpURL" alt=""></v-img>
                    </v-flex>
                </v-card-text>
            </v-card>
            <v-card v-if = isLoading>
                <v-card-text style="text-align: center;">
                    <img style="width: 120px;" src="../assets/logo_titulo.png" alt=""><br>
                    <v-progress-circular :size="60" :width="7" color="green" indeterminate style="margin-top: 15px;"></v-progress-circular>
                    <v-flex xs12 style="color: #37aa48; font-size 12px; margin-bottom: 10px; margin-top: 10px;">
                    Processing...
                    </v-flex>
                </v-card-text>
            </v-card>
        </v-dialog>
        <!------------ /Output dialog ------------>

    </div>

</template>

<script>

import moment from 'moment';
export default {
    name: "CropWeather",
    data: () => ({
      isValid: false,
      inputNumRules: [
          v => (v && /^\d+(\.\d{1,20})?$/.test(v)) || ''
      ],
      API_key: "8vh83gfhu34g",
      years_clima_start: 1985,
      years_clima_end: 1995,
      years_actual_start: 'current year-10',
      years_actual_end: 2020,
      month_start: "",
      month_end: "",
      t_base: "",
      t_max: "",
      formatSelected: 'png',
      formatArr: ['png', 'json', 'highchartsHtml'],
      soil_water_capacity: "",
      drought_threshold: "",
      drought_duration: 3,
      frost_threshold: 0,
      frost_duration: 3,
      location_name: '',
      isLoading: false,
      outputDialog: false,
      infoDialog: false,
      grahpURL: ""
  }),
  methods: {

  },
  created(){
  },
  filters: {
      truncate: function(value) {
          if(value != undefined){
              value = value.toString().substring(0, 8);
          }
          return value
      }
  }

};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
