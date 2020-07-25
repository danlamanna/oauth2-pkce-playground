<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" />
    <a v-on:click="startFlow">Sign in</a>
    <a v-on:click="finishFlow">Sign in</a>
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import {
  AuthorizationRequest,
  AuthorizationServiceConfiguration,
  BaseTokenRequestHandler,
  AuthorizationServiceConfigurationJson,
  FetchRequestor,
  AuthorizationNotifier,
  TokenRequest,
  GRANT_TYPE_AUTHORIZATION_CODE,
  DefaultCrypto,
  RedirectRequestHandler
} from "@openid/appauth/built/index";

import HelloWorld from "./components/HelloWorld.vue";
const authHandler = new RedirectRequestHandler();
const notifier = new AuthorizationNotifier();
authHandler.completeAuthorizationRequestIfPossible();
authHandler.setAuthorizationNotifier(notifier);
notifier.setAuthorizationListener((request, response, error) => {
  log("Authorization request complete ", request, response, error);
  debugger;
  if (response) {
    this.code = response.code;
    console.log(response);
  }
});
const config = new AuthorizationServiceConfiguration({
  authorization_endpoint: "https://127.0.0.1:8000/o/authorize/",
  token_endpoint: "https://127.0.0.1:8000/o/token/"
});

export default {
  name: "App",
  components: {
    HelloWorld
  },
  methods: {
    startFlow() {
      const request = new AuthorizationRequest({
        client_id: "9XiMl6OV68cOuzXjTgBuHvolKdEtJBjdrVyfXHa5",
        redirect_uri: "http://localhost:8080",
        scope: "identity",
        state: undefined,
        extras: { prompt: "consent", access_type: "offline" },

        response_type: AuthorizationRequest.RESPONSE_TYPE_CODE
      });

      //      request.setupCodeVerifier();
      authHandler.performAuthorizationRequest(config, request);
    },
    finishFlow() {
      var urlParams = new URLSearchParams(window.location.search);
      const tokenHandler = new BaseTokenRequestHandler(new FetchRequestor());
      const tokenRequest = new TokenRequest({
        client_id: "9XiMl6OV68cOuzXjTgBuHvolKdEtJBjdrVyfXHa5",
        redirect_uri: "http://localhost:8080",
        grant_type: GRANT_TYPE_AUTHORIZATION_CODE,
        code: urlParams.get("code"),
        extras: {
          code_verifier: request.internal["code_verifier"]
        },
        refresh_token: undefined
      });

      tokenHandler.performTokenRequest(config, tokenRequest).then(function(r) {
        console.log(r);
      });
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
