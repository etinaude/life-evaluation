<script lang="ts">
import { initializeApp } from "firebase/app";
import { getFirestore, collection, addDoc } from "firebase/firestore";
import firebaseConfig from "./firebase";
import { defineComponent } from "vue";
import { getAuth, signInWithPopup, GoogleAuthProvider } from "firebase/auth";
import './form.css';


const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const auth = getAuth();

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
      authorised: false,
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

      const dateStamp = new Date();

      let data: any = { dateStamp };

      for (const item of this.items) {
        item.plan = item.plan.trim();
        item.doing = item.doing.trim();

        if (item.plan === "" || item.doing === "") {
          valid = false;
          this.showBanner("Fill Everything In!", "error");
        }

        data[item.name] = {
          name: item.name,
          doing: item.doing,
          score: item.score,
          plan: item.plan,
        };
      }

      try {
        if (!this.authorised) {
          this.showBanner("No! bad! No Auth!", "error");
          return;
        }
        if (valid) {
          const docRef = await addDoc(collection(db, "feels"), data);
          console.log("Document written with ID: ", docRef.id);
          this.showBanner("Form submitted successfully!", "success");
        }
      } catch (e) {
        console.error("Error adding document: ", e);
        this.showBanner("Error submitting form!", "error");
      }
    },

    async authorise() {

      signInWithPopup(auth, new GoogleAuthProvider())
        .then((result) => {
          const credential = GoogleAuthProvider.credentialFromResult(result);
          const token = credential!.accessToken;
          const user = result.user;

          if (user.uid.substring(0, 5) != "WFuEx") {
            this.showBanner("Sorry this site is only for me :)", "error");
            return;
          }

          this.showBanner("Hey ;)", "success");
          this.authorised = true;
          console.log(token, user);
        }).catch((error) => {
          const errorCode = error.code;
          const errorMessage = error.message;
          const email = error.customData.email;
          const credential = GoogleAuthProvider.credentialFromError(error);

          console.error(errorCode, errorMessage, email, credential)
          this.authorised = false;
          this.showBanner("Auth", "error");
        });
    }, showBanner(text: string, style: string) {
      this.banner.text = text;
      this.banner.style = style;
      this.banner.shown = true;


      setTimeout(() => {
        this.banner.shown = false;
      }, 5000);

    },
  },

  beforeMount() {
    this.authorise()
  },
});
</script>

<template>
  <div class="background">
    <div class="flex-cont">
      <div v-for="item in items">
        <div class="card shadow">
          <h3>{{ item.name }}</h3>
          <textarea placeholder="Currently Doing..." v-model="item.doing" class="large" spellcheck="false" />
          <input class="slider" type="range" min="1" max="5" v-model="item.score" />
          <textarea placeholder="Will Do..." v-model="item.plan" class="large" spellcheck="false" />
        </div>
      </div>
    </div>
  </div>

  <button @click="submitForm" class="shadow">Submit</button>

  <div :class="banner.style + ' banner'" v-if="banner.shown">
    {{ banner.text }}
  </div>
</template>
