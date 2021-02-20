<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld v-if="isLoggedIn" v-bind:msg="'Hello, ' + userName"/>
  <button class="btn-square" v-if="!isInClient" @click="logout">ログアウト</button>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import liff  from "@line/liff";

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data() {
    return {
      userName: undefined,
      isInClient: undefined,
      isLoggedIn: false,
    }
  },
  created() {
    liff
      .init({
        liffId: process.env.VUE_APP_LIFF_ID
      })
      .then(() => {
        this.isLoggedIn = liff.isLoggedIn();
        if (!liff.isInClient() && !liff.isLoggedIn()) {
          liff.login();
        } else {
          this.isInClient = liff.isInClient();
          liff.getProfile()
            .then(profile => {
              this.userName = profile.displayName;
            })
        }
      })
  },
  methods: {
    logout: function () {
      if (liff.isLoggedIn()) {
        liff.logout();
        location.reload();
      }
    }
  }
}
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
.btn-square {
  display: inline-block;
  padding: 0.5em 1em;
  text-decoration: none;
  background: #42b983;/*ボタン色*/
  color: #FFF;
  border-bottom: solid 4px #627295;
  border-radius: 3px;
}
.btn-square:active {
  /*ボタンを押したとき*/
  -webkit-transform: translateY(4px);
  transform: translateY(4px);/*下に動く*/
  border-bottom: none;/*線を消す*/
}
</style>
