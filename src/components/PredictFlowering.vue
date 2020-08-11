<template>
    <div>
        <div style="background-color: white; padding-left: 10px; padding-right: 10px;">        
            <v-flex xs12 pl-2 row class="hidden-md-and-down">
                <v-layout row wrap>
                <p style="color: #27304c; font-size 6px;" class="pl-2 pr-2">
                    Select an analisys to run for the area you are seeing in the map. 
                    Then click the "run" button to display the result.
                </p>
                </v-layout>
            </v-flex>                    

            <v-flex xs12 pl-2 row>
                <v-layout row wrap>
                    <v-flex xs6>
                        <v-menu ref="menuPlantingDate" v-model="menuPlantingDate" :close-on-content-click="false" :nudge-right="40" lazy
                        transition="scale-transition" offset-y full-width min-width="290px">
                        <template v-slot:activator="{ on }">
                            <v-text-field color="#5cb860" :value="pf_model.planting_date" slot="activator" label="Planting date*"
                            :rules="inputDateRules" prepend-icon="event" readonly v-on="on"></v-text-field>
                        </template>
                        <v-date-picker ref="picker" v-model="pf_model.planting_date" scrollable color="#5cb860" first-day-of-week="1"
                            :max="new Date().toISOString().substr(0, 10)" min="1985-01-01" @change="savePlantingDate">
                        </v-date-picker>
                        </v-menu>
                    </v-flex>
                    <v-flex xs6>
                        <v-menu ref="menuStemDate" v-model="menuStemDate" :close-on-content-click="false" :nudge-right="40" lazy
                        transition="scale-transition" offset-y full-width min-width="290px">
                        <template v-slot:activator="{ on }">
                            <v-text-field color="#5cb860" :value="pf_model.stem_elong_date" slot="activator" label="STEM date*"
                            :rules="inputDateRules" prepend-icon="event" readonly v-on="on"></v-text-field>
                        </template>
                        <v-date-picker ref="picker" v-model="pf_model.stem_elong_date" scrollable color="#5cb860" first-day-of-week="1"
                            :max="new Date().toISOString().substr(0, 10)" min="1985-01-01" @change="saveStemDate">
                        </v-date-picker>
                        </v-menu>
                    </v-flex>    

                    <v-flex xs6>
                        <v-text-field dense color="#77b942" type="number" v-model="pf_model.T_base" :value="pf_model.T_base"
                        label="Temp base *" :rules="inputNumRules" required></v-text-field>
                    </v-flex>
                    <v-flex xs6>
                        <v-text-field dense color="#77b942" type="number" v-model="pf_model.T_max" :value="pf_model.T_max"
                        label="Temp max *" :rules="inputNumRules" required></v-text-field>
                    </v-flex>

                    <v-flex sm9 xs6 md6 lg9 xlg6 pa-1>
                        <v-combobox style="margin-top: 0px; padding-top: 0px"
                        v-model="pf_model.crop_name"
                        :items="cropNameArr"
                        item-text="name"
                        item-value="value"
                        label="Select crop name"
                        color="green"
                        ></v-combobox>
                    </v-flex>

                    <v-flex sm9 xs6 md6 lg9 xlg6 pa-1>
                        <v-combobox style="margin-top: 0px; padding-top: 0px"
                        v-model="pf_model.w_domain"
                        :items="wDomainArr"
                        item-text="name"
                        item-value="value"
                        label="Select domain"
                        color="green"
                        ></v-combobox>
                    </v-flex>

                </v-layout>
            </v-flex>
                        
            <v-flex xs12 sm12 md12 lg12 class="text-xs-right" style="padding: 0px; margin-bottom: 5px;">
                <v-btn small round color="#27304c" :loading="isLoading" dark @click="pfService()" title="Run service" >
                RUN
                </v-btn>
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

                </v-layout >  

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
export default {
    name: "PredictFlowering",    
    data: () => ({
        pf_model:{
            planting_date: '2015-04-14',
            stem_elong_date: '2015-05-10',
            crop_name: 'Maize',
            T_base: 10,
            T_max: 30,
            w_domain: 'NEMSGLOBAL',
        },
        cropNameArr: ['Maize', 'Sunflower', 'Summer_wheat'],    
        wDomainArr: ['NEMSGLOBAL', 'NEMS4'],
        isLoading: false,
        pfDialog: false,
        inputNumRules: [            
            v => (v && /^\d+(\.\d{1,20})?$/.test(v)) || ''
        ],        
        inputDateRules: [
            v => !!v || ''
        ],
        menuPlantingDate: false,
        menuStemDate: false,
        diagramURL: {}
    }),
    watch: {
        menuPlantingDate (val) {
            val && setTimeout(() => (this.$refs.picker.activePicker = 'YEAR'))
        },
        menuStemDate (val) {
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
            var coords4326 = this.$store.state.map.getView().getCenter()

            var url = 'https://growth-stages.test.euxdat.eu/backend/predict-growth-stages/';

            var headers = {         
            'Accept': 'application/json',
            'Content-Type': 'application/json'              
            };

            var payload = {
                "longitude": coords4326[0],
                "latitude": coords4326[1],
                "planting_date": this.pf_model.planting_date,
                "stem_elong_date": this.pf_model.stem_elong_date,
                "crop_name": this.pf_model.crop_name,
                "T_base": parseInt(this.pf_model.T_base),
                "T_max": parseInt(this.pf_model.T_max),
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
    },
    created(){
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


