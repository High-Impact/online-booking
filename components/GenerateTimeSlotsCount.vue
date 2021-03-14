<template>
    <button 
                @click="timeSlotClicked(slotDate.getUTCHours(), slotDate.getUTCMinutes())"
                class="timeSlot"
                :class="{ expired: expired}"
                :total="total"
            >
                <span class="time">{{ formatampm(slotDate) }}</span>
                <div>
                    <span v-if="!loaded" class="slots-left">Loading...</span>
                    <span v-if="loaded" class="slots-left">
                        <span v-if="expired">EXPIRED</span>
                        <span v-else>{{15 - total}} Spot<span v-if="total < 14">s</span> Left</span>
                    
                    </span>
                </div>
            </button>
</template>
<script>
export default {
    data: function () {
            return {
                total:Number,
                loaded:false,
                expired:false,
            }
        },
        props: {
            slotHours: String,
            slotMinutes: String,
            dateSelected: Date,
            today:Date,
            slotDate:Date,
            double00: Function,
            formatampm: Function,
            stepClick: Function,
            updateTimeSelected: Function,
            
        },
        methods: {
            checkExpired: function() {
                let now = "" + this.double00(this.today.getMonth()) + this.double00(this.today.getDate()) + this.double00(this.today.getHours()) + this.double00(this.today.getMinutes());
                let slot = "" + this.double00(this.dateSelected.getMonth()) + this.double00(this.dateSelected.getDate()) + this.double00(this.slotHours) + this.double00(this.slotMinutes);
                if (now > slot) {
                    this.expired = true;
                } else {
                    this.expired = false;
                }
            },
            timeSlotClicked: function(hours, minutes) {
                if ( !this.expired ) {
                    this.stepClick(2, true)
                    // this.step = 2;
                    let selected = new Date(this.dateSelected.getFullYear(), this.dateSelected.getMonth(), this.dateSelected.getDate(), this.double00(hours) - 5, this.double00(minutes), 0);
                    this.updateTimeSelected(selected);
                }
            }
        },

        beforeMount: function () {
            // Simple GET request using fetch
            // 46bcc02e118caf3510cb20e226a412
            let createDate = "" + this.dateSelected.getFullYear() + "-" + (this.double00(this.dateSelected.getMonth()+ 1) ) + "-" + this.double00(this.dateSelected.getDate());
            fetch(`https://climbnorthwall.com/cockpit-2/api/collections/get/appointments?token=46bcc02e118caf3510cb20e226a412&filter[date]=${createDate}&filter[time]=${this.slotHours}:${this.slotMinutes}`)
                .then(response => response.json())
                .then(data => (
                    this.total = data.total,
                    this.checkExpired(),
                    this.loaded = true
                ));
        },
}
</script>