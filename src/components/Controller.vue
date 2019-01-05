<template>
  <q-card :color="isActive? 'grey-2' : 'white'" text-color="black">
    <q-card-title>Controller</q-card-title>
    <q-card-separator/>
    <q-card-main>
      <q-table :data="ringTableData" :columns="ringTableColumns" dense hide-bottom></q-table>
      <q-table
        :data="muinsTableData"
        :columns="muinsTableColumns"
        dense
        hide-bottom
        class="q-mt-md"
      ></q-table>
      <signals :signals="cBus" class="q-mt-md"></signals>
      <signals :signals="conSignals" class="q-mt-md"></signals>
    </q-card-main>
    <q-card-separator/>
    <q-card-actions align="around">
      <q-btn
        :label="conSignals.CLK ? 'Tock' : 'Tick'"
        @click="$emit('halfCycle')"
        :disable="instruction === 'HLT'"
        color="primary"
        icon="access_time"
      ></q-btn>
      <q-btn label="Play" @click="play" :disable="halted" color="secondary" icon="play_arrow"></q-btn>
      <q-btn label="Pause" @click="play" :disable="halted" color="secondary" icon="pause"></q-btn>
      <q-btn label="CLR" icon="replay"></q-btn>
    </q-card-actions>
  </q-card>
</template>

<script>
import { convertWordsToBits, getInstruction } from "./bitFunctions";
import Signals from "./Signals";
import Bits from "./Bits";
export default {
  name: "Controller",
  props: ["cBus", "conSignals", "irBits"],
  components: { Bits, Signals },
  data() {
    return {
      timer: undefined,
      ringBits: [0, 0, 0, 0, 0, 0],
      TStateStr: [
        "Address",
        "Increment",
        "Memory",
        "Exec1",
        "Exec2",
        "Exec3",
        "Undef"
      ],
      microInstructionsROM: {
        AIM: ["5E3", "BE3", "263"],
        LDA: ["1A3", "2C3", "3E3"],
        ADD: ["1A3", "2E1", "3C7"],
        SUB: ["1A3", "2E1", "3CF"],
        OUT: ["3F2", "3E3", "3E3"],
        HLT: ["3E3", "3E3", "3E3"],
        NOP: ["3E3"]
      },
      microInstructionDescr: {
        "5E3": "Push PC to MAR via Bus",
        BE3: "Increment PC",
        "263": "Push RAM at MAR to IR via Bus",
        "1A3": "Push Add in IR to MAR via Bus",
        "2C3": "Push RAM at MAR to ACC via Bus",
        "3E3": "NOP",
        "2E1": "Push RAM at MAR to B via Bus",
        "3C7": "ACC + B and push result to Bus",
        "3CF": "ACC - B and push result to Bus",
        "3F2": "Push ACC to Out"
      }
    };
  },
  computed: {
    TState: function() {
      let msbi = this.ringBits.indexOf(1);
      return 6 - msbi;
    },
    TStateDesc: function() {
      return this.TStateStr[this.TState - 1];
    },
    halted: function() {
      return getInstruction(this.irBits) === "HLT";
    },
    instruction: function() {
      if (this.TState < 4) return "AIM";
      else return getInstruction(this.irBits);
    },
    microInstruction: function() {
      if (this.TState < 4)
        return this.microInstructionsROM.AIM[this.TState - 1];
      if (this.TState < 7)
        return this.microInstructionsROM[this.instruction][this.TState - 4];
      if (this.TState === 7) return this.microInstructionsROM.NOP[0];
      throw new Error("Invalid TState");
    },
    isActive: function() {
      return true;
    },
    ringTableColumns: function() {
      return [
        { name: "tstate", label: "", field: "tstate" },
        { name: "bits", label: "Bits", field: "bits" },
        { name: "t", label: "T", field: "t" },
        { name: "descr", label: "Descr", field: "descr" }
      ];
    },
    ringTableData: function() {
      return [
        {
          tstate: "TState",
          bits: this.ringBits.join(""),
          t: this.TState,
          descr: this.TStateDesc
        }
      ];
    },
    muinsTableColumns: function() {
      return [
        { name: "ins", label: "Ins", field: "ins" },
        { name: "muins", label: "MuIns", field: "muins" },
        { name: "descr", label: "Descr", field: "descr" }
      ];
    },
    muinsTableData: function() {
      return [
        {
          ins: this.instruction,
          muins: this.microInstruction,
          descr: this.microInstructionDescr[this.microInstruction]
        }
      ];
    }
  },
  watch: {
    "conSignals.CLK": function(newCLK, oldCLK) {
      if (newCLK === 0 && oldCLK === 0) throw new Error("CLK same");
      if (newCLK === 0) {
        this.ringRotate();
      }
    },
    microInstruction: function(newI, oldI) {
      console.log("Controller Watch microInstruction", newI);
      let cBusBits = convertWordsToBits(this.microInstruction)
        .split("")
        .map(x => parseInt(x));
      let cBusNew = {
        Cp: cBusBits[0],
        Ep: cBusBits[1],
        Lm: cBusBits[2],
        CE: cBusBits[3],
        Li: cBusBits[4],
        Ei: cBusBits[5],
        La: cBusBits[6],
        Ea: cBusBits[7],
        Su: cBusBits[8],
        Eu: cBusBits[9],
        Lb: cBusBits[10],
        Lo: cBusBits[11]
      };
      this.$emit("setCBus", cBusNew);
    }
  },
  methods: {
    halfCycle() {
      if (this.instruction !== "HLT") this.$emit("halfCycle");
    },
    play() {
      this.timer = setInterval(() => {
        if (this.instruction === "HLT") {
          clearInterval(this.timer);
        } else this.halfCycle();
      }, 500);
    },
    pause() {
      clearInterval(this.timer);
    },
    ringRotate: function() {
      let msbi = this.ringBits.indexOf(1);
      if (msbi === -1) {
        // first rotation
        this.ringBits.splice(5, 1, 1);
      } else {
        this.ringBits.splice(msbi, 1, 0);
        if (msbi === 0) {
          this.ringBits.splice(5, 1, 1);
        } else {
          this.ringBits.splice(msbi - 1, 1, 1);
        }
      }
    }
  }
};
</script>

<style>
</style>
