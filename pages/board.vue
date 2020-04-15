<template>
  <div class="container">
    <div class="login" @click="doLogin">[login:{{user_name}}]</div>
    <h1>{{title}}</h1>
    <p class="message">{{message}}</p>
    <div v-if="logined">
      <table>
        <tr>
          <th>Message</th>
          <td>
            <input v-model="msg" size="50" />
          </td>
          <td>
            <button @click="add">投稿</button>
          </td>
        </tr>
      </table>
      <hr />
      <ul v-for="(data, key) in json_data" :key="key">
        <li>
          <div class="list1">{{data.msg}}</div>
          <div class="list2>">{{data.user}} ({{data.posted}})</div>
        </li>
      </ul>
    </div>
  </div>
</template>


<script>
// https://firebase.google.com/docs/web/setup
import * as firebase from "firebase/app";
import "firebase/auth";
import "firebase/database";

const axios = require("axios");
var url = "https://cs8-vue.firebaseio.com/person";

let firebaseConfig = {
  apiKey: "AIzaSyB9u5Rtx03n8pElnFD1QL9qenbThI4pEqg",
  authDomain: "cs8-vue.firebaseapp.com",
  databaseURL: "https://cs8-vue.firebaseio.com",
  projectId: "cs8-vue",
  storageBucket: "cs8-vue.appspot.com",
  messagingSenderId: "508542185763",
  appId: "1:508542185763:web:b69a21d6a5d3c1f7619c96",
  measurementId: "G-T2NHX43ZQR"
};

// Initialize Firebase
firebase.initializeApp(firebaseConfig);

export default {
  data: function() {
    return {
      title: "Board",
      message: "ミニ伝言板. 最新の投稿を表示します.",
      user_name: "",
      logined: true,
      msg: "",
      page: 0,
      num_per_page: 10, // 取り出すデータ数
      json_data: {}
    };
  },
  methods: {
    login: function() {
      var provider = new firebase.auth.GoogleAuthProvider();
      var self = this;
      firebase
        .auth()
        .signInWithPopup(provider)
        .then(function(result) {
          console.log(result.user);
          self.user = result.user;
          self.user_name = result.user.displayName;
          self.message = "ログインしました";

          let db = firebase.database();
          let ref = db.ref("board");
          ref
            .orderByKey()
            .limitToLast(self.num_per_page)
            .on("value", function(snapshot) {
              if (firebase.auth().currentUser != null) {
                let arr = [];
                let data = snapshot.val();
                for (let item in data) {
                  arr.unshift(data[item]);
                }
                console.log(arr);
                self.json_data = arr;
                return;
              }
              self.json_data = {};
            });
        })
        .catch(error => {
          self.message = "Login failed! " + error.message;
        });
    },
    logout: function() {
      firebase.auth().signOut();
      this.user_name = "";
      this.json_data = {};
      this.message = "ログアウトしました";
    },
    doLogin: function() {
      if (firebase.auth().currentUser == null) {
        this.login();
        return;
      }
      this.logout();
    },
    add: function() {
      if (firebase.auth().currentUser == null) {
        alert("ログインしないと投稿できません。");
        return;
      }
      let db = firebase.database();
      let user = firebase.auth().currentUser;
      console.log(user);
      let self = this;
      let d = new Date();
      let dstr =
        d.getFullYear() +
        "-" +
        (d.getMonth() + 1) +
        "-" +
        d.getDate() +
        " " +
        d.getHours() +
        ":" +
        d.getMinutes() +
        ":" +
        d.getSeconds();
      let id = d.getTime();
      let data = {
        msg: this.msg,
        user: user.displayName,
        posted: dstr
      };
      db.ref("board/" + id).set(data);
      this.msg = "";
      this.message = "投稿しました";
    },
    created: function() {
      if (firebase.auth().currentUser == null) {
        this.login();
      }
      console.log(firebase.auth().currentUser);
    }
  }
};
</script>

<style>
.login {
  font-weight: bold;
  font-size: 12pt;
  cursor: pointer;
}
.list1 {
  text-align: left;
  font-size: 16pt;
}
.list2 {
  text-align: right;
  font-size: 10pt;
}
.container {
  padding: 5px 10px;
}
h1 {
  font-size: 60pt;
  color: #345980;
}
p {
  padding-top: 5px;
  font-size: 20pt;
}
div {
  font-size: 14pt;
}

hr {
  margin: 10px 0px;
}
tr th {
  width: 150px;
  background-color: darkblue;
  color: white;
  font-size: 16pt;
}
tr td {
  padding: 5px 10px;
  background-color: #eef;
  font-size: 14pt;
}
pre {
  padding: 10px;
  font-size: 18pt;
  background-color: #efefef;
  white-space: pre-wrap;
}
ul {
  margin: 0px 10px;
  font-size: 14pt;
}
input {
  font-size: 14pt;
}
button {
  font-size: 14pt;
}
</style>