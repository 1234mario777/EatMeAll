<template>
  <q-form class="col-grow col-sm-9">
    <div>
      <h4>Logowanie</h4>
    </div>
    <div>
      <QInputWithCustoRules
        @updateRecipeInputValue="updateLogin($event)"
        :props="{ type: 'text', value: login, label: 'Login/Email' }"
      ></QInputWithCustoRules>
      <QInputWithCustoRules
        @updateRecipeInputValue="updatePassword($event)"
        :props="{ type: 'password', value: password, label: 'Haslo' }"
      ></QInputWithCustoRules>
      <template>
        <div class="q-gutter-md">
          <q-btn
            class="bg-primary text-white"
            label="Zaloguj sie"
            @click="loginUser()"
          ></q-btn>
          <q-btn
            label="Dołącz"
            class="bg-primary text-white"
            @click="redirectToForm('/rejestracja')"
          ></q-btn>
        </div>
      </template>
    </div>
  </q-form>
</template>
<script>
import { LOGIN } from "../../EndpointAddresses.js";
import { mapActions } from "vuex";

export default {
  data() {
    return {
      login: "admin",
      password: "password",
    };
  },
  methods: {
    ...mapActions("store", ["updateJwt"]),
    redirectToForm(aPath) {
      this.$router.push({ path: aPath });
    },
    postRequest() {
      let headersList = {
        Accept: "*/*",
        "User-Agent": "Thunder Client (https://www.thunderclient.io)",
        "Content-Type": "application/json",
      };

      fetch(LOGIN, {
        method: "POST",
        body: `{
          "username":"${this.login.trim().toLowerCase()}",
          "password":"${this.password.trim().toLowerCase()}"
        }`,
        headers: headersList,
      })
        .then((response) => {
          if (response.ok) {
            return response.json();
          } else {
            throw new Error();
          }
        })
        .then((data) => {
          this.updateJwt(data.token);
          this.redirectToForm("/plan");
        })
        .catch((e) => {
          console.error(e);
          this.notifyError(e);
        });
    },
    notifyError(e) {
      Notify.create({
        message: `⚠ ${e}`,
        classes: "full-width text-center bg-negative",
      });
    },
    loginUser() {
      this.postRequest();
      this.updateJwt();
    },
    updateLogin(update) {
      this.login = update;
    },
    updatePassword(update) {
      this.password = update;
    },
  },
  components: {
    QInputWithCustoRules: () => import("../common/QInputWithCustoRules.vue"),
  },
};
</script>