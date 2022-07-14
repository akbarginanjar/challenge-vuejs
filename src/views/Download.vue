<template>
<div class="container">
    <div class="card mb-4">
    <div class="card-header pb-0">
      <h2>{{ currentRouteName }}</h2>
    </div>
    <div class="card-body px-0 pt-0 pb-2">
      <div class="container">
          <label for="">Nama Materi : </label>
          <h2>{{ posts.nama_materi }}</h2>
          <label for="">Deskripsi :</label>
          <h5 class="text-secondary">{{ posts.deskripsi }}</h5><br><br>
          <h6 class="text-dark">Nama Mapel : {{ mapel }}</h6><br>

          <div
      class=""
      v-if="posts.file_materi != null && posts.file_materi.length > 0"
    >
    <div v-for="(item, i) in posts.file_materi" :key="i">
        <h6 class="mb-4">Materi ke {{ ++i }}</h6>
        <!-- <h2>{{ item.path }}</h2> -->
        <button class="btn btn-success" @click.prevent="downloadMateri(item.path)" >Download <i class="fa fa-download ms-2" aria-hidden="true"></i></button>
          </div>
      </div>
    </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default{
    data() {
        return {
            posts: {},
            mapel: '',
            path: '',
        }
    },
    methods: {
        downloadMateri(path) {
      window.open(path, "_blank");
    },
        load() {
            axios.get('https://api.belajar.link/materi/' + this.$route.params.id, {
              headers: {
                    'secret': 'ADeuCasb4fDkdakK5IJas3CQ5h0TfrWGbiyHbYHBBIblitnj545GHdnrcdgNS6Q',
                    Authorization: `bearer ${localStorage.token}`
                }
            })
            .then((res)=>{
                this.posts = res.data
                this.mapel = res.data.mapel.nama_mapel
                if (res.data.file_materi != null && res.data.file_materi.length > 0) {
            res.data.file_materi.map((item) => {
              item.path = 'https://file.belajar.link' + "/" + item.file;
              item.filename = item.file.split("/").pop();
            });
                }
            })
            .catch(()=>{
                alert('error loaded')
            })
        },
    },
    mounted() {
        this.load();
    }
}
</script>
