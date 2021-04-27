<template>
  <div class="container">
    <input class="input-text" type="text" v-model="text" />
    <p>Привет {{computedText}}</p>
  </div>
  <div class="container">
    <div
      class="breedItem"
      v-for="breed in breeds"
      :key="breed.name"
      :style="{backgroundColor: breed.subBreeds.length ? 'red' : 'white'}"
    >{{breed.name}}</div>
  </div>
  <div class="container">
    <div class="container-buttons">
      <button v-on:click="addRandom">Добавить случайную породу</button>
      <button v-on:click="swapRandom" :disabled="randomBreeds.length < 2">Перемешать</button>
      <button v-on:click="removeRandom" :disabled="randomBreeds.length < 1">Удалить случайную породу</button>
    </div>
    <div
        class="breedItem"
        v-for="(breed, index) in randomBreeds"
        :key="index"
    >{{breed.name}}</div>
  </div>
  <div style="width: 100%" class="container container-lists">
    <div class="item">
      <List
          v-bind:data="breeds"
          v-bind:on-click-item="selectBreed"
          v-bind:checked="(name) => selectedBreeds.find((item) => item.name === name)"
          v-bind:color-checked="{backgroundColor: 'blue', color: 'white'}"
      />
    </div>
    <div class="item">
      <List
          v-bind:data="selectedBreeds"
          v-bind:on-click-item="unselectBreed"
      />
    </div>
  </div>
</template>

<script>
import List from './List';

const parser = (data) => Object.entries(data).map((item) => ({name: item[0], subBreeds: item[1]}));

const random = (max) => Math.floor(Math.random() * max);

const fetchAllBreeds = async () => {
  const response = await fetch('https://dog.ceo/api/breeds/list/all', {
    method: 'GET'
  });

  const result = await response.json();

  return parser(result.message);
}

const fetchRandomBreed = async () => {
  const response = await fetch('https://dog.ceo/api/breeds/list/all/random', {
    method: 'GET'
  });

  const result = await response.json();

  return parser(result.message);
}

export default {
  name: 'App',
  data() {
    return {
      text: '',
      breeds: [],
      randomBreeds: [],
      selectedBreeds: []
    }
  },
  components: {
    List
  },
  computed: {
    computedText() {
      return this.text || 'незнакомец';
    }
  },
  methods: {
    async addRandom() {
      this.randomBreeds = this.randomBreeds.concat(await fetchRandomBreed());
    },
    removeRandom() {
      const integer = random(this.randomBreeds.length);

      this.randomBreeds = [...this.randomBreeds.slice(0, integer), ...this.randomBreeds.slice(integer + 1)];
    },
    swapRandom() {
      const integerCurrent = () => random(this.randomBreeds.length);

      for(let i = 0; i < this.randomBreeds.length - 1; i++) {
        const firstIndex = integerCurrent();
        let secondIndex = integerCurrent();

        while (firstIndex === secondIndex) {
          secondIndex = integerCurrent();
        }

        const breed = this.randomBreeds[firstIndex];
        this.randomBreeds[firstIndex] = this.randomBreeds[secondIndex];
        this.randomBreeds[secondIndex] = breed;
      }
    },
    selectBreed(name) {
      return this.selectedBreeds = [...this.selectedBreeds, this.breeds.find((item) => item.name === name)];
    },
    unselectBreed(name) {
      return this.selectedBreeds = this.selectedBreeds.filter((item) => item.name !== name);
    }
  },
  async mounted() {
    this.breeds = await fetchAllBreeds();
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.container {
  padding: 10px;
}

.container-lists {
  display: flex;
}

.item {
  padding: 20px;
}

.input-text {
  margin-bottom: 5px;
}

.breedItem {
  border: 1px solid;
}

.container-buttons {
  display: flex;
  flex-direction: column;
  margin-bottom: 10px;

}
</style>
