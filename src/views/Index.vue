<template>
<div class="card">
    <div class="card-body">
        <h1>Selamat Datang</h1>
        <h4>Ini adalah halaman {{ currentRouteName }}</h4>
    </div>
    <p class="text-success" v-if="token">
            Halaman ini diakses DENGAN autentikasi login dan token !!
          </p>
          <p class="text-danger" v-else>
            Halaman ini diakses TANPA autentikasi login dan token !!
          </p>
</div>
</template>
<script>
// import axios from 'axios';
export default {
  name: "Index",
  data() {
    return {
      token: "",
      currentRouteName: ""
    };
  },
  created() {
    this.currentRouteName = this.$route.name;
    const token = localStorage.getItem('token');
    if (token) {
      this.token = token;
    } else {
      this.$router.push({
        name: 'Login'
      })
    }
  },
  methods: {
    logout() {
      localStorage.clear();
      this.$router.push('/login')
      //  const options = {
      //     url: "https://api.belajar.link/auth/logout",
      //     method: "post",
      //     headers: {
      //       "secret": "ADeuCasb4fDkdakK5IJas3CQ5h0TfrWGbiyHbYHBBIblitnj545GHdnrcdgNS6Q",
      //       "Content-Type": "multipart/form-data"
      //     },
      //   };

      //   axios(options)
      //   .then((response) => {
      //     localStorage.clear();

      //   })
      //   .catch((err) => {
      //   })
    }
  }
};
</script>

<style scoped>
p {
  text-align: center;
}
</style>