<template>
  <div class="hello">
    <h2> 🌒🌓🌔🌕🌖🌗🌘</h2>
    <ul>
      <li v-for="(name, index) in buttonsNames" :key="name">
        <button @click="toggleList(index)">
          {{ name }}
        </button>
        <ul v-show="isOpenList[index]" class="nested-list">
          <li v-for="(item, itemIndex) in items" :key="item" class="item-container">
            <div class="item-controls">
              <input
                  type="checkbox"
                  :id="`checkbox-${index}-${itemIndex}`"
                  v-model="checkedItems[index][itemIndex]"
                  @change="handleCheckboxChange(index, itemIndex)"
              >
              <label :for="`checkbox-${index}-${itemIndex}`">{{ item }}</label>
            </div>
            <div v-if="checkedItems[index][itemIndex]" class="item-options">
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
                    @input="updateCommonSquares(index)"
                >
              </div>
            </div>
          </li>
        </ul>
        <div v-show="isOpenList[index]" class="square-box">
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
      itemColors: [],
      itemSquares: [],
      squareCounts: [],
      commonSquares: []
    }
  },

  created() {
    this.initOpenList();
    this.initCheckedItems();
    this.initItemColors();
    this.initSquareCounts();
    this.initCommonSquares();
  },

  watch: {
    itemColors: {
      deep: true,
      handler(newVal) {
        newVal.forEach((list, listIndex) => {
          list.forEach((color, itemIndex) => {
            if (color && this.checkedItems[listIndex][itemIndex]) {
              this.$set(this.commonSquares[listIndex][itemIndex],
                  0,
                  this.itemColors[listIndex][itemIndex]
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
    toggleList(index) {
      this.isOpenList = this.isOpenList.map((val, i) =>
          i === index ? !val : val
      );
    },
    handleCheckboxChange(index, itemIndex) {
      if (!this.checkedItems[index][itemIndex]) {
        this.itemColors[index][itemIndex] = '#ffffff';
      }
      this.updateCommonSquares(index);
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
      });
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
  background: #ffc617;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-bottom: 5px;
}

ul {
  list-style-type: none;
  padding: 0;
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

.item-options {
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding: 8px;
  background: #f5f5f5;
  border-radius: 4px;
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
  transition: background-color 0.3s;
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
</style>