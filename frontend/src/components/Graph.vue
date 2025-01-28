<template>
    <!-- HTML HERE -->    
    <h2>New Template</h2>

    <v-container class = "container-fluid" align = "center">
        <v-row class = "row" align = "center">
            <v-col class = "col" align = "center" cols = "12"><canvas id="myChart"></canvas></v-col>
            <v-col class = "col" align = "center" cols = "12"></v-col>
            <v-btn class = "btn" text = "Refresh Graph" flat color = "secondary" variant = "outlined" @click = "updateGraph()"></v-btn>
        </v-row>
        <v-row class = "row">
            <div class= "mt-5 mb-5"></div>
        </v-row>
        <v-row class = "row" align = "center">
            <v-card class = "card mt-5 mb-5 mr-5 ml-5" subtitle = "LED A" width = "150" flat border align = "center" >
                <v-card-item pa-0>Turned On</v-card-item>
                <v-card-item pa-0>
                    <span class = "text-h5, text-primary, font-weight-bold">
                        {{led_A}}
                    </span>
                </v-card-item>
                <v-card-item pa-0>Times</v-card-item>
                <v-card-item>   
                    <v-btn class = "ma-1 text-caption" text = "Update" rounded = "pill" flat color = "secondary" variant = "tonal" @click = "updateLEDCount('ledA')"></v-btn> 
                </v-card-item>
            </v-card>
            <v-card class = "card mt-5 mb-5 mr-5 ml-5" subtitle = "LED B" width = "150" flat border align = "center" >
                <v-card-item pa-0>Turned On</v-card-item>
                <v-card-item pa-0>
                    <span class = "text-h5, text-primary, font-weight-bold">
                        {{led_B}}
                    </span>
                </v-card-item>
                <v-card-item pa-0>Times</v-card-item>
                <v-card-item>
                    <v-btn text = "Update" class = "ma-1 text-caption" rounded = "pill" flat color = "secondary" variant = "tonal" @click = "updateLEDCount('ledB')"></v-btn>
                </v-card-item>
            </v-card>
        </v-row>
    </v-container>
</template>

<script setup>
/** JAVASCRIPT HERE */

// IMPORTS
import { ref,reactive,watch ,onMounted,onBeforeUnmount,computed } from "vue";  
import { useRoute ,useRouter } from "vue-router";
import Chart from 'chart.js/auto'; 
import { useAppStore } from "@/store/appStore";
 
// VARIABLES
const AppStore = useAppStore();
const router = useRouter();
const route = useRoute(); 
const led_A = ref(0); // Store count for LED A
const led_B = ref(0); // Store count for LED B
let chart = null; // Chart object
const data = { labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
                datasets: [
                    {
                    label: 'Fully Rounded',
                    data: [0, 0, 0, 0, 0, 0],
                    borderColor: '#1ECBE1',
                    backgroundColor: '#4BD5E7',
                    borderWidth: 2,
                    borderRadius: 5,
                    borderSkipped: false,
                }]
            };
const config = { type: 'bar',
                data: data,
                options: {
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'top',
                        },
                    title: {
                        display: true,
                        text: 'Chart.js Bar Chart'
                        }
                    }
                },
            };

// FUNCTIONS
onMounted(()=>{
    // THIS FUNCTION IS CALLED AFTER THIS COMPONENT HAS BEEN MOUNTED
    const ctx = document.querySelector('#myChart'); // Select canvas for rendering chart
    chart = new Chart(ctx, config ); // create chart
});


onBeforeUnmount(()=>{
    // THIS FUNCTION IS CALLED RIGHT BEFORE THIS COMPONENT IS UNMOUNTED
});

// Update graph with labels and new data
const updateData = ( chart, label, newData) => {
    chart.data.labels = label;
    chart.data.datasets[0].data = newData;
    chart.update();
}

// Fetch new data and update graph
const updateGraph = async () =>{
    let result = await AppStore.getFrequencies();
    let labels = [];
    let data = [];
    if (result.length > 0){
        result.forEach(obj => {
            labels.push(obj["number"])
            data.push(obj["frequency"])
        });
        updateData(chart,labels,data);
    }
}

// Fetch new data and update cards
const updateLEDCount = async(name)=>{
    let result = await AppStore.getOnCount(name);
    // console.log(result);
    if (name == "ledA"){
        led_A.value = result;
    }
    if (name == "ledB"){
        led_B.value = result;
    }
}

</script>


<style scoped>
/** CSS STYLE HERE */
v-row.row{
    max-width: 1200px;
}

</style>
  