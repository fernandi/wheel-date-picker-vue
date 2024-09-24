<template>
    <br>
    <br>

<div>Ouverture</div>
<br>
<br>
<br>

  <DatePicker v-bind:value="datePickerValues" v-on:input="updateFutureDate" />
  <br>
  <TimePicker v-bind:value="additionalTime" v-on:input="updateAdditionalTime" />
</template>

<script>
import DatePicker from './components/DatePicker.vue';
import TimePicker from './components/TimePicker.vue';

export default {
  components: {
    TimePicker,
    DatePicker
  },
  data() {
    return {
      additionalTime: {
        year: '0',
        month: '0',
        day: '0',
        hour: '0'
      },
      futureDate: new Date() 
    };
  },
  computed: {
    // Remplit les champs du DatePicker avec les valeurs extraites de futureDate
    datePickerValues() {
      return {
        year: this.futureDate.getFullYear().toString(),
        month: this.futureDate.toLocaleString('default', { month: 'long' }),
        day: this.futureDate.getDate().toString(),
        hour: this.futureDate.getHours().toString()
      };
    }
  },
  methods: {
    updateAdditionalTime(newTime) {
      this.additionalTime = newTime;
      this.futureDate = this.calculatedDate(newTime);
    },
    updateFutureDate(newDateValues) {
      // newDateValues est un objet contenant {year, month, day, hour}
      const months = ['janvier', 'février', 'mars', 'avril', 'mai', 'juin', 'juillet', 'août', 'septembre', 'octobre', 'novembre', 'décembre'];

      // Trouver l'index du mois en utilisant le tableau de correspondance
      const monthIndex = months.indexOf(newDateValues.month.toLowerCase());

      if (monthIndex === -1) {
        console.error('Mois non valide:', newDateValues.month);
      } else {
        console.log('Index du mois:', monthIndex);
      }

      this.futureDate.setFullYear(parseInt(newDateValues.year));
      this.futureDate.setMonth(monthIndex);
      this.futureDate.setDate(parseInt(newDateValues.day));
      this.futureDate.setHours(parseInt(newDateValues.hour));

      this.additionalTime = this.calculatedTime(this.futureDate);

    },
    // Calcul de la future date en fonction du temps supplémentaire
    calculatedDate(additionalTime) {
      const future = new Date(this.pivot || new Date()); // Utiliser pivot ou la date actuelle
      future.setFullYear(future.getFullYear() + parseInt(additionalTime.year));
      future.setMonth(future.getMonth() + parseInt(additionalTime.month));
      future.setDate(future.getDate() + parseInt(additionalTime.day));
      future.setHours(future.getHours() + parseInt(additionalTime.hour));
      return future;
    },

    calculatedTime(futureDate) {
      const currentDate = new Date();
      let [yearDiff, monthDiff, dayDiff, hourDiff] = [
        futureDate.getFullYear() - currentDate.getFullYear(),
        futureDate.getMonth() - currentDate.getMonth(),
        futureDate.getDate() - currentDate.getDate(),
        futureDate.getHours() - currentDate.getHours()
      ];
      console.log(futureDate.getFullYear(), futureDate.getMonth(), futureDate.getDate(), futureDate.getHours());
      console.log("dif : ", yearDiff, monthDiff, dayDiff, hourDiff)

      if (monthDiff < 0) yearDiff--, monthDiff += 12;
      if (dayDiff < 0) monthDiff--, dayDiff += new Date(futureDate.getFullYear(), futureDate.getMonth(), 0).getDate();
      if (hourDiff < 0) dayDiff--, hourDiff += 24;

      return {
        year: yearDiff.toString(),
        month: monthDiff.toString(),
        day: dayDiff.toString(),
        hour: hourDiff.toString()
      };
    } 
    
  }
};
</script>
<style >

@font-face {
  font-family: 'Maria';
  src: url('@/assets/fonts/MariaProEdited.ttf') format('truetype');
  font-weight: normal;
  font-style: normal;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  overscroll-behavior-block: none;
  -webkit-font-smoothing: antialiased;
  user-select: none;

}

html{
  width: 100vw;
  overflow: hidden;
}

body {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100svh;
  background-color: #000;
  font-family: 'Maria', sans-serif;
  width: 100vw;
  overflow: hidden;
  color: white;
  
}

#app{
  display: flex;
  flex-direction: column;
  align-items: center;
}

:root {
  --wh-diameter: 350px;
}

.date {
  color:white;
}
</style>