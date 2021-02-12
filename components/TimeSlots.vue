<template>
    <div class="time-slots">
        <div 
            class="slotRow" 
            v-for="slot in createSlots(availability[dateSelected.getDay()].Open, availability[dateSelected.getDay()].Close)"
            :key="(''+dateSelected.getYear()+dateSelected.getMonth()+dateSelected.getDay() + slot.getUTCHours() + slot.getUTCMinutes() )"
        >
            <generate-time-slots-count
                :slotHours="double00(slot.getUTCHours())"
                :slotMinutes="double00(slot.getUTCMinutes())"
                :date-selected="dateSelected"
                :double00="double00"
                :slot-date="slot"
                :today="today"
                :formatampm="formatampm"
                :step-click="stepClick"
                :update-time-selected="updateTimeSelected"
            ></generate-time-slots-count>                     
        </div>
    </div>
</template>
<script>
import GenerateTimeSlotsCount from './GenerateTimeSlotsCount';
export default {
    data: function() {
            return {
                
            }
        },
        components: {
            GenerateTimeSlotsCount,
        },
        props: {
            today: Date,
            availability: Object,
            dateSelected: Date,
            updateTimeSelected: Function,
            createSlots: Function,
            formatampm: Function,
            stepClick: Function,
            double00: Function
        },

        beforeMount: function () {
            // Simple GET request using fetch
            // 46bcc02e118caf3510cb20e226a412
            let createDate = "" + this.today.getFullYear() + "-" + (this.double00(this.today.getMonth()+ 1) ) + "-" + this.double00(this.today.getDate());
            fetch(`https://climbnorthwall.com/cockpit-2/api/collections/get/appointments?token=46bcc02e118caf3510cb20e226a412&filter[date]=${createDate}&filter[time]=${this.slotHours}:${this.slotMinutes}`)
                .then(response => response.json())
                .then(data => (
                    this.appointments = data.entries,
                    this.total = data.total,
                    // this.expired = this.checkExpired(),
                    this.loaded = true
                ));
        },

        methods: {
            
        },
}
</script>