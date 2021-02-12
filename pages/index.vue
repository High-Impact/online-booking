
<template id="vue">
    <div>
        <template>
            <section id="message-header">

                <div class="text">
                    <h2><span class="date">{{monthNames[today.getMonth()]}} {{today.getDate()}}</span><br>Today's Schedule</h2>
                    <p>Due to COVID-19, we have limited climbers to 90 minute time slots with a max capacity of 15 climbers. This tool will help you find and book your spot on our schedule!</p>
                </div>

                <button 
                    id="booking-button"
                    @click="toggleBooking(0, today)"
                >Book your spot now</button>

            </section>


            <todays-schedule
                v-if="availabilityLoaded"
                :availability="availability"
                :today="today"
                :create-slots="createSlots"
                :formatampm="formatAMPM"
                :double00="double00"
                :toggle-booking="toggleBooking"
            >
            </todays-schedule>
            <div v-else class="todays-schedule-loading">
                <img src="https://climbnorthwall.com/wp-content/uploads/2021/02/Rolling-0.9s-200px-teal.gif"/>
                <h6>Sorry for the wait.</h6>
                <p>We are gettings todays live schedule.</p>
            </div>

            <button 
                    id="mb-booking-button"
                    @click="toggleBooking(0, today)"
                >Book your spot now</button>


            <booking-modal
                v-if="booking"
                :today="today"
                :availability="availability"
                :month-names="monthNames"

                :step.sync="step"
                :date-selected.sync="dateSelected"
                :time-selected.sync="timeSelected"
                :time-updated.sync="timeUpdated"
                
                :create-slots="createSlots"
                :formatampm="formatAMPM"
                :double00="double00"
                
                :toggle-booking="toggleBooking"
                
            ></booking-modal>


        </template>
        
    </div>
</template>


<script>

    import BookingModal from '../components/BookingModal';
    
    import TodaysSchedule from '../components/TodaysSchedule';
    
    
    export default {
        components: {
            BookingModal,
            TodaysSchedule
        },
        data() {
            let today = new Date();

            return {
                availabilityLoaded: false,
                availability: Array,

                today: new Date(),
                monthNames: ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December" ],

                todaysSlots: [],
                editingSlots: [],

                booking: false,
                
                dateSelected: this.today,
                timeSelected: this.today,
                timeUpdated:false,

                step: 0,
            }
        },
        beforeMount: function () {
            // Simple GET request using fetch
            // 46bcc02e118caf3510cb20e226a412
            fetch(
                    "https://climbnorthwall.com/cockpit-2/api/singletons/get/availability?token=46bcc02e118caf3510cb20e226a412")
                .then(response => response.json())
                .then(data => (
                    this.availability = data,
                    this.availabilityLoaded = true
                ));
        },
        methods: {
            createSlots: function (open, close) {
                // 43,200,000
                var start = Date.parse(`01 Jan 1970 ${open} GMT`);
                // 79,200,000
                var stop = Date.parse(`01 Jan 1970 ${close} GMT`);
                // 90 minutes in milliseconds
                var interval = 5400000;

                // 36,000,000
                var differance = stop - start;

                var slots = [];

                for (let index = 0; index < Math.floor(differance / interval); index++) {
                    if (index == 0) {
                        slots.push(new Date(start));
                    }
                    let addition = interval * (index + 1);
                    slots.push(new Date(start + addition));
                }
                return slots;
            },
            formatAMPM: function (date) {
                var hours = date.getUTCHours();
                var minutes = date.getUTCMinutes();
                var ampm = hours >= 12 ? 'pm' : 'am';
                hours = hours % 12;
                hours = hours ? hours : 12; // the hour '0' should be '12'
                minutes = minutes < 10 ? '0' + minutes : minutes;
                var strTime = hours + ':' + minutes + ' ' + ampm;
                return strTime;
            },

            
            double00: function( number ) {
                return ('0' + number).slice(-2);
            },
            updateSelected: function(step, slot) {
                this.step = step;
                this.timeSelected = slot;
                this.timeUpdated = true;
            },
            toggleBooking: function(step, slot) {

                this.updateSelected(step, slot);
                
                console.log(this.booking);
                
                this.booking = !this.booking;

                console.log(this.booking);
            },

            
        }
    }
</script>
<style type="text/css">
:root {
    --color-pri: rgb(26,97,160);
    --color-dark: rgb(78,66,70);
    --color-gray-l: rgb(241,241,241);
    --color-gray-d: rgb(221,221,221);
    --color-yellow: rgb(224,223,96);
    --font-pop: "Poppins",sans-serif;
    --font-ubuntu: "Ubuntu",sans-serif;
}
#__layout {
    color: var(--color-dark);
    background: #ddd;
    display: flex;
    justify-content: center;
    height: 100vh;
    align-items: flex-start;
    font-size: 18px;
    padding-top:5em;
}
  #__layout #message-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding-bottom: 2em; }
    #__layout #message-header .text {
      width: 750px;
      max-width: 70%; }
      #__layout #message-header .text h2 {
        font-size: 2em;
        margin-top: 0; }
        #__layout #message-header .text h2 span.date {
          font-size: .75em;
          font-style: italic; }
      #__layout #message-header .text p {
        margin: 0; }
    #__layout #message-header #booking-button {
      background: var(--color-pri);
      border: none;
      color: white;
      padding: 0.5em;
      font-weight: 900;
      text-transform: uppercase;
      border-radius: 0;
      transition: 300ms ease;
      border: 2px solid var(--color-pri);
      box-shadow: 0px 9px 5px -5px rgba(0, 0, 0, 0.25); }
      #__layout #message-header #booking-button:hover {
        box-shadow: 0px 19px 5px -10px rgba(0, 0, 0, 0.1);
        transform: translateY(-5px); }
  #__layout .todays-schedule-loading {
    display: flex;
    justify-content: center;
    flex-direction: column;
    text-align: center;
    padding: 2em 0; }
    #__layout .todays-schedule-loading h6 {
      color: #22a0ad;
      font-size: 2em;
      text-transform: capitalize;
      margin: 0; }
    #__layout .todays-schedule-loading img {
      width: 5em;
      height: 5em;
      margin: auto; }
  #__layout #mb-booking-button {
    display: none; }
  #__layout #todays-schedule #gridTable div:nth-last-of-type(1) {
    border-bottom: 0 !important; }
  #__layout #todays-schedule #gridTable > div {
    border-bottom: 1px solid;
    margin-bottom: 1em;
    padding-bottom: 1em; }
  #__layout #todays-schedule #gridTable .slotRow {
    display: grid;
    grid-template-columns: 2fr 15fr;
    grid-gap: 1em;
    align-items: center;
    opacity: .75;
    transition: 375ms ease;
    cursor: pointer; }
    #__layout #todays-schedule #gridTable .slotRow:hover {
      transform: scale(1.04);
      opacity: 1; }
    #__layout #todays-schedule #gridTable .slotRow .timeSlot .time {
      font-size: 1.25em;
      font-weight: 900;
      margin: 0; }
    #__layout #todays-schedule #gridTable .slotRow .timeSlot .spots-left {
      font-size: 0.75em;
      font-weight: 400;
      color: var(--color-dark);
      margin-top: -0.25em;
      display: block; }
    #__layout #todays-schedule #gridTable .slotRow #slots-container {
      width: 100%;
      display: grid;
      grid-template-columns: repeat(15, 1fr);
      grid-gap: 1em; }
      #__layout #todays-schedule #gridTable .slotRow #slots-container .slot {
        display: inline-flex;
        justify-content: center;
        align-items: center;
        background: white;
        border: 1px solid var(--color-dark);
        border-radius: .3em;
        padding-top: 100%;
        position: relative;
        box-shadow: 0px 6px 5px -5px rgba(0, 0, 0, 0.25); }
      #__layout #todays-schedule #gridTable .slotRow #slots-container .filled {
        background: #d54c34; }
        #__layout #todays-schedule #gridTable .slotRow #slots-container .filled:after {
          content: "X";
          position: absolute;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);
          color: white;
          font-weight: 900;
          font-size: 2rem; }
      #__layout #todays-schedule #gridTable .slotRow #slots-container .loading img {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 50%; }
      #__layout #todays-schedule #gridTable .slotRow #slots-container .expired {
        opacity: .5;
        background: var(--color-dark); }
  #__layout #booking {
    position: fixed;
    top: 0;
    left: 0;
    background: rgba(78, 66, 70, 0.5);
    width: 100vw;
    height: 100vh;
    z-index: 999999999;
    display: flex;
    align-items: center; }
    #__layout #booking .overlay {
      z-index: -1;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%; }
    #__layout #booking .container {
      width: 80%;
      max-width: 600px;
      max-height: 70vh;
      margin: auto;
      padding: 2em;
      background: var(--color-gray-l);
      border-radius: .3em;
      box-shadow: 0px 5px 10px -6px black;
      border: 2px solid;
      position: relative; }
      #__layout #booking .container #close-modal {
        background: transparent;
        border: 0;
        font-weight: 900;
        color: white;
        position: fixed;
        top: 1em;
        right: 1em;
        transition: 275ms;
        text-transform: uppercase;
        font-size: 2em; }
        #__layout #booking .container #close-modal:focus {
          outline: none; }
        #__layout #booking .container #close-modal:hover {
          opacity: .5; }
      #__layout #booking .container .stepTitle {
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-bottom: 1em;
        padding-bottom: 1em;
        border-bottom: 1px solid var(--color-dark); }
        #__layout #booking .container .stepTitle h3 {
          margin: 0;
          font-size: 2rem; }
        #__layout #booking .container .stepTitle .step-buttons button {
          background: rgba(78, 66, 70, 0.25);
          border: 0;
          border-radius: 100%;
          height: 2em;
          width: 2em;
          margin-left: .5em;
          transition: 375ms; }
          #__layout #booking .container .stepTitle .step-buttons button.active, #__layout #booking .container .stepTitle .step-buttons button:hover {
            background: var(--color-pri);
            color: white; }
          #__layout #booking .container .stepTitle .step-buttons button:focus {
            outline: none; }
      #__layout #booking .container .selected span {
        opacity: 0;
        transition: 500ms; }
        #__layout #booking .container .selected span.active {
          opacity: 1; }
      #__layout #booking .container .steps {
        position: relative;
        overflow: hidden;
        min-height: 300px; }
        #__layout #booking .container .steps .step {
          /* position: absolute; */
          opacity: 0;
          visibility: hidden;
          transition: 500ms;
          position: absolute;
          top: 1em;
          left: 50%;
          transform: translate(-50%);
          width: 100%; }
          #__layout #booking .container .steps .step.active {
            max-height: 100vh;
            visibility: visible;
            opacity: 1; }
          #__layout #booking .container .steps .step .time-slots {
            grid-template-columns: 1fr 1fr 1fr;
            display: grid;
            grid-gap: 1em; }
            #__layout #booking .container .steps .step .time-slots .slotRow {
              /* grid-template-columns: 1fr;
                                                border-radius: .3em;
                                                opacity:1;
                                                width:100%;
                                                background: var(--color-pri); */ }
              #__layout #booking .container .steps .step .time-slots .slotRow.expired {
                opacity: .5; }
                #__layout #booking .container .steps .step .time-slots .slotRow.expired .timeSlot {
                  opacity: 1;
                  background: var(--color-dark) !important; }
              #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot {
                background: var(--color-dark);
                width: 100%;
                padding: 1em;
                border-radius: .3em;
                align-items: flex-start;
                border: 0;
                display: flex;
                flex-direction: column;
                align-items: flex-start;
                transition: 300ms ease;
                box-shadow: 0px 9px 5px -5px rgba(0, 0, 0, 0.25); }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot:hover {
                  transform: translateY(-5px);
                  box-shadow: 0px 19px 5px -10px rgba(0, 0, 0, 0.25); }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="15"] {
                  background: #d54c34; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="14"] {
                  background: #22a0ad; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="13"] {
                  background: #22a0ad; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="12"] {
                  background: #22a0ad; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="11"] {
                  background: #22a0ad; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="10"] {
                  background: #1d81a7; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="9"] {
                  background: #1d81a7; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="8"] {
                  background: #1d81a7; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="7"] {
                  background: #1d81a7; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="6"] {
                  background: #1d81a7; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="5"] {
                  background: #1a61a0; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="4"] {
                  background: #1a61a0; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="3"] {
                  background: #1a61a0; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="2"] {
                  background: #1a61a0; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="1"] {
                  background: #1a61a0; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="0"] {
                  background: white;
                  border: 1px solid var(--color-dark); }
                  #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="0"] .time, #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot[total="0"] .slots-left {
                    text-shadow: none;
                    color: var(--color-dark); }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot.expired {
                  cursor: not-allowed;
                  background: var(--color-dark);
                  box-shadow: none;
                  opacity: .7; }
                  #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot.expired:hover {
                    transform: translateY(0px);
                    box-shadow: none; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot .time {
                  color: white;
                  font-weight: 700;
                  font-size: 1.25em;
                  margin-right: auto; }
                #__layout #booking .container .steps .step .time-slots .slotRow .timeSlot .slots-left {
                  color: white;
                  margin-top: -0.25em;
                  font-weight: 500;
                  font-size: 1em;
                  text-transform: lowercase;
                  display: block; }
              #__layout #booking .container .steps .step .time-slots .slotRow #slots-container {
                display: none; }
          #__layout #booking .container .steps .step .final-form {
            display: flex;
            align-items: flex-start;
            flex-direction: column;
            margin: 0; }
            #__layout #booking .container .steps .step .final-form .row {
              display: flex;
              justify-content: space-between;
              width: 100%;
              margin-top: 2rem; }
              #__layout #booking .container .steps .step .final-form .row input {
                width: 100%; }
              #__layout #booking .container .steps .step .final-form .row .half {
                width: calc(50% - 1em); }
              #__layout #booking .container .steps .step .final-form .row .third {
                width: calc(33.333% - 1em); }
              #__layout #booking .container .steps .step .final-form .row .vue-tel-input {
                display: none; }
              #__layout #booking .container .steps .step .final-form .row .field {
                margin: 0;
                position: relative;
                border-bottom: 1.25px solid var(--color-dark);
                transition: 500ms; }
                #__layout #booking .container .steps .step .final-form .row .field::after {
                  content: "";
                  position: relative;
                  display: block;
                  height: 4px;
                  width: 100%;
                  background: var(--color-pri);
                  transform: scaleX(0);
                  transform-origin: 0%;
                  opacity: 0;
                  transition: all 500ms ease;
                  top: 2px; }
                #__layout #booking .container .steps .step .final-form .row .field:focus-within {
                  border-color: transparent; }
                #__layout #booking .container .steps .step .final-form .row .field:focus-within::after {
                  transform: scaleX(1);
                  opacity: 1; }
                #__layout #booking .container .steps .step .final-form .row .field:focus-within label,
                #__layout #booking .container .steps .step .final-form .row .field input:not(:placeholder-shown) + .label,
                #__layout #booking .container .steps .step .final-form .row .field select:not([css-attr=""]) + .label {
                  transform: scale(0.8) translateY(-2rem);
                  opacity: 1; }
                #__layout #booking .container .steps .step .final-form .row .field .label {
                  color: var(--color-dark);
                  font-size: 1.2rem;
                  position: absolute;
                  transform-origin: 0%;
                  transition: transform 400ms;
                  cursor: pointer;
                  width:100%;
                  left:0;
                  font-weight:500; }
                #__layout #booking .container .steps .step .final-form .row .field .input {
                  outline: none;
                  border: none;
                  overflow: hidden;
                  margin: 0;
                  width: 100%;
                  padding: 0.25rem 0;
                  background: none;
                  color: var(--color-dark);
                  font-size: 1.2em;
                  font-weight: bold;
                  transition: border 500ms;
                  box-shadow: none;
                  height: 1.5em;
                  cursor: pointer; }
              #__layout #booking .container .steps .step .final-form .row select {
                width: 100%;
                background: transparent;
                box-shadow: none;
                border: 0;
                border-bottom: 2px dashed var(--color-dark);
                border-radius: 0;
                font-weight: 900;
                font-size: 1.25em; }
            #__layout #booking .container .steps .step .final-form input#final-submit {
              box-shadow: none;
              background: transparent;
              border: 3px solid;
              padding: 0.5em;
              font-weight: 900;
              text-transform: uppercase;
              text-shadow: none;
              display: block;
              position: relative;
              width: auto;
              margin-top: 2rem;
              color: var(--color-dark);
              opacity: .5;
              cursor: not-allowed; }
              #__layout #booking .container .steps .step .final-form input#final-submit.active {
                opacity: 1;
                color: var(--color-pri);
                cursor: pointer; }
          #__layout #booking .container .steps .step .thank-you h1 {
            font-size: 1.5em; }
      #__layout #booking .container .cal-time {
        display: flex;
        justify-content: space-between; }
  @media only screen and (max-width: 850px) and (min-width: 550px) {
    #__layout #todays-schedule #gridTable > div {
      width: calc(33.333% - .5em) !important; }
    #__layout #booking .container {
      max-height: 35rem !important; }
      #__layout #booking .container .steps .step .time-slots {
        grid-template-columns: 1fr 1fr 1fr !important; } }
  @media (max-width: 850px) {
    #__layout #message-header .text {
      max-width: 100%; }
    #__layout #message-header #booking-button {
      display: none; }
    #__layout #mb-booking-button {
      display: block;
      margin: auto;
      margin-top: 3rem;
      font-size: 1.1rem;
      padding: 1em;
      background: var(--color-dark);
      border: none;
      color: white;
      padding: 0.5em;
      font-weight: 900;
      text-transform: uppercase;
      border-radius: 0;
      transition: 300ms ease;
      box-shadow: 0px 9px 5px -5px rgba(0, 0, 0, 0.25); }
      #__layout #mb-booking-button:hover {
        box-shadow: 0px 19px 5px -10px rgba(0, 0, 0, 0.1);
        transform: translateY(-5px); }
    #__layout #todays-schedule #gridTable {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between; }
      #__layout #todays-schedule #gridTable > div {
        width: calc(50% - .5em);
        padding: 0;
        border-radius: .3em;
        border: 0; }
      #__layout #todays-schedule #gridTable .loading {
        color: white; }
      #__layout #todays-schedule #gridTable .slotRow {
        grid-template-columns: 1fr;
        border-radius: .3em;
        opacity: 1;
        background: var(--color-pri); }
        #__layout #todays-schedule #gridTable .slotRow.expired {
          opacity: .5; }
          #__layout #todays-schedule #gridTable .slotRow.expired .timeSlot {
            opacity: 1;
            background: var(--color-dark) !important; }
        #__layout #todays-schedule #gridTable .slotRow .timeSlot {
          padding: 1em;
          background: var(--color-pri);
          border-radius: .3em;
          box-shadow: 0px 9px 5px -5px rgba(0, 0, 0, 0.25); }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="15"] {
            background: #d54c34; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="14"] {
            background: #22a0ad; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="13"] {
            background: #22a0ad; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="12"] {
            background: #22a0ad; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="11"] {
            background: #22a0ad; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="10"] {
            background: #1d81a7; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="9"] {
            background: #1d81a7; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="8"] {
            background: #1d81a7; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="7"] {
            background: #1d81a7; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="6"] {
            background: #1d81a7; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="5"] {
            background: #1a61a0; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="4"] {
            background: #1a61a0; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="3"] {
            background: #1a61a0; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="2"] {
            background: #1a61a0; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="1"] {
            background: #1a61a0; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="0"] {
            background: white;
            border: 1px solid var(--color-dark); }
            #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="0"] .time, #__layout #todays-schedule #gridTable .slotRow .timeSlot[total="0"] .spots-left {
              text-shadow: none;
              color: var(--color-dark); }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot.expired {
            cursor: not-allowed;
            background: var(--color-dark);
            box-shadow: none;
            opacity: .7; }
            #__layout #todays-schedule #gridTable .slotRow .timeSlot.expired:hover {
              transform: translateY(0px);
              box-shadow: none; }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot .time {
            color: white;
            font-weight: 700;
            text-shadow: 2px 3px 5px rgba(0, 0, 0, 0.1); }
          #__layout #todays-schedule #gridTable .slotRow .timeSlot .spots-left {
            color: white;
            margin-top: 0;
            font-weight: 500;
            font-size: 1rem;
            text-shadow: 2px 3px 5px rgba(0, 0, 0, 0.1); }
        #__layout #todays-schedule #gridTable .slotRow #slots-container {
          display: none; }
    #__layout #booking .container {
      height: 100vh;
      max-height: 100vh;
      width: 100vw;
      border: 0;
      padding: 3em;
      border-radius: 0;
      border: 0; }
      #__layout #booking .container #close-modal {
        top: .5em;
        right: .5em;
        color: black;
        font-size: 1em; }
      #__layout #booking .container .stepTitle .step-buttons {
        display: none; }
      #__layout #booking .container .steps {
        min-height: 100vh; }
        #__layout #booking .container .steps .step .time-slots {
          grid-template-columns: 1fr 1fr; }
        #__layout #booking .container .steps .step .final-form {
          background: transparent;
          border: 0;
          padding: 0 !important; }
          #__layout #booking .container .steps .step .final-form .row {
            flex-wrap: wrap;
            margin: 0; }
            #__layout #booking .container .steps .step .final-form .row .half {
              /* width:100%; */
              margin-top: 2rem; }
            #__layout #booking .container .steps .step .final-form .row:nth-last-of-type(1) .half {
              width: 100%;
              margin-top: 2rem; }
              #__layout #booking .container .steps .step .final-form .row:nth-last-of-type(1) .half select + label {
                z-index: -1; }
          #__layout #booking .container .steps .step .final-form #final-submit {
            margin-top: 2em; } }

</style>