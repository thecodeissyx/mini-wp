<template>
  <div class="container d-flex justify-content-center align-items-center" style="height:90vh;">
    <div>
      <h1 style="text-align:center;margin-bottom:30px;color:grey">Register for free now!</h1>
      <form>
        <div class="form-group row" style="align-items: center;justify-content: center;">
          <label for="name" class="col-sm-4 col-form-label">Name</label>
          <input
            v-model="name"
            placeholder="Name"
            style="background-color:transparent;border: 1px solid grey;"
            type="text"
            class="form-control col-8 rounded-0"
            id="name"
          />
        </div>
        <div class="form-group row" style="align-items: center;justify-content: center;">
          <label for="email" class="col-sm-4 col-form-label">Email address</label>
          <input
            v-model="email"
            type="email"
            placeholder="Email address"
            class="form-control col-8 rounded-0"
            id="email-register"
            style="background-color:transparent;border: 1px solid grey;"
          />
        </div>
        <div class="form-group row" style="align-items: center;justify-content: center;">
          <label for="password" class="col-sm-4 col-form-label">Password</label>
          <input
            v-model="password"
            type="password"
            placeholder="Password"
            class="form-control col-8 rounded-0"
            id="password-register"
            style="background-color:transparent;border: 1px solid grey"
          />
        </div>
        <div class="form-group row" style="align-items: center;justify-content: center;">
          <button
            style=" text-align:center"
            @click.prevent="registerUser"
            type="submit"
            class="btn btn-outline-secondary rounded-0"
          >Register Now</button>
        </div>
        <p style="text-align:center; margin:20px">OR</p>
        <GoogleLogin
          :params="params"
          :renderParams="renderParams"
          :onSuccess="onSuccess"
          :onFailure="onFailure"
        >Login</GoogleLogin>
        <p style="text-align:center">
          Already registered ?
          <a @click.prevent="movePage('login-page')" href="#">login</a> with
          your account
        </p>
      </form>
    </div>
  </div>
</template>

<script>
import axios from "../config/axios";
import Swal from "sweetalert2";
import GoogleLogin from "vue-google-login";

export default {
  components: {
    GoogleLogin
  },
  data() {
    return {
      name: "",
      email: "",
      password: "",
      renderParams: {
        width: 250,
        height: 50,
        longtitle: true
      },
      params: {
        client_id:
          "965890208116-v1u6q7d2u690tl22dvmlekaf87qs2e54.apps.googleusercontent.com"
      }
    };
  },
  methods: {
    movePage(page) {
      this.$emit("currentPage", { page });
    },
    checkUserLogin() {
      const accessToken = localStorage.getItem("access_token");
      if (accessToken) {
        this.$emit("currentPage", { page: "dashboard-page" });
      }
    },
    onSuccess(googleUser) {
      let gProfile = googleUser.getBasicProfile();
      axios({
        method: "POST",
        url: `users/gsignin`,
        data: {
          gProfile
        }
      })
        .then(({ data }) => {
          localStorage.setItem("access_token", data);
          Swal.fire({
            title: "Success!",
            text: "Login success",
            icon: "success",
            confirmButtonText: "Okay"
          });
          this.$emit("currentPage", { page: "dashboard-page" });
        })
        .catch(err => {
          console.log(err);
        });
    },
    onFailure() {},
    registerUser() {
      const data = {
        name: this.name,
        email: this.email,
        password: this.password
      };
      axios({
        method: "POST",
        url: "/users/register",
        data
      })
        .then(({ data }) => {
          localStorage.setItem("access_token", data.token);
          Swal.fire({
            title: "Success!",
            text: "Register success",
            icon: "success",
            confirmButtonText: "Okay"
          });
          this.$emit("currentPage", { page: "dashboard-page" });
        })
        .catch(err => {
          console.log(err);
          Swal.fire({
            title: "Error!",
            text: `${err.message}`,
            icon: "error",
            confirmButtonText: "Okay"
          });
        });
    }
  },
  created() {
    this.checkUserLogin();
  }
};
</script>

<style></style>
