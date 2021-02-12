<template>
    <section id="booking">
        <div class="overlay" @click="toggleBooking(0, today)"></div>
        <div class="container">
            <button @click="toggleBooking(0, today)" id="close-modal">Close</button>

            <div class="stepTitle">
                <h3>{{stepTitles[step]}}</h3>
                <div class="step-buttons">
                    <button :class="{ active: activeStepCheck(0) }" @click="stepClick(0)">1</button>
                    <button :class="{ active: activeStepCheck(1) }" @click="stepClick(1)">2</button>
                    <button :class="{ active: activeStepCheck(2) }" @click="stepClick(2)">3</button>
                </div>
            </div>
            <div class="selected">
                <span :class="{ active: stepGreaterThan(0) }">Date: {{monthNames[dateSelected.getMonth()]}},
                    {{dateSelected.getDate()}}</span>
                <span :class="{ active: stepGreaterThan(1) }"> | Time: {{formatampm(timeSelected)}}</span>
            </div>
            <div class="steps">
                <div class="step" :class="{ active: activeStepCheck(0) }">
                    <calendar is-expanded :attributes='calAttributes' @dayclick="onDayClick"
                        :available-dates='{ start: today, end: new Date(today.getFullYear(),today.getMonth(),today.getDate() + 14) }'>
                    </calendar>
                </div>
                <div class="step" :class="{ active: activeStepCheck(1) }">
                    <time-slots v-if="step == 1" :today="today" :date-selected="dateSelected"
                        :update-time-selected="updateTimeSelected" :availability="availability"
                        :create-slots="createSlots" :formatampm="formatampm" :step-click="stepClick"
                        :double00="double00"></time-slots>
                </div>
                <div class="step" :class="{ active: activeStepCheck(2) }">
                    <final-form v-if="step == 2" :date-selected="dateSelected" :time-selected="timeSelected"
                        :step-click="stepClick" :double00="double00" />
                </div>
                <div class="step" :class="{ active: activeStepCheck(3) }">
                    <div class="thank-you">
                        <h1>Thank you for the submission!</h1>
                        <p>The page will refresh in a moment.</p>
                    </div>
                </div>
            </div>

        </div>
    </section>
</template>

<script>

    import FinalForm from './FinalForm';
    import TimeSlots from './TimeSlots';
    
    
    // import Vue from 'vue';
    // import VCalendar from 'v-calendar';

    // Vue.use(VCalendar);

    import Calendar from 'v-calendar/lib/components/calendar.umd'

    export default {
        components: {
            FinalForm,
            TimeSlots,
            Calendar,
        },
        data: function () {
            return {
                dateSelected: this.today,

                stepTitles: ["Select Date", "Select Time", "Fill out information", "All Set!"],

                canSubmit: false,

                name: "",
                phone: "",
                membtype: "",
                numclimber: "",

                countries: [
                    [
                        'United States',
                        'us',
                        '1',
                        0,
                    ]
                ],

                calAttributes: [{
                    key: 'today',
                    highlight: {
                        color: 'blue',
                        fillMode: 'solid',
                    },
                    dates: this.today,
                }, ],
            }
        },
        props: {
            today: Date,
            availability: Object,
            monthNames: Array,
            step: Number,

            createSlots: Function,
            formatampm: Function,
            double00: Function,

            toggleBooking: Function,

            timeSelected: Date,
            timeUpdated: Boolean,
        },
        methods: {
            onDayClick: function (day) {
                if ((day.date >= new Date(this.today.getFullYear(), this.today.getMonth(), this.today
                    .getDate())) && (day.date <= new Date(this.today.getFullYear(), this.today.getMonth(), this
                        .today.getDate() + 14))) {
                    this.dateSelected = day.date;
                    this.step = 1;
                    this.$emit('update:step', this.step)
                }

            },

            updateTimeSelected: function (date) {
                this.timeSelected = date;
                this.timeUpdated = true;
            },
            stepClick: function (step, override) {
                if ((step > this.step) && (!override)) {
                    console.log('Must pass in override = true');
                } else {
                    this.step = step;
                }
            },
            activeStepCheck: function (thisStep) {
                if (thisStep === this.step) {
                    return true;
                } else {
                    return false;
                }
            },
            stepGreaterThan: function (check) {
                if (this.step > check) {
                    return true;
                } else {
                    return false;
                }
            },

        }
    }
</script>