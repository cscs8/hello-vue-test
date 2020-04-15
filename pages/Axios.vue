<template>
  <div class="container">
    <h1>{{title}}</h1>
    <p>{{message}}</p>
    <div>
      <input type="text" v-model="find" />
      <button @click="getData">Click</button>
    </div>
    <!-- <ul>
      <li>{{json_data}}</li>
    </ul>-->
    <table>
      <tr>
        <th>Email</th>
        <td>
          <input v-model="email" />
          <button @click="delData">Delete</button>
        </td>
      </tr>
      <tr>
        <th>Name</th>
        <td>
          <input v-model="username" />
        </td>
      </tr>
      <tr>
        <th>Age</th>
        <td>
          <input v-model="age" />
        </td>
      </tr>
      <tr>
        <th>Tel</th>
        <td>
          <input v-model="tel" />
        </td>
      </tr>
      <tr>
        <th></th>
        <td>
          <button @click="addData">Click</button>
        </td>
      </tr>
    </table>
    <ul v-for="(data, key) in json_data" :key="key">
      <li>
        <strong>{{key}}</strong>
        <br />
        {{data}}
      </li>
    </ul>
    <!-- <table>
      <tr>
        <th>User ID</th>
        <td>{{json_data.userId}}</td>
      </tr>
      <tr>
        <th>ID</th>
        <td>{{json_data.id}}</td>
      </tr>
      <tr>
        <th>Title</th>
        <td>{{json_data.title}}</td>
      </tr>
      <tr>
        <th>Body</th>
        <td>{{json_data.body}}</td>
      </tr>
    </table>-->
  </div>
</template>

<script>
// https://firebase.google.com/docs/web/setup
import * as firebase from "firebase/app";
import "firebase/auth";

const axios = require("axios");
// var url = "https://jsonplaceholder.typicode.com/todos/1";
// var url = "https://jsonplaceholder.typicode.com/posts/";
// orderBy="$key"&equalTo=";
var url =
  // "https://cs8-vue.firebaseio.com/person.json?orderBy=%22$key%22&equalTo=%22";
  // "https://cs8-vue.firebaseio.com/person.json?orderBy=%22age%22";
  "https://cs8-vue.firebaseio.com/person";

export default {
  data: function() {
    return {
      title: "Axios",
      find: "",
      email: "",
      username: "",
      tel: "",
      age: 0,
      // msg: "",
      message: "axios app.",
      json_data: {}
    };
  },
  created: function() {
    var firebaseConfig = {
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

    // firebase.analytics();
    var provider = new firebase.auth.GoogleAuthProvider();
    var self = this;
    firebase
      .auth()
      .signInWithPopup(provider)
      .then(function(result) {
        self.message = result.user.displayName + ", " + result.user.email;
        self.message += result.user;
      })
      .catch(error => {
        self.message = "Login failed! " + error.message;
      });
  },
  methods: {
    delData: function(event) {
      let del_url = url + "/" + this.email + ".json";
      axios
        .delete(del_url)
        .then(res => {
          console.log(res);
          if (res.data == null) {
            this.message = this.email + "は存在しません.";
            return;
          }
          this.message = this.email + "を削除しました!";
          this.email = "";
          this.getData();
        })
        .catch(error => {
          this.message = "del ERROR!";
        });
    },
    addData: function(event) {
      let add_url = url + "/" + this.email + ".json";
      let data = {
        name: this.username,
        age: this.age,
        tel: this.tel
      };
      axios
        .put(add_url, data)
        .then(res => {
          this.email = "";
          this.username = "";
          this.age = 0;
          this.tel = "";
          this.getData();
        })
        .catch(error => {
          this.message = "add ERROR!";
        });
    },
    getData: function() {
      axios
        .get(url + ".json")
        .then(res => {
          this.message = "get all Data";
          this.json_data = res.data;
        })
        .catch(error => {
          // this.message = error;
          this.message = "ERRROR!";
          this.json_data = {};
        });
    }
    // getData: function(event) {
    //   let range = this.find.split(",");
    //   // let id_url = url + this.find + "%22";
    //   let age_url = url + "&startAt=" + range[0] + "&endAt=" + range[1];
    //   axios
    //     // .get(id_url)
    //     .get(age_url)
    //     .then(res => {
    //       this.message = "get ID=" + this.find;
    //       this.json_data = res.data;
    //     })
    //     .catch(error => {
    //       // this.message = error;
    //       this.message = "ERRROR!";
    //       this.json_data = {};
    //     });
    // }
    // doClick: function(event) {
    //   axios
    //     .get(url + this.msg)
    //     .then(res => {
    //       this.message = "get ID=" + this.msg;
    //       this.json_data = res.data;
    //     })
    //     .catch(error => {
    //       // this.message = error;
    //       this.message = "ERRROR!";
    //     });
    // }
  }
  // asyncData: async function() {
  //   let id = 1;
  //   let result = await axios.get(url + id);
  //   return {
  //     json_data: result.data
  //   };
  // }
};
</script>


<style>
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
</style>