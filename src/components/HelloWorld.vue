<template>
  <div class="hello">
    <h2> 🌒🌓🌔🌕🌖🌗🌘</h2>
    <ul>
      <li v-for="(name, index) in buttonsNames" :key="name">
        <button @click=toggleList(index)>
          {{ name }}
        </button>
        <ul v-show="isOpenList[index]">
          <li v-for="item in items" :key="item"> {{item}} </li>
        </ul>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {

    buttonsNames: { type: Array, required: true, default: () => [] },

    items: { type: Array, required: true, default: () => [] }

  },

  data() {
    return {
      isOpenList: []
    }
  },

  methods: {

    initOpenList() {
      this.isOpenList = this.buttonsNames.map(() => false);
    },

    created() {
      this.initOpenList();
    },

    toggleList(index) {
      this.isOpenList = this.isOpenList.map((val, i) =>
          i === index ? !val : val
      );
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
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
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  margin: 0 10px;
  padding: 10px 0;
}
li:hover {
  background: #f5f5f5;
}
a {
  color: #42b983;
}
</style>
