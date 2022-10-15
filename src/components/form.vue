<script lang="ts">
import { initializeApp } from "firebase/app";
import { getFirestore, collection, addDoc } from "firebase/firestore";
import firebaseConfig from "./firebase";
import { defineComponent } from "vue";

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

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

export default defineComponent({
  data() {
    return {
      banner: {
        text: "",
        style: "loading",
        shown: false,
      },
      items: [
        new Field("Educational"),
        new Field("Financial"),
        new Field("Physical"),
        new Field("Social"),
        new Field("Societal"),
      ],
    };
  },

  methods: {
    async submitForm() {
      let valid = true;

      this.banner = {
        text: "Loading...",
        style: "loading",
        shown: true,
      };

      const dateStamp = new Date();

      let data: any = { dateStamp };

      for (const item of this.items) {
        item.plan = item.plan.trim();
        item.doing = item.doing.trim();

        if (item.plan === "" || item.doing === "") {
          valid = false;
          this.banner = {
            text: "Fill Everything In!",
            style: "error",
            shown: true,
          };
        }

        data[item.name] = {
          name: item.name,
          doing: item.doing,
          score: item.score,
          plan: item.plan,
        };
      }

      try {
        if (valid) {
          const docRef = await addDoc(collection(db, "feels"), data);
          console.log("Document written with ID: ", docRef.id);
          this.banner = {
            text: "Form submitted successfully!",
            style: "success",
            shown: true,
          };
        }
      } catch (e) {
        console.error("Error adding document: ", e);
      }

      setTimeout(() => {
        this.banner.shown = false;
      }, 5000);
    },
  },
});
</script>

<template>
  <div class="flex-cont">
    <div v-for="item in items">
      <div class="col">
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

  <button @click="submitForm">Submit</button>

  <div :class="banner.style + ' banner'" v-if="banner.shown">
    {{ banner.text }}
  </div>
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

.col {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  align-content: center;
  padding: 0 20px;
  margin: 0;
  min-width: 400px;
  border-right: 2px solid #4fffc4;
  height: 100%;
}

.col > * {
  width: 100%;
  padding: 0;
  margin: 10px 0;
  height: 40px;
}

.col > .large {
  height: 200px;
}

.col:first-of-type {
  border-left: 2px solid #4fffc4;
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

.banner {
  padding: 1rem;
  border-radius: 10px;
  text-align: center;
  font-weight: bold;

  position: fixed;
  top: 10px;
  left: 50%;
  transform: translate(-50%, 0);
  width: 90%;
  background-color: #111;
}

.banner.success {
  border: 2px solid #4fffc4;
  color: #4fffc4;
}

.banner.error {
  border: 2px solid #ff4f4f;
  color: #ff4f4f;
}

.banner.loading {
  border: 2px solid #4da9ff;
  color: #4da9ff;
}
</style>
