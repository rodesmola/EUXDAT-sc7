<template>
  <div>
    <div style="background-color: white; padding-left: 10px; padding-right: 10px;">        
      <v-flex xs12 pl-2 row class="hidden-md-and-down">
        <v-layout row wrap>
          <p style="color: #27304c; font-size 6px;" class="pl-2 pr-2">
            The service will use the coordinates showed in the rigth side of the map, can be input by the user.
          </p>
        </v-layout>
      </v-flex>                    

      <v-flex xs12 pl-2 row>
        <v-form ref="pf_modelForm" v-model="pf_modelValid">
          <v-layout row wrap>

            <v-flex xs6 class="pl-3 pr-3">
              <v-menu ref="menuPlantingDate" v-model="menuPlantingDate" :close-on-content-click="false" :nudge-right="40" lazy
                transition="scale-transition" offset-y full-width min-width="290px">
              <template v-slot:activator="{ on }">
                <v-text-field color="#5cb860" :value="pf_model.planting_date" slot="activator" label="Planting date*"
                  prepend-icon="event" readonly v-on="on" clearable></v-text-field>
              </template>
              <v-date-picker ref="picker" v-model="pf_model.planting_date" scrollable color="#5cb860" first-day-of-week="1"
                :max="new Date().toISOString().substr(0, 10)" min="1985-01-01" @change="savePlantingDate" >
              </v-date-picker>
              </v-menu>
            </v-flex>

            <v-flex xs6 class="pl-3 pr-3">
              <v-menu ref="menuStemDate" v-model="menuStemDate" :close-on-content-click="false" :nudge-right="40" lazy
                transition="scale-transition" offset-y full-width min-width="290px">
              <template v-slot:activator="{ on }">
                <v-text-field color="#5cb860" :value="pf_model.stem_elong_date" slot="activator" label="STEM date*"
                prepend-icon="event" readonly v-on="on" clearable></v-text-field>
              </template>
              <v-date-picker ref="picker" v-model="pf_model.stem_elong_date" scrollable color="#5cb860" first-day-of-week="1"
                :max="new Date().toISOString().substr(0, 10)" min="1985-01-01" @change="saveStemDate">
              </v-date-picker>
              </v-menu>
            </v-flex>    

            <v-flex xs6 class="pl-3 pr-3">
              <v-menu ref="menuLeafingDate" v-model="menuLeafingDate" :close-on-content-click="false" :nudge-right="40" lazy
                transition="scale-transition" offset-y full-width min-width="290px">
              <template v-slot:activator="{ on }">
                <v-text-field color="#5cb860" :value="pf_model.leafing_date" slot="activator" label="Leafing date*"
                prepend-icon="event" readonly v-on="on" clearable></v-text-field>
              </template>
              <v-date-picker ref="picker" v-model="pf_model.leafing_date" scrollable color="#5cb860" first-day-of-week="1"
                :max="new Date().toISOString().substr(0, 10)" min="1985-01-01" @change="saveLeafingDate">
              </v-date-picker>
              </v-menu>
            </v-flex>    

            <v-flex xs3 class="pl-3 pr-3">
              <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="pf_model.t_base" :value="pf_model.t_base"
                label="Temp base" title="Crop base temperature (°C)."
                @input="$v.pf_model.t_base.$touch()" @blur="$v.pf_model.t_base.$touch()"
                :error-messages="t_baseErrors">
              </v-text-field> 
            </v-flex>
  
            <v-flex xs3 class="pl-3 pr-3">
              <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="pf_model.t_max" :value="pf_model.t_max"
                label="Temp max" title="Crop max temperature (°C)."
                @input="$v.pf_model.t_max.$touch()" @blur="$v.pf_model.t_max.$touch()"
                :error-messages="t_maxErrors">
              </v-text-field> 
            </v-flex>

            <v-flex sm9 xs6 md6 lg6 xlg6 class="pl-3 pr-3">
              <v-combobox hide-no-data hide-selected dense
              v-model="pf_model.crop_name"
              :items="cropNameArr"
              item-text="name"
              item-value="value"
              label="Select crop name"
              color="green"
              ></v-combobox>
            </v-flex>

            <v-flex sm9 xs6 md6 lg6 xlg6 class="pl-3 pr-3">
              <v-combobox hide-no-data hide-selected dense
              v-model="pf_model.w_domain"
              :items="wDomainArr"
              item-text="name"
              item-value="value"
              label="Select domain"
              color="green"
              ></v-combobox>
            </v-flex>

            <v-flex xs12 sm12 md12 lg12 class="text-xs-right" style="padding: 0px; margin-bottom: 5px;">
              <v-btn small round color="#27304c" :disabled="!pf_modelValid" :loading="isLoading" dark @click="pfService()" title="Run service" >
              RUN
              </v-btn>
            </v-flex>

          </v-layout>
        </v-form> 
      </v-flex>
                      
    </div>

    <!------------ MeteoBlue PF dialog ------------>
    <v-dialog v-model="pfDialog" max-width="900">
      <v-card>
        <v-card-title class="headline">
          <img style="width: 130px;" src="../assets/logo_titulo.png" alt="">
        </v-card-title>

        <v-divider></v-divider>

        <v-card-text>

          <v-layout row wrap style="text-align: center;" class="mb-2">
            <v-flex xs6 class="pa-1">
              <span class="titleDate" style="color: #37aa48; font-size 12px;">
                Predicted flowering date: 
              </span>                  
              <span class="titleDate" style="color: balck">
                {{diagramURL.predicted_flowering_date}}
              </span>                  
            </v-flex>
            <v-flex xs6 class="pa-1">
              <span class="titleDate" style="color: #37aa48; font-size 12px;">
                Predicted flowering date (forecast): 
              </span>                  
              <span class="titleDate" style="color: balck">
                {{diagramURL.predicted_flowering_date_with_forecast}}
              </span>                  
            </v-flex>
          </v-layout>  

          <v-flex xs12 class="mb-1">
            <v-card hover>
              <v-card-title primary-title>
                <h3 class="headline title" style="color: #37aa48; font-size 14px; ">Cumulated degree days horuly</h3>
                <v-spacer></v-spacer>
                <v-btn icon title="Download graphic" @click="downloadGraph(diagramURL.CDD_image_url)">
                  <v-icon color="#27304c">archive</v-icon>
                </v-btn>         
              </v-card-title>
              <v-img v-if="diagramURL.CDD_image_url" :src="'https://growth-stages.test.euxdat.eu/backend/predict-growth-stages/images/' + 
                diagramURL.CDD_image_url" alt=""></v-img>
            </v-card>
          </v-flex>

          <v-flex xs12 class="mb-1">
            <v-card hover>
              <v-card-title primary-title>
                  <h3 class="headline title" style="color: #37aa48; font-size 14px; ">Cumulated precipitation (mm)</h3>
                <v-spacer></v-spacer>
                <v-btn icon title="Download graphic" @click="downloadGraph(diagramURL.CPREC_image_url)">
                  <v-icon color="#27304c">archive</v-icon>
                </v-btn>         
              </v-card-title>
              <v-img v-if="diagramURL.CPREC_image_url" :src="'https://growth-stages.test.euxdat.eu/backend/predict-growth-stages/images/' + 
                diagramURL.CPREC_image_url" alt=""></v-img>
            </v-card>
          </v-flex>

          <v-flex xs12 class="mb-1">
            <v-card hover>
              <v-card-title primary-title>
                <h3 class="headline title" style="color: #37aa48; font-size 14px; ">Cumulated radiation (W/m2)</h3>
                <v-spacer></v-spacer>
                <v-btn icon title="Download graphic" @click="downloadGraph(diagramURL.CRAD_image_url)">
                  <v-icon color="#27304c">archive</v-icon>
                </v-btn>         
              </v-card-title>
              <v-img v-if="diagramURL.CRAD_image_url" :src="'https://growth-stages.test.euxdat.eu/backend/predict-growth-stages/images/' + 
                diagramURL.CRAD_image_url" alt=""></v-img>
            </v-card>
          </v-flex>

          <v-flex xs12>
            <v-card hover>
              <v-card-title primary-title>
                <h3 class="headline title" style="color: #37aa48; font-size 14px; ">Average soil moisture (fraction)</h3>
                <v-spacer></v-spacer>
                <v-btn icon title="Download graphic" @click="downloadGraph(diagramURL.CSOILM_image_url)">
                  <v-icon color="#27304c">archive</v-icon>
                </v-btn>         
              </v-card-title>
              <v-img v-if="diagramURL.CSOILM_image_url" :src="'https://growth-stages.test.euxdat.eu/backend/predict-growth-stages/images/' + 
                diagramURL.CSOILM_image_url" alt=""></v-img>
            </v-card>
          </v-flex>

        </v-card-text>
      </v-card>

    </v-dialog>
    <!------------ /MeteoBlue PF dialog ------------>
  </div>

</template>

<script>

import moment from 'moment';
import { decimal, required } from 'vuelidate/lib/validators'
export default {
    name: "PredictFlowering",    
    data: () => ({
      pf_model:{
        planting_date: '2015-04-14',
        stem_elong_date: '2015-05-10',
        leafing_date: '2015-05-10',
        crop_name: 'Maize',
        t_base: 10,
        t_max: 30,
        w_domain: 'NEMSGLOBAL',
      },
      cropNameArr: ['Maize', 'Sunflower', 'Summer_wheat'],    
      wDomainArr: ['NEMSGLOBAL', 'NEMS4'],
      isLoading: false,
      pfDialog: false,
      menuPlantingDate: false,
      menuStemDate: false,
      menuLeafingDate: false,
      diagramURL: {},
      pf_modelValid: false,
    }),
    watch: {
      menuPlantingDate (val) {
        val && setTimeout(() => (this.$refs.picker.activePicker = 'YEAR'))
      },
      menuStemDate (val) {
        val && setTimeout(() => (this.$refs.picker.activePicker = 'YEAR'))
      },  
      menuLeafingDate (val) {
        val && setTimeout(() => (this.$refs.picker.activePicker = 'YEAR'))
      },         
    },
    methods: {        
      /**
      * Create the url to acces the output rm diagram
      *        
      * @public
      */
      pfService(){
        
        this.isLoading = true;        
        var url = 'https://growth-stages.test.euxdat.eu/backend/predict-growth-stages/';

        var headers = {         
          'Accept': 'application/json',
          'Content-Type': 'application/json'              
        };

        var payload = {
          "longitude": parseInt(this.$store.state.mapCoords.long),
          "latitude": parseInt(this.$store.state.mapCoords.lat),
          "planting_date": this.pf_model.planting_date,
          "stem_elong_date": this.pf_model.stem_elong_date,
          "leafing_date": this.pf_model.leafing_date,
          "crop_name": this.pf_model.crop_name,
          "t_base": parseInt(this.pf_model.t_base),
          "t_max": parseInt(this.pf_model.t_max),
          "aggr": this.pf_model.w_domain
        }

        this.$http.post(url, payload, headers).then(response => {               
          this.diagramURL = response.body;
          this.pfDialog = true;
          this.isLoading = false;
          this.$eventBus.$emit('show-alert', "success", response.statusText);
        }, response => {     
          this.isLoading = false;   
          this.$eventBus.$emit('show-alert', "error", response.statusText);
        });

      },//pfService
      /**
      * Open the image in other browser tab to force to be download
      *
      * @public
      */
      downloadGraph(url){            
        window.open('https://growth-stages.test.euxdat.eu/backend/predict-growth-stages/images/' + url)                  
      },        
      savePlantingDate (date) {
        this.$refs.menuPlantingDate.save(date)
      },
      saveStemDate (date) {
        this.$refs.menuStemDate.save(date)
      },
      saveLeafingDate (date) {
        this.$refs.menuLeafingDate.save(date)
      },

    },
    validations: {
      pf_model:{
        t_base: {required, decimal},
        t_max: {required, decimal}, 
      }    
    },
    computed: {
      t_baseErrors () {
        const errors = []
        if (!this.$v.pf_model.t_base.$dirty) return errors
        !this.$v.pf_model.t_base.decimal && errors.push('Insert a number')
        !this.$v.pf_model.t_base.required && errors.push('Required field.')
        return errors
      },        
      t_maxErrors () {
        const errors = []
        if (!this.$v.pf_model.t_max.$dirty) return errors
        !this.$v.pf_model.t_max.decimal && errors.push('Insert a number')
        !this.$v.pf_model.t_max.required && errors.push('Required field.')
        return errors
      },           
    },
    filters: {
      formatDate: function(value) {
        if (value) {
          return moment(String(value)).format('MM/DD/YYYY hh:mm')
        }
      }
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>


