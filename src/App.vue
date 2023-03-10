<template>
  <div id="app">
    <h2 v-if="state.matches('Flow Meter Off')">
      The Machine is Off
    </h2>
    <h2 v-else>
      The Machine is On
    </h2>
    <!-- Flow Meter -->
    <div class="pa-5">
      <button
        class="next_button"
        v-if="state.matches('Flow Meter Off')"
        @click="send('Power Flow Meter')">
        Power Flow Meter
      </button>
      <button
        class="previous_button"
        v-if="state.matches('Flow Meter On')"
        @click="send('Power Off Flow Meter')"
        :disabled="!state.matches('Flow Meter On')">
        Power Off Flow Meter
      </button>
    </div>

    <!-- Density Meter -->
    <div class="pa-5">
      <button
        class="next_button"
        v-if="state.matches('Flow Meter On')"
        @click="send('Turn on Density Meter')">
        Turn On Density Meter
      </button>
      <button
        class="previous_button"
        v-if="state.matches('Density Meter On')"
        @click="send('Turn Off Density Meter')"
        >
        Turn Off Density Meter
      </button>
    </div>

    <!-- Pump -->
    <div class="pa-5">
      <button
        class="next_button"
        v-if="state.matches('Density Meter On')"
        @click="send('Turn On Pump')">
        Turn On Pump
      </button>
      <button
        class="previous_button"
        v-if="state.matches('Pump is On')"
        @click="send('Turn Off Pump')"
        >
        Turn Off Pump
      </button>
    </div>

    <!-- Carrier Fluid -->
    <div class="pa-5">
      <button
        class="next_button"
        v-if="state.matches('Pump is On')"
        @click="send('Run Carrier Fluid')">
        Run Carrier Fluid
      </button>
    </div>
    <div>
      <button
        class="previous_button"
        v-if="state.matches('Density Meter Ready to Be Calibrated')"
        @click="send('Problem')"
        >
        There was a Calibration Problem
      </button>
    </div>
    <div>
      <button
        class="next_button"
        v-if="state.matches('Density Meter Ready to Be Calibrated')"
        @click="send('Calibrate Density Meter')"
        >
        Calibrate Density Meter
      </button>
    </div>

    <!-- Calibration -->
    <div>
      <button
        class="previous_button"
        v-if="state.matches('Meter Calibration Ready to be Verified')"
        @click="send('Calibration Not Verified')"
        >
        Calibration Not Verified
      </button>
    </div>
    <div>
      <button
        class="next_button"
        v-if="state.matches('Meter Calibration Ready to be Verified')"
        @click="send('Calibration Verified')"
        >
        Calibration Verified
      </button>
    </div>

    <!-- Verified -->
    <div>
      <button
        class="previous_button"
        v-if="state.matches('Density Meter Calibrated')"
        @click="send('Turn Off Density Meter')"
        >
        Turn Off Density Meter
      </button>
    </div>
    <div>
      <button
        class="next_button"
        v-if="state.matches('Density Meter Calibrated')"
        @click="send('Turn On Pump')"
        >
        Turn On Pump
      </button>
    </div>

    <!-- We ready! -->
    <div>
      <button
        class="previous_button"
        v-if="state.matches('Density Meter Ready to Measure SG')"
        @click="send('Turn Off Pump')"
        >
        Turn Off Pump
      </button>
    </div>
    <div>
      <button
        class="next_button"
        v-if="state.matches('Density Meter Ready to Measure SG')"
        @click="send('Add Solids')"
        >
        Add Solids
      </button>
    </div>

    <!-- Reading SG -->
    <div>
      <button
        class="previous_button"
        v-if="state.matches('Read SG')"
        @click="send('Turn Off Pump')"
        >
        Turn Off Pump
      </button>
    </div>
    <div>
      <button
        class="next_button"
        v-if="state.matches('Read SG')"
        @click="send('Add or Remove Solids as Needed')"
        >
        Add or Remove Solids
      </button>
    </div>

    <!-- Pump Turned Off -->
    <div>
      <button
        class="next_button"
        v-if="state.matches('Take a Break / End Shift')"
        @click="send('Resume Shift')"
        >
        Resume Shift
      </button>
    </div>
    <div>
      <button
        class="previous_button"
        v-if="state.matches('Take a Break / End Shift')"
        @click="send('End Shift')"
        >
        End Shift
      </button>
    </div>

    <!-- Shift Ended -->
    <div>
      <button
        class="next_button"
        v-if="state.matches('Prepare to Export/Download Data')"
        @click="send('Export Data')"
        >
        Export Data
      </button>
    </div>

    <!-- Data Saved -->
    <div>
      <button
        class="next_button"
        v-if="state.matches('Data Saved')"
        @click="send('Confirm Data Saved')"
        >
        CONFIRM DATA IS SAVED
      </button>
    </div>

    <div>
      <button
        v-if="!historyExpanded"
        @click="historyExpanded = true"
        class="help_button">
        See History
      </button>
      <button
        v-if="historyExpanded"
        @click="historyExpanded = false"
        class="help_button">
        Hide History
      </button>
      <div v-if="historyExpanded">
        <h3 v-for="(element, index) in history" :key="index">
          {{ history[history.length + - 1 - index] }}
        </h3>
      </div>
    </div>
  </div>
</template>

<script>
import { createMachine } from "xstate";
import { useMachine } from "@xstate/vue";

const MeterMachine = createMachine({
  id: "Flow Meter",
  initial: "Flow Meter Off",
  states: {
    "Flow Meter Off": {
      on: {
        "Power Flow Meter": {
          target: "Flow Meter On",
        },
      },
    },
    "Flow Meter On": {
      on: {
        "Turn on Density Meter": {
          target: "Density Meter On",
        },
        "Power Off Flow Meter": {
          target: "Flow Meter Off",
        },
      },
    },
    "Density Meter On": {
      on: {
        "Turn On Pump": {
          target: "Pump is On",
        },
        "Turn Off Density Meter": {
          target: "Flow Meter On",
        },
      },
    },
    "Meter Calibration Ready to be Verified": {
      on: {
        "Calibration Not Verified": {
          target: "Pump is On",
        },
        "Calibration Verified": {
          target: "Density Meter Calibrated",
        },
      },
    },
    "Density Meter Ready to Measure SG": {
      on: {
        "Add Solids": {
          target: "Read SG",
        },
        "Turn Off Pump": {
          target: "Density Meter Calibrated",
        },
      },
    },
    "Take a Break / End Shift": {
      on: {
        "Resume Shift": {
          target: "Read SG",
        },
        "End Shift": {
          target: "Prepare to Export/Download Data",
        },
      },
    },
    "Read SG": {
      on: {
        "Add or Remove Solids as Needed": {},
        "Turn Off Pump": {
          target: "Take a Break / End Shift",
        },
      },
    },
    "Density Meter Ready to Be Calibrated": {
      on: {
        "Calibrate Density Meter": {
          target: "Meter Calibration Ready to be Verified",
        },
        Problem: {
          target: "Density Meter On",
        },
      },
    },
    "Pump is On": {
      on: {
        "Run Carrier Fluid": {
          target: "Density Meter Ready to Be Calibrated",
        },
        "Turn Off Pump": {
          target: "Density Meter On",
        },
      },
    },
    "Prepare to Export/Download Data": {
      on: {
        "Export Data": {
          target: "Data Saved",
        },
      },
    },
    "Data Saved": {
      on: {
        "Confirm Data Saved": {
          target: "Density Meter Ready to Measure SG",
        },
      },
    },
    "Density Meter Calibrated": {
      on: {
        "Turn On Pump": {
          target: "Density Meter Ready to Measure SG",
        },
        "Turn Off Density Meter": {
          target: "Flow Meter On",
        },
      },
    },
  },
  context: {},
  predictableActionArguments: true,
  preserveActionOrder: true,
});


export default {
  name: "App",
  data() {
    return {
      historyExpanded: false,
      history: [],
      stack: []
    }
  },
  setup() {
    const { state, send } = useMachine(MeterMachine);
    return { state, send };
  },
  watch: {
    state() {
      this.history.push(this.state.event.type)
    }
  }
};
</script>

<style>

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

button {
  background-color: #4CAF50;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  border-radius: 10px;
  cursor: pointer;
}

.next_button {
  background-color: #08a64f;
  color: #fff;
}

.previous_button {
  background-color: #e12727;
  color: #fff;
}

.help_button {
  background-color: #b5b5b5;
  margin-top: 100px;
  color: rgb(0, 0, 0);
}

</style>
