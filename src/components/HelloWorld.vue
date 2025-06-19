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
                  v-model.number="itemNumbers[index][itemIndex]"
                  placeholder="Введите число"
                  min="0"
                  class="number-input"
              >

              <div class="color-panel">
                <div
                    class="color-preview"
                    :style="{ backgroundColor: itemColors[index][itemIndex] }"
                    @click="openColorPicker()"
                ></div>
                <input
                    type="color"
                    v-model="itemColors[index][itemIndex]"
                    class="native-color-picker"
                    ref="colorPicker"
                    @input="handleColorInput(index, itemIndex)"
                >
              </div>
            </div>
          </li>
        </ul>
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
      itemNumbers: [],
      itemColors: [],
      colorComponents: []
    }
  },
  created() {
    this.initOpenList();
    this.initCheckedItems();
    this.initItemNumbers();
    this.initItemColors();
    this.initColorComponents();
  },
  watch: {
    itemColors: {
      deep: true,
      handler(newVal) {
        // При изменении цвета через color-picker обновляем RGB компоненты
        newVal.forEach((list, i) => {
          list.forEach((color, j) => {
            if (color && color.startsWith('#')) {
              this.colorComponents[i][j] = this.parseColor(color);
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
    initItemNumbers() {
      this.itemNumbers = this.buttonsNames.map(() =>
          this.items.map(() => null)
      );
    },
    initItemColors() {
      this.itemColors = this.buttonsNames.map(() =>
          this.items.map(() => '#ffffff')
      );
    },
    initColorComponents() {
      this.colorComponents = this.buttonsNames.map(() =>
          this.items.map(() => ({ r: 255, g: 255, b: 255 }))
      );
    },
    toggleList(index) {
      this.isOpenList = this.isOpenList.map((val, i) =>
          i === index ? !val : val
      );
    },
    handleCheckboxChange(index, itemIndex) {
      if (!this.checkedItems[index][itemIndex]) {
        this.itemNumbers[index][itemIndex] = null;
        this.itemColors[index][itemIndex] = '#ffffff';
        this.colorComponents[index][itemIndex] = { r: 255, g: 255, b: 255 };
      }
    },
    openColorPicker() {
      this.$refs.colorPicker.click();
    },
    handleColorInput(index, itemIndex) {
      const hexColor = this.itemColors[index][itemIndex];
      this.colorComponents[index][itemIndex] = this.parseColor(hexColor);
    },
    parseColor(hexColor) {
      // Парсим hex-цвет в RGB компоненты
      const r = parseInt(hexColor.slice(1, 3), 16);
      const g = parseInt(hexColor.slice(3, 5), 16);
      const b = parseInt(hexColor.slice(5, 7), 16);
      return { r, g, b };
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
  background: #42b983;
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

.nested-list {
  margin-left: 20px;
  border-left: 2px solid #42b983;
  padding-left: 10px;
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

.color-preview {
  width: 50px;
  height: 50px;
  border-radius: 4px;
  border: 1px solid #ccc;
}

.color-controls {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.color-slider {
  display: flex;
  align-items: center;
  gap: 8px;
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