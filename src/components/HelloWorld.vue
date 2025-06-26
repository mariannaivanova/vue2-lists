<template>
  <div class="hello">
    <ul>
      <li v-for="(name, index) in buttonsNames" :key="name">
        <div class="button-controls">
          <input
              class="button-input"
              type="checkbox"
              :id="`checkbox-${index}`"
              v-model="checkedButtons[index]"
              @change="toggleAllItems(index)">
          <button @click="toggleList(index)">
            {{ name }}
          </button>
          <transition name="fade">
            <div v-if="isOpenList[index]" class="wrapper">
              <ul class="nested-list">
                <li v-for="(item, itemIndex) in items" :key="item" class="item-container">
                  <div class="item-controls">
                    <input
                        type="checkbox"
                        :id="`checkbox-${index}-${itemIndex}`"
                        v-model="checkedItems[index][itemIndex]">
                    <label :for="`checkbox-${index}-${itemIndex}`">{{ item }}</label>
                  </div>
                  <transition name="fade">
                    <div v-if="checkedItems[index][itemIndex]"
                         class="item-options">
                      <input
                          type="number"
                          v-model.number="squareCounts[index][itemIndex]"
                          placeholder="Кол-во квадратов"
                          min="1"
                          max="10"
                          class="number-input"
                          @change="updateSquareCount(index, itemIndex)"
                      >
                      <div class="color-panel">
                        <input
                            type="color"
                            v-model="itemColors[index][itemIndex]"
                            class="native-color-picker"
                            ref="colorPicker"
                        >
                      </div>
                    </div>
                  </transition>
                </li>
              </ul>
              <div class="square-box">
                <div v-for="(row, rowIndex) in commonSquares[index]" :key="rowIndex" class="common-row">
                  <div
                      v-for="(color, colorIndex) in row"
                      :key="colorIndex"
                      class="common-square"
                      :style="{ backgroundColor: color }"
                  >
                  </div>
                </div>
              </div>
              <button
                  class="shuffle-button"
                  @click="mixSquares(index)">
                {{ isMixed[index] ? 'Unshuffle' : 'Shuffle' }}
              </button>
            </div>
          </transition>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',

  props: {
    buttonsNames: {
      type: Array,
      required: true,
      default: () => []
    },

    items: {
      type: Array,
      required: true,
      default: () => []
    }
  },

  data() {
    return {
      isOpenList: [],
      checkedItems: [],
      checkedButtons: [],
      itemColors: [],
      itemSquares: [],
      squareCounts: [],
      commonSquares: [],
      isMixed: [],
      originalSquares: [],
    }
  },

  created() {
    this.initOpenList();
    this.initCheckedItems();
    this.initCheckedButtons();
    this.initItemColors();
    this.initSquareCounts();
    this.initCommonSquares();
    this.initIsMixed();
    this.initOriginalSquares();
  },

  watch: {
    itemColors: {
      deep: true,
      handler(newVal) {
        newVal.forEach((list, listIndex) => {
          list.forEach((color, itemIndex) => {
            if (color && this.checkedItems[listIndex][itemIndex]) {
              const count = this.squareCounts[listIndex][itemIndex] || 3;
              this.$set(
                  this.commonSquares[listIndex],
                  itemIndex,
                  Array(count).fill(color)
              );
            }
          });
        });
      }
    }
  },

  methods: {
    initOpenList() {
      this.isOpenList = this.buttonsNames.map(() => false);
    },

    initCheckedButtons() {
      this.checkedButtons = this.buttonsNames.map(() => false);
    },

    initCheckedItems() {
      this.checkedItems = this.buttonsNames.map(() =>
          this.items.map(() => false)
      );
    },

    initItemColors() {
      this.itemColors = this.buttonsNames.map(() =>
          this.items.map(() => '#ffffff')
      );
    },

    initIsMixed() {
      this.isMixed = this.buttonsNames.map(() => false);
    },

    initSquareCounts() {
      this.squareCounts = this.buttonsNames.map(() =>
          this.items.map(() => 3)
      );
    },

    initCommonSquares() {
      this.commonSquares = this.buttonsNames.map((_, listIndex) =>
          this.items.map((_, itemIndex) =>
              Array(this.squareCounts[listIndex][itemIndex] || 3).fill('#ffffff')
          )
      );
      this.buttonsNames.forEach((_, index) => {
        this.updateCommonSquares(index);
      });
    },

    initOriginalSquares() {
      this.originalSquares = [...this.commonSquares];
    },

    toggleList(index) {
      this.isOpenList = this.isOpenList.map((val, i) =>
          i === index ? !val : val
      );
    },

    toggleAllItems(index) {
      const isChecked = this.checkedButtons[index];
      this.checkedItems[index] = this.items.map(() => isChecked);
    },

    updateSquareCount(index, itemIndex) {
      this.squareCounts[index][itemIndex] = Math.min(
          10,
          Math.max(1, this.squareCounts[index][itemIndex] || 1)
      );
      this.updateCommonSquares(index);
    },

    updateCommonSquares(index) {
      this.$nextTick(() => {
        const newSquares = this.items.map((_, itemIndex) => {
          if (!this.checkedItems[index][itemIndex]) {
            return Array(this.squareCounts[index][itemIndex] || 3).fill('#ffffff');
          }
          return Array(this.squareCounts[index][itemIndex] || 3)
              .fill(this.itemColors[index][itemIndex]);
        });
        this.$set(this.commonSquares, index, newSquares);
        this.$set(this.originalSquares, index, newSquares);
      });
    },

    mixSquares(index) {
      if (!this.isMixed[index]) {
        if (!this.originalSquares) this.originalSquares = [...this.commonSquares];
        const mixed = this.commonSquares[index].flat().sort(() => Math.random() - 0.5);
        this.$set(this.commonSquares, index, [mixed]);
      } else {
        this.$set(this.commonSquares, index, [...this.originalSquares[index]]);
      }
      this.$set(this.isMixed, index, !this.isMixed[index]);
    }
  },
}
</script>

<style scoped>
.hello {
  position: relative;
  display: inline-block;
}

button {
  padding: 8px 16px;
  background: #f882ff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-bottom: 5px;
  box-shadow: 0 0 5px rgba(171, 171, 171, 0.8);
}

button:active {
  box-shadow: inset 2px 2px 3px rgba(171, 171, 171, 0.8);
}

button:hover,
button:focus-visible {
  background-color: #ef5af8;
}

ul {
  list-style-type: none;
  padding: 0;
}
.wrapper {
  transition: all 0.5s ease;
}

.item-options {
  transition: all 0.5s ease;
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding: 8px;
  background: #f5f5f5;
  border-radius: 4px;
}

.fade-enter-active,
.fade-leave-active {
  max-height: 500px;
  opacity: 1;
  transition: max-height 0.5s ease, opacity 0.5s ease;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.5s ease, opacity 0.5s ease;
}

li {
  margin: 5px 0;
  padding: 5px;
}

.item-container {
  display: flex;
  flex-direction: column;
  gap: 8px;
  padding: 8px;
  border: 1px solid #eee;
  border-radius: 4px;
}

.item-controls {
  display: flex;
  align-items: center;
  gap: 8px;
}

.number-input {
  padding: 4px;
  width: 80px;
}

.color-panel {
  display: flex;
  gap: 15px;
  align-items: center;
}

.square-box {
  width: 300px;
  height: 100px;
  border: 1px solid #ccc;
}

.common-row {
  display: flex;
  gap: 5px;
  margin: 10px;
  flex-wrap: wrap;
  align-items: center;
}

.common-square {
  width: 20px;
  height: 20px;
  display: flex;
  border: 1px solid #ccc;
  border-radius: 4px;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.color-slider label {
  width: 20px;
  text-align: center;
}

.color-slider input[type="range"] {
  flex-grow: 1;
}

.color-slider span {
  width: 30px;
  text-align: right;
}

.native-color-picker {
  width: 100%;
  height: 30px;
  cursor: pointer;
}

.shuffle-button {
  margin: 10px;
}

</style>