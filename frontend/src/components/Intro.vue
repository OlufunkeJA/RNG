<template>
    <!-- HTML HERE -->    
    <h2>New Template</h2>

    <header></header>
    <nav></nav> //navigation links
    <section></section>
    <aside></aside> //sidebar
    <footer></footer>

    <v-container class = "container">
       <v-row class = "row, bg-surfaceVariant" align = "center">
            <v-col class = "bg-black col col1" align = "center">
                <v-card title = "LED A" width = "150" density = "compact" border flat rounded = "md">
                    <v-divider></v-divider>
                    <v-card-item>
                        <v-icon v-if="payload.ledA == 0" size="50" icon="mdi:mdi-lightbulb"></v-icon>
                        <v-icon v-if="payload.ledA == 1" size="50" icon="mdi:mdi-lightbulb-on" color="yellow"></v-icon>
                    </v-card-item>
                    <v-card-item>
                        <v-btn class = "text-caption" text = "Toggle" variant = "tonal" color = "primary" density = "compact" @click="toggle('LED A')"></v-btn>
                    </v-card-item>
                </v-card>
            </v-col>
            <v-col class = "bg-black col col2" align = "center">
                <v-card title = "LED B" width = "150" density = "compact" border flat rounded = "md">
                    <v-divider></v-divider>
                    <v-card-item>
                        <v-icon v-if="payload.ledB == 0" size="50" icon="mdi:mdi-lightbulb"></v-icon>
                        <v-icon v-if="payload.ledB == 1" size="50" icon="mdi:mdi-lightbulb-on" color="yellow"></v-icon>
                    </v-card-item>
                    <v-card-item>
                        <v-btn class = "text-caption" text = "Toggle" variant = "tonal" color = "primary" density = "compact" @click="toggle('LED A')"></v-btn>
                    </v-card-item>
                </v-card>
            </v-col>
        </v-row>

        <v-row class = "row" align = "center">
            <v-col class = "bg-black col col3" align = "center">
                <span class="digit text-error">
                    <span v-if="payload.number == 0" >&#x1fbf0;</span>
                    <span v-else-if="payload.number == 1" >&#x1fbf1;</span>
                    <span v-else-if="payload.number == 2" >&#x1fbf2;</span>
                    <span v-else-if="payload.number == 3" >&#x1fbf3;</span>
                    <span v-else-if="payload.number == 4" >&#x1fbf4;</span>
                    <span v-else-if="payload.number == 5" >&#x1fbf5;</span>
                    <span v-else-if="payload.number == 6" >&#x1fbf6;</span>
                    <span v-else-if="payload.number == 7" >&#x1fbf7;</span>
                    <span v-else-if="payload.number == 8" >&#x1fbf8;</span>
                </span>
            </v-col>
        </v-row>
    </v-container>
</template>

<script setup>
/** JAVASCRIPT HERE */

// IMPORTS
import { ref,reactive,watch ,onMounted,onBeforeUnmount,computed } from "vue";  
import { useRoute ,useRouter } from "vue-router";
import { useMqttStore } from '@/store/mqttStore'; // Import Mqtt Store
import { storeToRefs } from "pinia";
 
// VARIABLES
const router = useRouter();
const route = useRoute();  
const Mqtt = useMqttStore();
const { payload, payloadTopic } = storeToRefs(Mqtt);

// FUNCTIONS
onMounted(()=>{
    // THIS FUNCTION IS CALLED AFTER THIS COMPONENT HAS BEEN MOUNTED
    Mqtt.connect(); // Connect to Broker located on the backend
    setTimeout( ()=>{
    // Subscribe to each topic
    Mqtt.subscribe("topic1");
    Mqtt.subscribe("topic2");
    },3000);    
});


onBeforeUnmount(()=>{
    // THIS FUNCTION IS CALLED RIGHT BEFORE THIS COMPONENT IS UNMOUNTED
    // unsubscribe from all topics
    Mqtt.unsubcribeAll();
});

const toggle = (name) => {
    let message = JSON.stringify({"type":"toggle","device": name}); // Create message and convert to a json string
    Mqtt.publish("topic",message); // Publish message to appropriate topic
}

</script>


<style scoped>
/** CSS STYLE HERE */
    v-container.container{
        /*border: 1px solid;
        border-color: blue;*/
        height: 100%;
    }

    v-row.row{
        text-align: center;
        width: 100%;
        padding: 10px;
        margin-top: 10px;
        margin-bottom: 10px;
        margin-left: 0px;
        margin-right: 0px;
        /*border: 1px solid;
        border-color: purple;*/
    }

    v-row.bg-surfaceVariant{
        background-color:antiquewhite;
    }

    v-col.col{
        text-align: center;
        /*border: 1 px solid;
        border-color: green;*/
    }

    v-col.col1, v-col.col2{
        max-width: 200px;
    }

    v-col.col3{
        height: 320px;
        max-width: 270px;
        border: 2px solid;
        border-color:lightslategray;
    }

    @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+Symbols+2&display=swap');

    .digit{
        font-family: Noto Sans Symbols 2;
        font-size: 250px;
    }
</style>
  