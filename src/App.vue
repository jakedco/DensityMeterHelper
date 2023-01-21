<template>
  <div id="app">
    <h1>XState Vue Process Controller MVP POC</h1>
    <h2>(Using Stately's Template...)</h2>
    <button @click="send('Power Flow Meter')" :disabled="state.matches('Flow Meter On')">Power Flow Meter</button>
    <button @click="send('Power Off Flow Meter')" :disabled="state.matches('Flow Meter Off')">Power Off Flow Meter</button>
    <code>
      {{ state.matches("Flow Meter On") ? 'Flow Meter On' : 'Flow Meter Off'}}
    </code>
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
