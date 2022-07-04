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
    const token = this.$route.params.token;
    if (token) {
      this.token = token;
      alert("Anda mengakses halaman Backend dengan token : " + "\n\n" + token);
    }
  }
};
</script>

<style scoped>
p {
  text-align: center;
}
</style>