<template>

    <div>
        <v-flex xs12 pl-2 row class="hidden-md-and-down">
            <v-layout row wrap>
            <p style="color: #27304c; font-size 6px;" class="pl-2 pr-2">
                The service will use the coordinates showed in the rigth side of the map, can be input by the user.
                <a @click="infoDialog = true">More info.</a>
            </p>
            </v-layout>
        </v-flex>

        <v-flex xs12 pl-2 row>
            <v-form ref="cropWeatherForm" v-model="cropWeatherValid">
                <v-layout row wrap class="pl-2 pr-2">

                    <v-flex xs8 sm8 md6 lg8 xlg8 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="cropWeather.location_name" :value="cropWeather.location_name"
                        label="Diagram title"></v-text-field>
                    </v-flex>

                    <v-flex sm9 xs6 md6 lg4 xlg4 class="pl-3 pr-3">
                        <v-combobox hide-no-data hide-selected dense
                        v-model="cropWeather.selectedFormat"
                        :items="cropWeather.formatArr"
                        item-text="name"
                        item-value="value"
                        label="Select Diagram format"
                        color="green"
                        ></v-combobox>
                    </v-flex>

                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="number" v-model="cropWeather.years_clima_start" 
                            :value="cropWeather.years_clima_start" label="First year comp." title="First year of past comparison period, from 1985 onwards." 
                            @input="$v.cropWeather.years_clima_start.$touch()" @blur="$v.cropWeather.years_clima_start.$touch()"
                            :error-messages="years_clima_startErrors">                        
                        </v-text-field>
                    </v-flex>  
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="number" v-model="cropWeather.years_clima_end" 
                            :value="cropWeather.years_clima_end" label="Last year comp." title="Last year of past comparison period." 
                            @input="$v.cropWeather.years_clima_end.$touch()" @blur="$v.cropWeather.years_clima_end.$touch()"
                            :error-messages="years_clima_endErrors">                        
                        </v-text-field>
                    </v-flex>   
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="number" v-model="cropWeather.years_actual_start" 
                            :value="cropWeather.years_actual_start" label="First year of recent comp." title="First year of recent comparison period, from 1985 onwards." 
                            @input="$v.cropWeather.years_actual_start.$touch()" @blur="$v.cropWeather.years_actual_start.$touch()"
                            :error-messages="years_actual_startErrors">  
                        </v-text-field>
                    </v-flex>            
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="number" v-model="cropWeather.years_actual_end" 
                            :value="cropWeather.years_actual_end" label="Last year of recent comp." title="Last year of recent comparison period." 
                            @input="$v.cropWeather.years_actual_end.$touch()" @blur="$v.cropWeather.years_actual_end.$touch()"
                            :error-messages="years_actual_endErrors">
                        </v-text-field>
                    </v-flex>    
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field  dense color="#77b942" hide-no-data hide-selected  type="text" v-model="cropWeather.month_start" 
                            :value="cropWeather.month_start" label="Initial month" title="Initial month of growing season" 
                            @input="$v.cropWeather.month_start.$touch()" @blur="$v.cropWeather.month_start.$touch()"
                            :error-messages="month_startErrors">
                        </v-text-field>
                    </v-flex>
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field  dense color="#77b942" hide-no-data hide-selected  type="text" v-model="cropWeather.month_end" 
                            :value="cropWeather.month_end" label="Final month" title="Final month of growing season" 
                            @input="$v.cropWeather.month_end.$touch()" @blur="$v.cropWeather.month_end.$touch()"
                            :error-messages="month_endErrors">
                        </v-text-field>
                    </v-flex>
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="cropWeather.t_base" 
                            :value="cropWeather.t_base" label="Base temp" title="Crop base temperature (°C)."
                            @input="$v.cropWeather.t_base.$touch()" @blur="$v.cropWeather.t_base.$touch()"
                            :error-messages="t_baseErrors">                        
                        </v-text-field>
                    </v-flex>
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="cropWeather.t_max" 
                            :value="cropWeather.t_max" label="Max temp" title="Crop max temperature (°C)."
                            @input="$v.cropWeather.t_max.$touch()" @blur="$v.cropWeather.t_max.$touch()"
                            :error-messages="t_maxErrors">                        
                        </v-text-field>
                    </v-flex>
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="cropWeather.soil_water_capacity" 
                            :value="cropWeather.soil_water_capacity" label="Soil water capacity" title="Maximum possible soil water content (mm)."
                            @input="$v.cropWeather.soil_water_capacity.$touch()" @blur="$v.cropWeather.soil_water_capacity.$touch()"
                            :error-messages="soil_water_capacityErrors">
                        </v-text-field> 
                    </v-flex>
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="cropWeather.drought_threshold" 
                            :value="cropWeather.drought_threshold" label="Drought threshold" title="Soil water content threshold (mm) below which drought happens."
                            @input="$v.cropWeather.drought_threshold.$touch()" @blur="$v.cropWeather.drought_threshold.$touch()"
                            :error-messages="drought_thresholdErrors">
                        </v-text-field> 
                    </v-flex>
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="cropWeather.drought_duration" 
                            :value="cropWeather.drought_duration" label="Drought duration" title="Duration threshold (hours) above which drought happens."
                            @input="$v.cropWeather.drought_duration.$touch()" @blur="$v.cropWeather.drought_duration.$touch()"
                            :error-messages="drought_durationErrors">
                        </v-text-field> 
                    </v-flex>
                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="cropWeather.frost_threshold" 
                            :value="cropWeather.frost_threshold" label="Frost threshold" title="Temperature threshold (°C) below which frost happens."
                            @input="$v.cropWeather.frost_threshold.$touch()" @blur="$v.cropWeather.frost_threshold.$touch()"
                            :error-messages="frost_thresholdErrors">
                        </v-text-field> 
                    </v-flex>

                    <v-flex xs3 class="pl-3 pr-3">
                        <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="cropWeather.frost_duration" 
                            :value="cropWeather.frost_duration" label="Frost duration" title="Duration threshold (hours) above which frost happens."
                            @input="$v.cropWeather.frost_duration.$touch()" @blur="$v.cropWeather.frost_duration.$touch()"
                            :error-messages="frost_durationErrors">
                        </v-text-field> 
                    </v-flex>

                    <v-flex xs9 sm9 md9 lg9 class="text-xs-right pr-2 mt-3" style="padding: 0px; margin-bottom: 5px;">
                        <v-btn small round color="#27304c" :disabled="!cropWeatherValid" :loading="isLoading" dark @click="runService()" title="Run service" >
                        RUN
                        </v-btn>
                    </v-flex>

                </v-layout>
            </v-form> 
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
                                    <span class="title" style="color: #37aa48; font-size 12px;">Crop weather risk monitoring and prediction</span>
                                </div>
                                <p>The Crop Risk Monitoring diagram shows a weather variable behaviour during the current growing season of a selected crop. </p>
                                <p>The colored background represents the risk of occurrence of a weather event (drought, frost) in the selected location, for the specific crop, during its growing season.
                                The risk is calculated as the product of likelihood of the event (based on historical statistics for the selected time period or on the ratio between the current value and the maximum limit of the weather variable) and the impact on the crop (growing phase specific, based on empirical/literature assessment).
                                Crop growing phases are based on cumulated growing degree days (GDD).</p>
                                <p>The current season shows the weather variable behaviour from the beginning of the growing season up to today, plus 7 days forecast. The forecast is enveloped between the last 10 years maxima, minima and averages occurred at the correspondent GDD.
                                The current season is also compared to climate averages of two periods in the past.</p>
                                <img style="width: 750px;" src="../assets/cropWeatherTable.png" alt="">
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

                    <v-btn icon title="Download diagram" @click="downloadOutput(format)" v-if="format === 'png'">
                        <v-icon color="#27304c">archive</v-icon>
                    </v-btn>
                    <v-flex xs2 v-if="format === 'json'">
                        <v-btn dark small block @click="downloadOutput(format)" style="background-color: #47a34b; color: white;" title="Download JSON">
                            <v-icon class="pa-1 ml-1">save</v-icon> <span v-if="!$vuetify.breakpoint.xs">Download</span>
                        </v-btn>        
                    </v-flex>            
                </v-card-title>

                <v-divider></v-divider>
                <v-card-text>
                    <v-flex xs12  class="pa-3" v-if="format === 'png'">
                        <v-img :src="grahpURL" alt=""></v-img>
                    </v-flex>
                    <v-flex xs12 class="pa-3" v-if="format === 'json'">                       
                        <p>
                            <span class="title" style="color: #37aa48; font-size 12px;">
                                Crop weather risk monitoring and prediction
                            </span>
                        </p>
                           <p>The Crop Risk Monitoring diagram shows a weather variable behaviour during the current growing season of a selected crop. </p>
                            <p>The colored background represents the risk of occurrence of a weather event (drought, frost) in the selected location, for the specific crop, during its growing season.
                            The risk is calculated as the product of likelihood of the event (based on historical statistics for the selected time period or on the ratio between the current value and the maximum limit of the weather variable) and the impact on the crop (growing phase specific, based on empirical/literature assessment).
                            Crop growing phases are based on cumulated growing degree days (GDD).</p>
                            <p>The current season shows the weather variable behaviour from the beginning of the growing season up to today, plus 7 days forecast. The forecast is enveloped between the last 10 years maxima, minima and averages occurred at the correspondent GDD.
                            The current season is also compared to climate averages of two periods in the past.</p>

                        <img style="width: 750px;" src="../assets/cropWeatherTable.png" alt="">
                    </v-flex>   
                    
                    <v-flex xs12  class="pa-3" v-if="format === 'highchartsHtml'"> 
                        <iframe 
                            width="100%"
                            height="600"
                            frameborder="0" style="overflow:hidden;"
                            :src="grahpURL">
                        </iframe> 
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

import { decimal, between, numeric } from 'vuelidate/lib/validators'
export default {
    name: "CropWeather",
    data: () => ({
        //API_key: "8vh83gfhu34g",
        API_key: "1a98a8ef2598-EU-SG-testing",
        isLoading: false,
        outputDialog: false,
        infoDialog: false,
        grahpURL: "",
        cropWeatherValid: false,
        outputjson: {},
        format: "",
        cropWeather: {
            years_clima_start: 1985,
            years_clima_end: 1995,
            years_actual_start: 2010,
            years_actual_end: 2020,
            month_start: 5,
            month_end: 9,
            t_base: 4,
            t_max: 30,
            selectedFormat: 'png',
            formatArr: ['png', 'json', 'highchartsHtml'],
            soil_water_capacity: 50,
            drought_threshold: 5,
            drought_duration: 3,
            frost_threshold: 0,
            frost_duration: 3,
            location_name: '',
        },            
     }),
     methods: {
        runService(){
            this.$v.$touch()
            var url = 'http://pyapi.meteoblue.com/cropWeatherRiskMonitoring/Drought/Winter_Wheat/';    
        
            url = url.concat(this.$store.state.mapCoords.lat, '/', this.$store.state.mapCoords.long, '/', this.API_key, 
            '?format=', this.cropWeather.selectedFormat);

            if(this.cropWeather.years_clima_start){
                url = url.concat('&years_clima_start=', this.cropWeather.years_clima_start)
            }
            if(this.cropWeather.years_clima_end){
                url = url.concat('&years_clima_end=', this.cropWeather.years_clima_end)
            }
            if(this.cropWeather.years_actual_start){
                url = url.concat('&years_actual_start=', this.cropWeather.years_actual_start)
            }
            if(this.cropWeather.years_actual_end){
                url = url.concat('&years_actual_end=', this.cropWeather.years_actual_end)
            }
            if(this.cropWeather.location_name){
                url = url.concat('&location_name=', this.cropWeather.location_name)
            }
            if(this.cropWeather.month_start){
                url = url.concat('&month_start=', this.cropWeather.month_start)
            }           
            if(this.cropWeather.month_end){
                url = url.concat('&month_end=', this.cropWeather.month_end)
            }           
            if(this.cropWeather.soil_water_capacity){
                url = url.concat('&soil_water_capacity=', this.cropWeather.soil_water_capacity)
            }            
            if(this.cropWeather.drought_threshold){
                url = url.concat('&drought_threshold=', this.cropWeather.drought_threshold)
            }                  
            if(this.cropWeather.drought_duration){
                url = url.concat('&drought_duration=', this.cropWeather.drought_duration)
            }             
            if(this.cropWeather.frost_threshold){
                url = url.concat('&frost_threshold=', this.cropWeather.frost_threshold)
            } 
            if(this.cropWeather.frost_duration){
                url = url.concat('&frost_duration=', this.cropWeather.frost_duration)
            } 
      
            this.getOutput(url, this.cropWeather.selectedFormat)

        },
        getOutput(url, format){

            this.outputDialog = true;
            this.grahpURL = url;
            this.format = format;
            var self = this;           
            this.isLoading = true;  

            if(format === 'json'){
                this.$http.get(url).then(response => {                             
                    this.outputjson = response.body;    
                    this.isLoading = false;         
                    self.$eventBus.$emit('show-alert', "success", "JSON retrieved successfully");                           
                }, response => {
                    this.isLoading = false;                    
                    this.$eventBus.$emit('show-alert', "error", response.statusText); 
                });

            }else{
                setTimeout(function(){                                                                    
                    self.$eventBus.$emit('show-alert', "success", "Diagram retrieved successfully");   
                    self.isLoading = false;                  
                }, 4000); 
            }                              
                            
        },
        /**
        * Open the image in other browser tab to force to be download
        *
        * @public
        */
        downloadOutput(format){
            if(format === 'png'){
                window.open(this.grahpURL)  
            }else{
                var data = JSON.stringify(this.outputjson)
                var blob = new Blob([data], {type: 'text/plain'})
                var e = document.createEvent('MouseEvents'),
                a = document.createElement('a');
                a.download = "output.json";
                a.href = window.URL.createObjectURL(blob);
                a.dataset.downloadurl = ['text/json', a.download, a.href].join(':');
                e.initEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
                a.dispatchEvent(e);
            }          
        },             
    },
    validations: {
        cropWeather: {
            month_start: {between: between(0, 12), numeric},
            month_end: {between: between(0, 12), numeric},                   
            years_clima_start: {between: between(1985, 2020), numeric},  
            years_clima_end: {between: between(1985, 2020), numeric},  
            years_actual_start: {between: between(1985, 2020), numeric},
            years_actual_end: {between: between(1985, 2020), numeric}, 
            t_base: {decimal},
            t_max: {decimal},        
            soil_water_capacity: {numeric},
            drought_threshold: {numeric},
            drought_duration: {numeric},
            frost_threshold: {numeric},
            frost_duration: {numeric},
        },    
    },
    computed: {
        month_startErrors () {
            const errors = []
            if (!this.$v.cropWeather.month_start.$dirty) return errors            
            !this.$v.cropWeather.month_start.between && errors.push('Values from 0 to 12')
            !this.$v.cropWeather.month_start.numeric && errors.push('Insert a number')
            return errors
        },
        month_endErrors () {
            const errors = []
            if (!this.$v.cropWeather.month_end.$dirty) return errors
            !this.$v.cropWeather.month_end.between && errors.push('Values from 0 to 12')
            !this.$v.cropWeather.month_end.numeric && errors.push('Insert a number')
            return errors
        },
        years_clima_startErrors () {
            const errors = []
            if (!this.$v.cropWeather.years_clima_start.$dirty) return errors
            !this.$v.cropWeather.years_clima_start.between && errors.push('Values from 1985 to 2020')
            !this.$v.cropWeather.years_clima_start.numeric && errors.push('Insert a number')
            return errors
        },     
        years_clima_endErrors () {
            const errors = []
            if (!this.$v.cropWeather.years_clima_end.$dirty) return errors
            !this.$v.cropWeather.years_clima_end.between && errors.push('Values from 1985 to 2020')
            !this.$v.cropWeather.years_clima_end.numeric && errors.push('Insert a number')
            return errors
        },      
        years_actual_startErrors () {
            const errors = []
            if (!this.$v.cropWeather.years_actual_start.$dirty) return errors
            !this.$v.cropWeather.years_actual_start.between && errors.push('Values from 1985 to 2020')
            !this.$v.cropWeather.years_actual_start.numeric && errors.push('Insert a number')
            return errors
        },    
        years_actual_endErrors () {
            const errors = []
            if (!this.$v.cropWeather.years_actual_end.$dirty) return errors
            !this.$v.cropWeather.years_actual_end.between && errors.push('Values from 1985 to 2020')
            !this.$v.cropWeather.years_actual_end.numeric && errors.push('Insert a number')
            return errors
        },
        t_baseErrors () {
            const errors = []
            if (!this.$v.cropWeather.t_base.$dirty) return errors
            !this.$v.cropWeather.t_base.decimal && errors.push('Insert a number')
            return errors
        },
        t_maxErrors () {
            const errors = []
            if (!this.$v.cropWeather.t_max.$dirty) return errors
            !this.$v.cropWeather.t_max.decimal && errors.push('Insert a number')
            return errors
        },    
        soil_water_capacityErrors () {
            const errors = []
            if (!this.$v.cropWeather.soil_water_capacity.$dirty) return errors
            !this.$v.cropWeather.soil_water_capacity.numeric && errors.push('Insert a number')
            return errors
        },
        drought_thresholdErrors () {
            const errors = []
            if (!this.$v.cropWeather.drought_threshold.$dirty) return errors
            !this.$v.cropWeather.drought_threshold.numeric && errors.push('Insert a number')
            return errors
        },
        drought_durationErrors () {
            const errors = []
            if (!this.$v.cropWeather.drought_duration.$dirty) return errors
            !this.$v.cropWeather.drought_duration.numeric && errors.push('Insert a number')
            return errors
        },
        frost_thresholdErrors () {
            const errors = []
            if (!this.$v.cropWeather.frost_threshold.$dirty) return errors
            !this.$v.cropWeather.frost_threshold.numeric && errors.push('Insert a number')
            return errors
        },
        frost_durationErrors () {
            const errors = []
            if (!this.$v.cropWeather.frost_duration.$dirty) return errors
            !this.$v.cropWeather.frost_duration.numeric && errors.push('Insert a number')
            return errors
        },                   
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
