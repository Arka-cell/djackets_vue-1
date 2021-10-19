<template>
  <div class="page-log-in">
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <h1 class="title font-king is-size-3">Se Connecter</h1>

        <form @submit.prevent="submitForm">
          <div class="field">
            <label class="font-king is-size-4">Nom d'Utilisateur</label>
            <div class="control">
              <input
                type="text"
                class="input font-king is-size-6"
                v-model="username"
              />
            </div>
          </div>

          <div class="field">
            <label class="font-king is-size-4">Mot de Passe</label>
            <div class="control">
              <input type="password" class="input" v-model="password" />
            </div>
          </div>

          <div class="notification is-danger font-king" v-if="errors.length">
            <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
          </div>

          <div class="field">
            <div class="control">
              <button class="button is-dark font-king is-size-5">
                Se Connecter
              </button>
            </div>
          </div>

          <hr />

          <div class="font-king is-size-5">
            Ou <router-link to="/sign-up">cliquez içi</router-link> pour
            s'inscrire!
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "LogIn",
  data() {
    return {
      username: "",
      password: "",
      errors: [],
    };
  },
  mounted() {
    document.title = "Log In | Djackets";
  },
  methods: {
    async submitForm() {
      axios.defaults.headers.common["Authorization"] = "";

      localStorage.removeItem("token");

      const formData = {
        username: this.username,
        password: this.password,
      };

      await axios
        .post("/api/v1/token/login/", formData)
        .then((response) => {
          const token = response.data.auth_token;

          this.$store.commit("setToken", token);

          axios.defaults.headers.common["Authorization"] = "Token " + token;

          localStorage.setItem("token", token);

          const toPath = this.$route.query.to || "/cart";

          this.$router.push(toPath);
        })
        .catch((error) => {
          if (error.response) {
            for (const property in error.response.data) {
              this.errors.push(
                "Mot de passe ou nom d'utilisateur est incorrecte"
              );
            }
          } else {
            this.errors.push("Something went wrong. Please try again");

            console.log(JSON.stringify(error));
          }
        });
    },
  },
};
</script>
