<template>

 <main class="mt-0 main-content main-content-bg">
    <section>
      <div class="page-header min-vh-75">
        <div class="container">
          <div class="row">
            <div class="mx-auto col-xl-4 col-lg-5 col-md-6 d-flex flex-column">
              <div class="mt-8 card card-plain">
                <div class="pb-0 card-header text-start">
                  <h1 class="font-weight-bolder text-success text-gradient">
                    Login
                  </h1>
                  <p class="mb-0">Enter your email and password to sign in</p>
                </div>
                <div class="card-body">
                  <!-- <h4>Ini Adalah halaman {{ currentRouteName }}</h4> -->

<form @submit.prevent="handleSubmit">
    <div class="form-group">   
        <label>Email</label> 
            <input type="email" class="form-control" v-model="email" placeholder="Email">
        </div>
        <div class="form-group">
        <label>Password</label> 
            <input type="password" class="form-control" v-model="password" placeholder="Password">
        </div>
    <button type="submit" class="btn btn-success form-control">
        Login
    </button>
</form>
</div>
                <div class="px-1 pt-0 text-center card-footer px-lg-2">
                  <p class="mx-auto mb-4 text-sm">
                    Don't have an account?
                    <router-link
                      :to="{ name: 'Sign Up' }"
                      class="text-success text-gradient font-weight-bold"
                      >Sign up</router-link
                    >
                  </p>
                </div>
              </div>
            </div>
            <div class="col-md-6">
              <div
                class="top-0 oblique position-absolute h-100 d-md-block d-none me-n8"
              >
                <div
                  class="bg-cover oblique-image position-absolute fixed-top ms-auto h-100 z-index-0 ms-n6"
                  :style="{
                    backgroundImage:
                      'url(' +
                      require('@/assets/img/curved-images/curved9.jpg') +
                      ')',
                  }"
                ></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
  <app-footer />

</template>

<script>
import AppFooter from "@/examples/PageLayout/Footer.vue";
const body = document.getElementsByTagName("body")[0];
import { mapMutations } from "vuex";
import axios from 'axios'

	export default {
		name: 'Login',
        data() {
    return {
      email: "",
      password: "",
      currentRouteName: ""
    };
  },
  components: {
    AppFooter,
  },
  created() {
    this.currentRouteName = this.$route.name;
    this.toggleEveryDisplay();
    this.toggleHideConfig();
    body.classList.remove("bg-gray-100");
  },
  beforeUnmount() {
    this.toggleEveryDisplay();
    this.toggleHideConfig();
    body.classList.add("bg-gray-100");
  },
  methods: {
    ...mapMutations(["toggleEveryDisplay", "toggleHideConfig"]),

    handleSubmit() {
      if (this.email.trim() && this.password.trim()) {
        let formData = new FormData();
        formData.append("email", this.email.trim());
        formData.append("password", this.password);

        const options = {
          url: "https://api.belajar.link/auth/user-password",
          method: "post",
          headers: {
            "secret": "ADeuCasb4fDkdakK5IJas3CQ5h0TfrWGbiyHbYHBBIblitnj545GHdnrcdgNS6Q",
            "Content-Type": "multipart/form-data"
          },
          data: formData
        };

        axios(options)
          .then(response => {
            const token = response.data.token;

            if (token) {
              this.$router.push({
                name: "Index",
                params: {
                  token: token
                }
              });
            }
          })
          .catch(e => {
            alert(e + "\n" + "Username / password yang dimasukkan salah.");
          });
      }
    }
  }
		// data() {
		// 	return {
		// 		email: '',
		// 		password: '',
        //         currentRouteName: ''
		// 	}
		// },
        // created() {
        //     this.currentRouteName = this.$route.name;
        // },
		// methods: {
		// 	async handleSubmit() {
        //         // const token = ""

        //         axios.post('https://api.belajar.link/auth/user-password', {
        //         // data
        //             email: this.email,
		// 			password: this.password
        //         }, {
        //         headers: {
        //             'secret': 'ADeuCasb4fDkdakK5IJas3CQ5h0TfrWGbiyHbYHBBIblitnj545GHdnrcdgNS6Q',
        //             // "Authorization": `Bearer ${token}`
        //         }
        //         })
        //         .then(response => {
        //             const token = response.data.token;

        //             if(token) {
        //             this.$router.push({
        //                 name: "Dashboard",
        //                 params: {
        //                 token: token
        //                 }
        //             });
        //             }
        //         })
        //         .catch(e => {
        //             alert(e + '\n' + 'Username / password salah');
        //         })
		// 	}
		// }
	}

</script>

