<template>
  <div>
    <b-container>
      <b-row class="justify-content-md-center">
        <b-col md="4">
          <b-card title="Login" sub-title="Enter with your e-mail and password">
            <b-form @submit="login">
              <b-form-group id="input-group-1" label-for="input-1">
                <b-form-input id="input-1" v-model="form.email" type="email" required placeholder="Enter email" ></b-form-input>
              </b-form-group>

              <b-form-group id="input-group-2" label-for="input-2">
                <b-form-input id="input-2" type="password" v-model="form.password" required placeholder="Enter password"></b-form-input>
              </b-form-group>

              <b-button type="submit" variant="success">Login</b-button>
            </b-form>
          </b-card>
        </b-col>
      </b-row>
      <b-row class="justify-content-md-center mt-2">
        <b-col md="4">
          <b-card title="Login with SSO" sub-title="Login with your Google Account">
            <b-button @click="signinGoogle()" variant="info">Google</b-button>
          </b-card>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Vue from "vue";
import firebase from "firebase";

export default {
  data() {
    return {
      form: {
        email: "",
        password: "",
      },
    };
  },
  methods: {
    login() {
      var router = this.$router;
      var self = this;      
      firebase
        .auth()
        .signInWithEmailAndPassword(this.form.email, this.form.password)
        .catch((reason)=>{ 
          if(reason.code == "auth/user-not-found"){ 
            self.$bvToast.toast('usuário não encontrado, criando...', { title: 'Login', variant: 'default', solid: true });
            self.createUser();
          }
         })
        .then((value) => { 
        }, (reason) => {})
        .finally(()=>{ });
    },
    createUser(){
      var self = this;
      firebase
        .auth()
        .createUserWithEmailAndPassword(this.form.email, this.form.password)
        .catch((reason)=>{
          self.$bvToast.toast('create - catch: ' + reason, { title: 'Login', variant: 'default', solid: true });
          })
        .then((value) => {           
          self.$bvToast.toast('usuário criado, logando...', { title: 'Login', variant: 'default', solid: true });
          self.login();
        }, (reason) => {})
        .finally(()=>{});

    },
    signinGoogle() {
      var provider = new firebase.auth.GoogleAuthProvider();
      firebase.auth().signInWithPopup(provider);
    },
  }
};
</script>