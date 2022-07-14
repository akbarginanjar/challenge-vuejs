<template>
<div class="container">
    <div class="card mb-4">
    <div class="card-header pb-0">
      <h2>{{ currentRouteName }}</h2>
    </div>
    <div class="card-body px-0 pt-0 pb-2">
      <div class="table-responsive p-0">
        <table class="table align-items-center mb-0">
          <thead>
            <tr>
              <th
                class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7"
              >
              Nama Materi
              </th>
              <th
                class="text-uppercase text-secondary text-xxs font-weight-bolder opacity-7 ps-2"
              >
                Nama Mapel
              </th>
              <!-- <th
                class="text-center text-uppercase text-secondary text-xxs font-weight-bolder opacity-7"
              >
                Status
              </th> -->
              <!-- <th
                class=" text-uppercase text-secondary text-xxs font-weight-bolder opacity-7"
              >
                Deskripsi
              </th> -->
              <th class="text-secondary opacity-7"></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item of posts" :key="item.id">
              <td>
                <div class="d-flex px-2 py-2">
                  <div class="d-flex flex-column justify-content-center">
                    <h3 class="mb-0 text-sm">{{ item.nama_materi }}</h3>
                  </div>
                </div>
              </td>
              <td>
                <p class="text-xs font-weight-bold mb-0">{{ item.mapel.nama_mapel }}</p>
              </td>
              <!-- <td class="align-middle text-center text-sm">
                <soft-badge color="success" variant="gradient" size="sm"
                  >Online</soft-badge
                >
              </td> -->
              <!-- <td class="align-middle">
                <span class="text-secondary text-xs font-weight-bold"
                  >{{ item.deskripsi }}</span
                >
              </td> -->
              <td class="align-middle">
                <!-- <a
                  href=""
                  class="text-secondary font-weight-bold btn btn-light btn-sm"
                  data-toggle="tooltip"
                  data-original-title="Edit user"
                  >Download</a
                > -->
                <!-- <button
                  @click="download(item)"
                  class="text-secondary font-weight-bold btn btn-light btn-sm"
                  data-toggle="tooltip"
                  data-original-title="Edit user"
                  >Download</button
                > -->
                <button @click="lihat(item.id)" class="text-secondary font-weight-bold btn btn-light btn-sm"
                  data-toggle="tooltip">DOWNLOAD</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      
    </div>
  </div>
</div>
</template>

<script>
import axios from 'axios'

export default {
    data() {
        return {
            posts: [],
            currentRouteName: "",
            data: {
              path: null
            }
        }
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
  forceFileDownload(response, item) {
    var headers = response.headers;
    var extension = item.url.substring(item.url.lastIndexOf(". ")+1)
    var blob = new Blob([response.data.file_materi.file], 
    {
      type: headers['content-type']
      }
    );
    var link = document.createElement('a');
    link.href = window.URL.createObjectURL(blob);
    link.download = `${item.name}.${extension}`;
    link.click();
    link.remove()
  },
  download(item) {
    axios({
      method: 'get',
      url: 'https://api.belajar.link/materi',
      headers: {
        'secret': 'ADeuCasb4fDkdakK5IJas3CQ5h0TfrWGbiyHbYHBBIblitnj545GHdnrcdgNS6Q',
        Authorization: `bearer ${localStorage.token}`
      },
      responseType: 'blob'
    })
    .then(response => {
      this.forceFileDownload(response, item)
    })
    .catch((err) => console.log(err))
  },
    load() {
            axios.get('https://api.belajar.link/materi', {
              headers: {
                    'secret': 'ADeuCasb4fDkdakK5IJas3CQ5h0TfrWGbiyHbYHBBIblitnj545GHdnrcdgNS6Q',
                    Authorization: `bearer ${localStorage.token}`
                }
            })
            .then((res)=>{
                this.posts = res.data.data
                if (this.posts.file_materi != null && this.posts.file_materi.length > 0) {
            this.posts.file_materi.map((data) => {
              this.data.path = process.env.VUE_APP_API_FILE + "/" + data.file;
              data.filename = data.file.split("/").pop();
            });
                }
            })
            .catch(()=>{
                alert('error loaded')
            })
        },
        lihat(id) {
          this.$router.push('/materi/'+id)
        }
},
    mounted() {
        this.load();
    }
}
</script>
