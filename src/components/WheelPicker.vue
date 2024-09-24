<template>
  <div class="wheel-container" :style="{ width: `${maxItemLength}ch` }">
    <div class="wheel" :style="{ transform: `translate(0, calc( .5rem - 50%)) rotate(${angle}deg)` }">
      <div v-for="(item, index) in items" 
           :key="index" 
           :class="{ 'selected-item': isSelected(index) }"
           class="wheel-item"
           :style="rotationStyle(index)"
           @touchstart="onTouchStart"
           @touchmove="onTouchMove">
        {{ item }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    totalCrans: {
      type: Number,
      required: true,
    },
    items: {
      type: Array,
      required: true,
    },
    value: {
      type: [Number, String],
      default: null
    }
  },
  data() {
    return {
      angle: 360, // Angle initialisé ici, non plus dans computed
      startY: 0, // Position initiale pour les interactions tactiles
      isScrolling: null, // Timer pour gérer le snap lors de l'arrêt du défilement
      isRotating: false // Variable pour indiquer si la roue est en train de tourner
    };
  },
  computed: {
    angleStep() {
      return 360 / this.totalCrans;
    },
    selectedItemIndex() {
      return Math.round((360 - this.angle) / this.angleStep) % this.items.length;
    },
    minAngle() {
      return 360 - (this.items.length - 1) * this.angleStep;
    },
    maxItemLength() {
      // Calculer la longueur du string le plus long dans les items
      return Math.max(2, ...this.items.map(item => item.length));
    }
  },
  watch: {
    value(newValue) {
      console.log('value has changed to', newValue);
      // Si la valeur passée par v-model change, ajuster l'angle pour pointer vers cet item
      this.rotateToItem(newValue);
    }
  },
  methods: {
    rotationStyle(index) {
      const rotation = index * this.angleStep;
      const distanceFromCenter = Math.abs(360 - this.angle - rotation);
      const opacity = distanceFromCenter < 40 ? 1 : 0;
      return {
        transform: `rotate(${rotation}deg)`,
        opacity: `${opacity}`,
        color: this.isRotating ? '#008000' : 'rgb(35 32 32)', // Change color when rotating
        width: `${this.maxItemLength}ch`
      };
    },
    rotateWheel(delta) {
      this.isRotating = true; // Indique que la roue est en rotation

      const newAngle = this.angle + delta;
      this.angle = newAngle;
      if (newAngle < (this.minAngle - this.angleStep)) {
        this.angle = this.minAngle - this.angleStep;
      } else if (newAngle > 360 + this.angleStep) {
        this.angle = 360 + this.angleStep;
      } else {
        this.angle = newAngle;
      }
    },
    rotateToItem(item) {
      // Trouver l'index de l'élément dans items et ajuster l'angle
      

      if (item === null || item === undefined) {
        console.warn('Invalid item:', item); // Affiche un avertissement si l'item est nul
        return; // Ne fais rien si l'item est invalide
      }


      const index = this.items.indexOf(item.toString());
      if (index !== -1) {
        this.angle = 360 - index * this.angleStep;
      }
    },
    snapToNearestStep() {
      const step = this.angleStep;

      if (this.angle < this.minAngle) {
        this.angle = this.minAngle;
      } else if (this.angle > 360) {
        this.angle = 360;
      }
      this.angle = Math.round(this.angle / step) * step; 
      this.isRotating = false; // Fin de la rotation

      this.$emit('input', this.items[this.selectedItemIndex]); // Émettre l'item sélectionné
    },
    isSelected(index) {
      return index ===  Math.round((360 - this.angle) / this.angleStep);
    },
    onTouchStart(event) {
      this.startY = event.touches[0].clientY;
    },
    onTouchMove(event) {
      const currentY = event.touches[0].clientY;
      const deltaY = currentY - this.startY;
      this.rotateWheel(-deltaY * 0.2);
      this.startY = currentY;
      clearTimeout(this.isScrolling);
      this.isScrolling = setTimeout(this.snapToNearestStep, 200);
    }
  },mounted(){
    console.log('value initiale', this.value)
    this.rotateToItem(this.value)

  } 
};
</script>

<style scoped>
.wheel-container {
  display: inline-block;
  width: 2ch;
  height: 1rem;
  position: relative;
  overflow: visible;
  margin: 5px;
  border: white solid .5px;
border-radius: 10px;
}

.wheel {
  position: absolute;
  width: var(--wh-diameter);
  height: var(--wh-diameter);
  display: flex;
  align-items: center;
  transition: transform 0.3s ease-out;
}

.wheel-item {
  position: absolute;
  display: block;
  width: 2ch;
  height: 1rem;
  font-size: 1rem;
  line-height: 1rem;
  text-align: center;
  color: rgb(35 32 32);
  transform-origin: calc(var(--wh-diameter) / 2) center;
  touch-action: none;
  z-index: 1;
  transition: opacity 0.3s ease-out, color 0.3s ease-out;
  
}

.selected-item {
  color: white !important;
}

.wheel-item::before {
  content: '';
  position: absolute;
  top: -10px;
  bottom: -10px;
  left: -10px;
  right: -10px;
  z-index: -1;
}
</style>