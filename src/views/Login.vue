<template>
  <v-card width="400" class="mx-auto mt-12">
    <v-card-title>
      <h1 class="display-1">Авторизация</h1>
    </v-card-title>
    <v-card-text>
    <ValidationObserver ref="observer" v-slot="{ passes }" slim>
      <form @submit.prevent="passes(logIn)">

          <ValidationProvider
            rules="required|alpha"
            v-slot="{ errors }"
            name="Логин"
            mode="eager"
            vid="login"
          >
            <v-text-field
              label="Логин"
              :error-messages="errors"
              type="text"
              prepend-icon="mdi-account-circle"
              v-model="login"
            />
          </ValidationProvider>

          <ValidationProvider
            rules="required|alpha"
            v-slot="{ errors }"
            name="Пароль"
            mode="eager"
            vid="password"
          >
            <v-text-field
              label="Пароль"
              :error-messages="errors"
              :type="showPassword ? 'text' : 'Password'"
              prepend-icon="mdi-lock"
              :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
              @click:append="showPassword = !showPassword"
              v-model="password"
            />
          </ValidationProvider>
        <v-card-actions class="mt-2">
          <v-btn type="submit" color="info">Войти</v-btn>
        </v-card-actions>
      </form>
    </ValidationObserver>
    </v-card-text>
  </v-card>
</template>

<script>
import { mapActions } from "vuex";
import {
  extend,
  localize,
  ValidationProvider,
  ValidationObserver
} from "vee-validate";
import ru from "vee-validate/dist/locale/ru.json";

import { required, alpha } from "vee-validate/dist/rules";

localize("ru", ru);
extend("required", required);
extend("alpha", alpha);
extend("alpha", {
  message: "Поле может содержать только строчные буквы"
});

export default {
  name: "Login",
  components: {
    ValidationProvider,
    ValidationObserver
  },
  data: () => ({
    login: "",
    password: "",
    showPassword: false
  }),
  methods: {
    ...mapActions(["requestData"]),
    logIn() {
      this.$store
        .dispatch("authorizationRequest", {
          login: this.login,
          password: this.password
        })
        .then(() => {
          this.requestData();
          this.$router.push("/contacts").catch(() => {});
        })
        .catch(response =>
          this.$refs.observer.setErrors({
            login: [response.message],
            password: [response.message]
          })
        )
      }
    }
  }
</script>