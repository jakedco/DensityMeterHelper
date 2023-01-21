<template>
  <div id="app">
    <h1>XState Vue Process Controller MVP POC</h1>
    <h2>(Using Stately's Template...)</h2>

    <!-- Flow Meter -->
    <div class="pa-5">
      <button
        v-if="state.matches('Flow Meter Off')"
        @click="send('Power Flow Meter')">
        Power Flow Meter
      </button>
      <button
        v-if="state.matches('Flow Meter On')"
        @click="send('Power Off Flow Meter')"
        :disabled="!state.matches('Flow Meter On')">
        Power off Flow Meter
      </button>
    </div>

    <!-- Density Meter -->
    <div class="pa-5">
      <button
        v-if="state.matches('Flow Meter On')"
        @click="send('Turn on Density Meter')">
        Turn on Density Meter
      </button>
      <button
        v-if="state.matches('Density Meter On')"
        @click="send('Turn Off Density Meter')"
        >
        Turn off Density Meter
      </button>
    </div>

    <!-- Pump -->
    <div class="pa-5">
      <button
        v-if="state.matches('Density Meter On')"
        @click="send('Turn On Pump')">
        Turn on Pump
      </button>
      <button
        v-if="state.matches('Pump is On')"
        @click="send('Turn Off Pump')"
        >
        Turn off Pump
      </button>
    </div>

    <!-- Carrier Fluid -->
    <div class="pa-5">
      <button
        v-if="state.matches('Pump is On')"
        @click="send('Run Carrier Fluid')">
        Run Carrier Fluid
      </button>
    </div>
    <div>
      <button
        v-if="state.matches('Density Meter Ready to Be Calibrated')"
        @click="send('Calibrate Density Meter')"
        >
        Calibrate Density Meter
      </button>
      <button
        v-if="state.matches('Density Meter Ready to Be Calibrated')"
        @click="send('Problem')"
        >
        Problem
      </button>
    </div>

    <!-- Calibration -->
    <div>
      <button
        v-if="state.matches('Meter Calibration Ready to be Verified')"
        @click="send('Calibration Verified')"
        >
        Calibration Verified
      </button>
      <button
        v-if="state.matches('Meter Calibration Ready to be Verified')"
        @click="send('Calibration Not Verified')"
        >
        Calibration Not Verified
      </button>
    </div>

    <!-- Verified -->
    <div>
      <button
        v-if="state.matches('Density Meter Calibrated')"
        @click="send('Turn Off Density Meter')"
        >
        Turn Off Density Meter
      </button>
      <button
        v-if="state.matches('Density Meter Calibrated')"
        @click="send('Turn On Pump')"
        >
        Turn On Pump
      </button>
    </div>

    <!-- We ready! -->
    <div>
      <button
        v-if="state.matches('Density Meter Ready to Measure SG')"
        @click="send('Add Solids')"
        >
        Add Solids
      </button>
      <button
        v-if="state.matches('Density Meter Ready to Measure SG')"
        @click="send('Turn Off Pump')"
        >
        Turn Off Pump
      </button>
    </div>

    <!-- Reading SG -->
    <div>
      <button
        v-if="state.matches('Read SG')"
        @click="send('Add or Remove Solids as Needed')"
        >
        Add or Remove Solids as Needed
      </button>
      <button
        v-if="state.matches('Read SG')"
        @click="send('Turn Off Pump')"
        >
        Turn Off Pump
      </button>
    </div>

    <!-- Pump Turned Off -->
    <div>
      <button
        v-if="state.matches('Take a Break / End Shift')"
        @click="send('Resume Shift')"
        >
        Resume Shift
      </button>
      <button
        v-if="state.matches('Take a Break / End Shift')"
        @click="send('End Shift')"
        >
        End Shift
      </button>
    </div>

    <!-- Shift Ended -->
    <div>
      <button
        v-if="state.matches('Prepare to Export/Download Data')"
        @click="send('Export Data')"
        >
        Export Data
      </button>
    </div>

    <!-- Data Saved -->
    <div>
      <button
        v-if="state.matches('Data Saved')"
        @click="send('Confirm Data Saved')"
        >
        CONFIRM DATA SAVED
      </button>
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
  setup() {
    const { state, send } = useMachine(MeterMachine);
    return { state, send };
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
</style>
