<template>
  <div class="time-container">
    <div class="time-line">
        <div class="center-label">Le</div>

        <WheelPicker v-bind:value="localTime.day" v-on:input="updateDay" :totalCrans="50" :items="days" />
        <WheelPicker v-bind:value="localTime.month" v-on:input="updateMonth" :totalCrans="38" :items="months" />
        <WheelPicker v-bind:value="localTime.year" v-on:input="updateYear" :totalCrans="50" :items="years" />
<div class="center-label">à</div>
        <WheelPicker v-bind:value="localTime.hour" v-on:input="updateHour" :totalCrans="50" :items="hours" />
        <div class="center-label">heures</div>
    </div>
  </div>
</template>

<script>
import WheelPicker from './WheelPicker.vue'; // Import du composant WheelPicker

export default {
  name: 'DatePicker',
  components: {
    WheelPicker
  },
  props: {
    value: {
      type: Object,
      required: true,
      default: () => ({
        year: '0',
        month: '0',
        day: '0',
        hour: '0'
      })
    }
  },
  data() {
    return {
      // Local copy of props to allow bidirectional data binding
      localTime: { ...this.value },
      years: this.generateYears(),
      months: Array.from({ length: 12 }, (_, i) => new Date(0, i).toLocaleString('fr', { month: 'long' })),
      days: Array.from({ length: 31 }, (_, i) => (i+1).toString()),   // 0 à 30 jours
      hours: Array.from({ length: 24 }, (_, i) => i.toString()),  // 0 à 23 heures
    };
  },
  watch: {
    // Watch for changes in the props and update local data accordingly
    value: {
      handler(newValue) {
        this.localTime = { ...newValue };
      },
      deep: true
    }
  },
  methods: {
    generateYears() {
      const currentYear = new Date().getFullYear();
      return Array.from({ length: 10 }, (_, i) => (currentYear + i).toString()); // Années à partir de l'année actuelle
    },
    updateYear(value) {
      this.localTime.year = value;
      this.updateTime();
    },
    updateMonth(value) {
      this.localTime.month = value;
      this.updateTime();
    },
    updateDay(value) {
      this.localTime.day = value;
      this.updateTime();
    },
    updateHour(value) {
      this.localTime.hour = value;
      this.updateTime();
    },
    updateTime() {
      // Emettre les valeurs modifiées vers le parent via 'v-model' (ou 'input')
      console.log(this.localTime.year,this.localTime.month,this.localTime.day,this.localTime.hour);
      this.$emit('input', { ...this.localTime });
    }
  }
};
</script>

<style scoped>
.time-container {
  justify-content: center;
  height: 30svh;
  width: 100vw;
  overflow: visible;
  display: flex;
  background-color: rgb(0, 0, 0);
}

.time-line div {
  /*display: flex;*/
  align-items: center;
  /*flex-basis: 25%;*/
}

.time-line {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 300px;
}

.center-label {
  font-size: 1rem;
  color: white;
}

.date {
  color: white;
}
</style>