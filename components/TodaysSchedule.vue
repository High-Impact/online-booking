<template>
    <section id="todays-schedule">
        <div id="gridTable">
            <div 
                v-for="slot in createSlots(availability[today.getDay()].Open, availability[today.getDay()].Close)"
                :key="slot"
            >
                <generate-time-slots 
                    :slotHours="('0' + slot.getUTCHours()).slice(-2)"
                    :slotMinutes="('0' + slot.getUTCMinutes()).slice(-2)"
                    :today="today"
                    :formatampm="formatampm"
                    :double00="double00"
                    :toggle-booking="toggleBooking"
                ></generate-time-slots>
            </div>
        </div>
    </section>
</template>

<script>
import GenerateTimeSlots from './GenerateTimeSlots';

export default {
        data: function () {
            return {
                loaded:false,
            }
        },
        components: {
            GenerateTimeSlots
        },
        props: {
            availability: Object,
            today: Date,
            // todaysSlots: Array,
            createSlots: Function,
            formatampm: Function,
            double00: Function,
            toggleBooking: Function,
        },
}
</script>