<template>
    <v-container>
        <v-row>
            <v-col lg="8" offset-lg="2">
                <v-stepper v-model="e1">
    <v-stepper-header>
      <v-stepper-step
        :complete="e1 > 1"
        step="1"
      >
        Choose destination
      </v-stepper-step>

      <v-divider></v-divider>

      <v-stepper-step
        :complete="e1 > 2"
        step="2"
      >
        Choose flight
      </v-stepper-step>

      <v-divider></v-divider>

      <v-stepper-step step="3">
        Final information
      </v-stepper-step>
    </v-stepper-header>

    <v-stepper-items>
      <v-stepper-content step="1">
        <v-form ref="destinationForm">
             <v-autocomplete 
            class="mt-2"
            v-model="destination" 
            :items="destinations"
            label="Destination"
            dense
            outlined
            rounded
            :rules = "destinationRules"
          />
            
                <v-row>
                    <v-col>
                        <v-text-field  
                        :value = "dates[0]" 
                        label="Start" 
                        outlined 
                        rounded 
                        dense 
                        readonly 
                        @click="dateDialog = true"
                        :rules="dateStartRules"/>
                    </v-col>
                    <v-col>
                        <v-text-field 
                        :value = "dates[1]" 
                        label="End" 
                        outlined 
                        rounded 
                        dense 
                        readonly 
                        @click="dateDialog = true "
                        :rules="dateEndRules"/>
                    </v-col>
                </v-row>
            
          <v-btn block color= "primary" rounded @click="search">
              Search flights
          </v-btn>
        </v-form>
            <v-dialog v-model="dateDialog" width="450">
                <v-card> 
                   <div class="d-flex flex-column">
                        <v-date-picker v-model="dates" range></v-date-picker>
                    <!-- <v-btn @click="dateDialog=false" rounded color="primary">Cancel</v-btn> -->
                        <v-btn @click="dateDialog=false" rounded color="primary">Submit</v-btn>
                   </div>
                </v-card>
            </v-dialog>
      </v-stepper-content>

      <v-stepper-content step="2">
          
              <v-list>
                  <v-virtual-scroll :items="flightsList" height="500px" item-height="80px">
                      <template v-slot:default="{item}">
                            <v-list-item two-line :key="item" @click="flightId = item.id">
                                    <v-list-item-avatar>
                                        <v-img :src="item.image"></v-img>
                                    </v-list-item-avatar>
                                <v-list-item-content>
                                        <v-list-item-title>
                                            {{item.title}}
                                        </v-list-item-title>
                                        <v-list-item-submit>
                                            {{item.dates[0]}} - {{item.dates[1]}}
                                        </v-list-item-submit>
                                </v-list-item-content>
                                <v-list-item-action v-if="item.id===flightId">
                                  <v-chip color="primary">✔</v-chip>
                                </v-list-item-action>
                             </v-list-item>
                      </template>
                </v-virtual-scroll>
             </v-list>
         
          <v-row>
              <v-col>
                  <v-btn rounded dense outlined block color="accent" @click="e1 = 1">
                      Cancel
                  </v-btn>
              </v-col>
              <v-col>
                  <v-btn rounded dense block color="primary" :disabled="flightId===null" @click="e1 = 3" >
                      Search
                  </v-btn>
              </v-col>
          </v-row>
      </v-stepper-content>

      <v-stepper-content step="3">
        <v-card class="mb-12" v-if="selectedFlight">
                <v-img :src="selectedFlight.image">
                    <v-card-title v-text="selectedFlight.title"></v-card-title>
                </v-img>
                <v-card-text>
                    <div class="text-sublite-1">
                        Date: {{selectedFlight.dates[0]}} - {{selectedFlight.dates[1]}}.
                    </div>
                    <div class="text-sublite-1">
                        Destination: {{this.destination}}.
                    </div>
                </v-card-text>
        </v-card>

        <v-row>
                <v-col>
                    <v-btn rounded dense outlined block color="accent" @click="e1 = 2">
                        Cancel
                    </v-btn>
                </v-col>
                <v-col>
                    <v-btn rounded dense block color="primary" :disabled="flightId===null" >
                        Confirm
                    </v-btn>
                </v-col>
            </v-row>
        </v-stepper-content>
        </v-stepper-items>
    </v-stepper>
                </v-col>
            </v-row>
    </v-container>
</template>

<script>
import {fakeList} from "@/helpers/fakeData"
const count = 10000;

export default {
    name: "Booking",
    data() {
        return {
            e1: 1,
            dateDialog: false,
            dates: [],
            destinations: ["Mercury", "Venera","Mars", "Moon" ],
            destination: null,
            flightId: null,
            destinationRules: [v => !!v || 'Destination is required',],
            dateStartRules: [v => !!v || 'Start date is required',],
            dateEndRules: [v => !!v || 'End date is required',],

        };
    },
    methods: {
        search() {
            const isValid = this.$refs["destinationForm"].validate();
            if (!isValid) {
                return;
            }
            this.e1 = 2;
        }
    },
    computed: {
        flightsList() {
            return fakeList([this.dates[0],this.dates[1]], count);
        },
        selectedFlight() {
            return this.flightsList[this.flightId];
        },
    },
};
</script>

<style scoped>
    .List-flights{
        height: 68vh;
        overflow-y: auto;
    }
</style>