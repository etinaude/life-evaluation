<script lang="ts">
class Field {
  name: string;
  doing: string;
  score: number;
  plan: string;

  constructor(name: string) {
    this.name = name;
    this.doing = "";
    this.score = 0;
    this.plan = "";
  }
}

export default {
  data() {
    return {
      parentMessage: "Parent",
      items: [
        new Field("Educational"),
        new Field("Financial"),
        new Field("Physical"),
        new Field("Social"),
        new Field("Societal"),
      ],
    };
  },
};
</script>

<template>
  <div class="flex-cont">
    <div v-for="item in items">
      <div class="row">
        <h3>{{ item.name }}</h3>
        <textarea
          placeholder="Currently Doing..."
          v-model="item.doing"
          class="large"
          spellcheck="false"
        />
        <input
          class="slider"
          type="range"
          min="1"
          max="5"
          v-model="item.score"
        />
        <textarea
          placeholder="Will Do..."
          v-model="item.plan"
          class="large"
          spellcheck="false"
        />
      </div>
    </div>
  </div>

  <button class="btn">Submit</button>
</template>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@100;300;400;500;700;900&display=swap");

:root {
  --primary: #4fffc4;
}
.flex-cont {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  align-content: center;
  width: 100%;
  height: 100%;
  padding: 0;
  margin: 0;
  background: #222;
}

* {
  color: #eee;
  user-select: none;
  font-family: "Roboto", sans-serif;
}

.row {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  align-content: center;
  padding: 0 20px;
  margin: 0;
  min-width: 300px;
  border-right: 2px solid #4fffc4;
  height: 100%;
}

.row > * {
  width: 100%;
  padding: 0;
  margin: 10px 0;
  height: 40px;
}

.row > .large {
  height: 150px;
}

textarea {
  height: 100px;
  border: 1px solid #ccc;
  background: transparent;
  border-radius: 5px;
  resize: none;
  transition: ease 0.2s;
  user-select: all;
  font-size: 16px;
  line-height: 25px;
}

textarea:hover,
textarea:focus {
  background: #111;
}

textarea::placeholder {
  color: #888;
  margin: 2px;
}

h3 {
  font-weight: bold;
  margin: 0;
  padding: 0;
  text-align: center;
  font-size: 2em;
}

.slider:hover {
  opacity: 1;
}

.slider {
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  height: 25px;
  background: #111;
  opacity: 0.7;
  transition: opacity 0.2s;
  cursor: pointer;
  border-radius: 100vmax;
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 25px;
  height: 25px;
  border-radius: 100vmax;
  background: #4fffc4;
  cursor: pointer;
}

::-webkit-scrollbar {
  width: 10px;
  border-radius: 100vmax;
}

/* Track */
::-webkit-scrollbar-track {
  background: #222;
  border-radius: 100vmax;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 100vmax;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #fff;
}

button {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 50px;
  background: #4fffc4;
  border: none;
  color: #222;
  font-size: 1.5em;
  font-weight: bold;
  cursor: pointer;
  transition: ease 0.2s;
}
</style>
