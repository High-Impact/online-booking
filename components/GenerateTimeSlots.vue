<template>
<div 
                @click="toggleBooking(2, slotDate(slotHours, slotMinutes))"
                v-if="loaded"
                class="slotRow"
                :id="('ID-'+slotHours+slotMinutes)"
                :class="{ expired: expired }"
            >
                <div 
                    class="timeSlot" 
                    :total="total"
                >
                    <h2 class="time">
                        {{timeCorrection(slotHours, slotMinutes)}}
                    </h2>
                    <span class="spots-left">
                        <span v-if="expired">expired</span>
                        <span v-else >{{15 - total}} spot<span v-if="total < 14">s</span> left</span>
                        
                    </span>
                </div>
                <div id="slots-container">
                    <div 
                        v-for="(filled, index ) in appointments" 
                        class="slot filled"
                        :class="{ expired: expired }"
                    ></div>
                    <div 
                        v-for="(empty, index ) in (15 - total)" 
                        :key="empty._id"
                        :index="index"
                        :id="empty._id"
                        class="slot empty"
                        :class="{ expired: expired }"
                    ></div>
                </div>
            </div>
            <div v-else>
                <div id="loading" class="slotRow">
                    <div total="0" class="timeSlot">
                    <h2 class="time">{{timeCorrection(slotHours, slotMinutes)}}</h2>
                    <span class="spots-left">Loading...</span>
                </div> 
                <div id="slots-container"> 
                    <div index="0" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="1" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="2" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="3" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="4" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="5" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="6" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="7" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="8" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="9" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="10" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="11" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="12" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="13" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    <div index="14" class="slot loading"><img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px.gif"/></div>
                    </div>
                </div>
            </div>
</template>
    
<script>
export default {
    data: function () {
            return {
                appointments:Array,
                total:Number,
                expired: Boolean,
                loaded: false,

                itemSelected: 0,
            }
        },
        props: {
            slotHours: String,
            slotMinutes: String,
            today: Date,
            formatampm: Function,
            double00: Function,
            toggleBooking: Function,
            // checkExpired: Function
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
                    this.expired = this.checkExpired(),
                    this.loaded = true
                ));
        },

        methods: {
            checkExpired: function() {
                let now = ('0' + this.today.getHours()).slice(-2) + ('0' + this.today.getMinutes()).slice(-2);
                let slot = "" + this.slotHours + this.slotMinutes;
                
                if (now > slot) {
                    return true;
                } else {
                    return false;
                }
            },
            timeCorrection: function(hours, minutes) {
                let date = new Date(this.today.getFullYear(),this.today.getMonth(),this.today.getDate(),(this.double00(hours) - 6), this.double00(minutes));
                return this.formatampm(date);
            },
            slotDate: function(hours, minutes) {
                let date = new Date(this.today.getFullYear(),this.today.getMonth(),this.today.getDate(),(this.double00(hours) - 6), this.double00(minutes));
                return date;
            },
            
        },
}
</script>