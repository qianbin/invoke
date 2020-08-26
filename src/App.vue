<template>
  <div id="app">
    <input type="button" value="Sign Auto" />
    <input type="button" value="Sign via SPA" @click="signViaSPA" />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { blake2b256 } from "thor-devkit/dist/cry/blake2b";
import { randomBytes } from "crypto";

export default Vue.extend({
  name: "App",
  methods: {
    signViaSPA() {
      this.uploadRequest().then((id) => {
        window.open(`http://127.0.0.1:8080/#sign?rid=${id}`);
      });
    },
    uploadRequest() {
      const req = this.buildRequest();
      const body = JSON.stringify(req);
      const reqId = blake2b256(body).toString("hex");
      return fetch("https://tos.vecha.in:5678/" + reqId, {
        method: "POST",
        headers: {
          "content-type": "application/json",
        },
        body,
      })
        .then(() => {
          return reqId;
        })
        .catch((err) => {
          alert("error:" + err);
        });
    },
    buildRequest() {
      return {
        secret: randomBytes(16).toString("hex"),
        type: "tx",
        gid:
          "0x000000000b2bce3c70bc649a02749e8687721b09ed2e15997f466536b20bb127",
        body: {
          message: [
            {
              to: "0x9840AcBcd7417ceEe8117dA585a3C3F6642c8a52",
              value: "1000000000000000000",
              comment: "this is clause comment",
            },
          ],
          options: {
            comment: "this is tx comment",
            /*signer?: string
                            gas?: number
                            dependsOn?: string
                            link?: string
                            comment?: string
                            delegate?: string*/
          },
        },
      };
    },
  },
});
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
