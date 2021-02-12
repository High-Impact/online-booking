<template>
    <form class="final-form" @submit.prevent="submitButtonPressed">
                <div class="row">
                    <div class="half field">
                        <input v-on:keyup="checkForm" class="input" v-model="name" type="text" id="name" name="name" placeholder=" ">
                        <label class="label" for="name">Full name</label>
                    </div>
                    <div class="half field">
                        <vue-tel-input v-model="phone"></vue-tel-input>
                        <input v-on:keyup="checkForm" class="input" v-model="phone" type="text" id="phone" name="phone" placeholder=" ">
                        <label class="label" for="phone">Phone Number</label>
                    </div>
                </div>
                <div class="row">
                    <div class="half field">
                    <select v-on:change="checkForm" :css-attr="membtype" required class="input" v-model="membtype" type="select" id="membtype" name="membtype">
                        <option value="null" disabled selected></option>
                        <option value="Member">Member</option>
                        <option value="Day Climber">Day Climber</option>
                        <option value="First Time Climber">First Time Climber</option>
                    </select>
                    <label for="membtype" class="label">Climber Type</label>

                    </div>
                    <div class="half field">
                        <select v-on:change="checkForm" :css-attr="numclimber" class="input" v-model="numclimber" type="select" id="numclimber" name="numclimber">
                            <option value="null" disabled selected></option>
                            <option v-for="(value,index) in total" :value="value" v-if="index <= 4">{{value}}</option>
                        </select>
                        <label for="numclimber" class="label">Number of climbers</label>

                    </div>

                </div>
                <input 
                    @click="submitButtonPressed" 
                    id="final-submit" 
                    type="submit" 
                    value="Submit"
                    :class="{ active: canSubmit }"
                >
            </form>
</template>

<script>
import { VueTelInput } from 'vue-tel-input'

export default {
    data: function() {
            return {
                total:0,
                canSubmit:false,

                name:"",
                phone:"",
                membtype:"", 
                numclimber: "",
                
            }
        },
        components: {
            VueTelInput,
        },
        props: {
            dateSelected:Date,
            timeSelected:Date,
            stepClick:Function,
            double00:Function,

            
        },

        beforeMount: function () {
            // Simple GET request using fetch
            // 46bcc02e118caf3510cb20e226a412
            var hours = ('0' + this.timeSelected.getUTCHours()).slice(-2);
            var minutes = ('0' + this.timeSelected.getUTCMinutes()).slice(-2);
            var time = `${hours}:${minutes}`;
            let createDate = "" + this.dateSelected.getFullYear() + "-" + (this.double00(this.dateSelected.getMonth()+ 1) ) + "-" + this.double00(this.dateSelected.getDate());
            fetch(`https://climbnorthwall.com/cockpit-2/api/collections/get/appointments?token=46bcc02e118caf3510cb20e226a412&filter[date]=${createDate}&filter[time]=${time}`)
                .then(response => response.json())
                .then(data => (
                    this.total = 15 - data.total
                ));
        },

        methods: {
            checkForm: function() {
                if(
                    (this.name) &&
                    (this.phone) &&
                    (this.membtype) &&
                    (this.numclimber)
                ) {
                    this.canSubmit = true
                } else {
                    this.canSubmit = false
                }
            },
            submitButtonPressed: function(e) {
                e.preventDefault();
                if (this.canSubmit) {
                    console.group("Form Data")
                        console.log(this.name.split(" ")[0]);
                        console.log(this.name.split(" ")[1]);
                        console.log(this.phone);
                        console.log(this.dateSelected);
                        console.log(this.timeSelected);
                        console.log(this.membtype);
                        console.log(this.numclimber);
                    console.groupEnd()
    
                    for (let index = 0; index < this.numclimber; index++) {
                        var hours = ('0' + this.timeSelected.getUTCHours()).slice(-2);
                        var minutes = ('0' + this.timeSelected.getUTCMinutes()).slice(-2);
                        var time = `${hours}:${minutes}`;
                        let createDate = "" + this.dateSelected.getFullYear() + "-" + (this.double00(this.dateSelected.getMonth()+ 1) ) + "-" + this.double00(this.dateSelected.getDate());
                        fetch(`https://climbnorthwall.com/cockpit-2/api/collections/save/appointments?token=46bcc02e118caf3510cb20e226a412&data[firstName]=${this.fname}&data[lastName]=${this.lname}&data[memberType]=${this.membtype}&data[phone]=${this.phone}&data[date]=${createDate}&data[time]=${time}`)
                        .then(response => response.json())
                        .then(data => (
                            console.log(data),
                            console.log('sent')
                        ));
                    }

                    this.stepClick(3, true)
                    // this.step = 3;
                    
                    setTimeout(function(){ location.reload(true); }, 3000);

                }
                
            } 
        },
}
</script>