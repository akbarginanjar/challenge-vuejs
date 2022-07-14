<template>
  <main class="container md:p-8 lg:p-8">
    <header class="mb-8">
      <h4>
        <strong>{{ materi.nama_materi }}</strong>
      </h4>
    </header>
    <div
      class=""
      v-if="materi.file_materi != null && materi.file_materi.length > 0"
    >
      <div v-for="(item, i) in materi.file_materi" :key="i">
        <h6 class="mb-4">Materi ke {{ ++i }}</h6>
        <h2>{{ item.path }}</h2>
        <iframe
          v-if="getExtFile(item.path) == 'pdf'"
          :src="item.path"
          frameborder="0"
          class="mb-4"
        ></iframe>
        <video
          v-else-if="
            getExtFile(item.path) == 'mp4' ||
            getExtFile(item.path) == 'webm' ||
            getExtFile(item.path) == 'ogg'
          "
          :src="item.path"
          controls
        ></video>
        <audio
          v-else-if="
            getExtFile(item.path) == 'mp3' || getExtFile(item.path) == 'wav'
          "
          :src="item.path"
          controls
        ></audio>
        <vx-card
          v-else-if="['doc', 'docx'].indexOf(getExtFile(item.path)) != -1"
        >
          <div
            class="flex justify-between items-center"
            style="width: auto; overflow-x: auto"
          >
            <div class="flex items-center">
              <vs-button
                icon-pack="feather"
                icon="icon-file"
                type="gradient"
                class="rounded-full text-white mr-6"
              ></vs-button>
              <h5>
                <strong>{{ item.filename }}</strong>
              </h5>
            </div>

            <feather-icon
              @click.prevent="downloadMateri(item.path)"
              icon="DownloadIcon"
              class="text-primary"
            ></feather-icon>
          </div>
        </vx-card>
      </div>
    </div>
    <footer
      class="mb-8 mt-8"
      v-if="materi.link_materi != null && materi.link_materi.length > 0"
    >
      <h4 class="mb-4"><strong>Tautan</strong></h4>
      <div>
        <p
          class="text-primary mb-4"
          v-for="(item, i) in materi.link_materi"
          :key="i"
        >
          <a :href="item.link" target="_blank"
            ><strong>{{ item.link }}</strong></a
          >
        </p>
      </div>
    </footer>
    <footer class="mb-8 mt-8">
      <h4 class="mb-4"><strong>Deskripsi</strong></h4>
      <p v-html="materi.deskripsi"></p>
    </footer>

    <!-- Popup Subscribe -->
    <vs-popup
      background-color="rgba(0, 0, 0, .5)"
      title=""
      :active.sync="showSubscribe"
    >
      <img
        src="@/assets/images/locked.svg"
        alt=""
        class="block mx-auto"
        style="width: 70px"
      />
      <h5 class="mt-8 mb-6 text-center text-primary">
        <strong>Yuk buka materi ini dengan beli paketnya!</strong>
      </h5>
      <div class="w-full text-center mt-8 mb-6">
        <vs-button
          color="warning"
          class="rounded-full text-dark"
          @click.prevent="redirectToPaket"
          >Beli Sekarang</vs-button
        >
      </div>
    </vs-popup>
  </main>
</template>
<style scoped>
iframe {
  width: 100%;
  height: 500px;
}
</style>

<script>
pdfjsLib.GlobalWorkerOptions.workerSrc =
  "https://cdn.jsdelivr.net/npm/pdfjs-dist@2.7.570/build/pdf.worker.min.js";

export default {
  data() {
    return {
      url: null,
      showSubscribe: false,
      loadingTask: pdfjsLib.getDocument("/sample2.pdf"), // async download pdf
      pdfDoc: null,
      pageNum: 1,
      pageRendering: false,
      pageNumPending: null,
      scale: 1.5,
      materi: {
        nama_materi: "Aljabar Bagian 1",
        deskripsi: null,
      },
      pdfDoc: null,
    };
  },
  methods: {
    downloadMateri(path) {
      window.open(path, "_blank");
    },
    getMateri() {
      this.$vs.loading();
      this.$store
        .dispatch("materi/fetchFirst", this.$route.params.id)
        .then((materi) => {
          this.$vs.loading.close();
          this.materi = materi;

          if (materi.file_materi != null && materi.file_materi.length > 0) {
            materi.file_materi.map((item) => {
              item.path = process.env.VUE_APP_API_FILE + "/" + item.file;
              item.filename = item.file.split("/").pop();
            });
            // let file_materi = materi.file_materi[0],
            // 	path_file = process.env.VUE_APP_API_FILE + '/' + file_materi.file

            // this.url = path_file

            // let loadingTask = pdfjsLib.getDocument(this.url),
            // 	vm = this
            // loadingTask.promise.then(function(pdfDoc_) {
            //     vm.pdfDoc = pdfDoc_;
            //     document.getElementById('page_count').textContent = vm.pdfDoc.numPages;

            //     // Initial/first page rendering
            //     vm.renderPage(vm.pageNum);
            // });
          }
        })
        .catch((e) => {
          this.$vs.loading.close();
          if (e.response) console.error(e.response);
          this.$vs.notify({
            title: "Oops, materi tidak dapat dimuat :(",
            text: "",
            color: "warning",
            position: "top-right",
          });

          return false;
        });
    },
    redirectToPaket() {
      this.showSubscribe = false;
      this.$router.push("/paket");
    },
    renderPage(num) {
      let vm = this;
      this.pageRendering = true;
      // Using promise to fetch the page
      this.pdfDoc.getPage(num).then(function (page) {
        var viewport = page.getViewport({ scale: vm.scale });
        // Support HiDPI-screens.
        var outputScale = window.devicePixelRatio || 1,
          canvas = document.getElementById("the-canvas"),
          ctx = canvas.getContext("2d");

        canvas.width = Math.floor(viewport.width * outputScale);
        canvas.height = Math.floor(viewport.height * outputScale);
        canvas.style.width = Math.floor(viewport.width) + "px";
        canvas.style.height = Math.floor(viewport.height) + "px";

        var transform =
          outputScale !== 1 ? [outputScale, 0, 0, outputScale, 0, 0] : null;

        // Render PDF page into canvas context
        var renderContext = {
          canvasContext: ctx,
          transform: transform,
          viewport: viewport,
        };
        var renderTask = page.render(renderContext);

        // Wait for rendering to finish
        renderTask.promise.then(function () {
          vm.pageRendering = false;
          if (vm.pageNumPending !== null) {
            // New page rendering is pending
            renderPage(vm.pageNumPending);
            vm.pageNumPending = null;
          }
        });
      });

      // Update page counters
      document.getElementById("page_num").textContent = num;
    },
    queueRenderPage(num) {
      if (this.pageRendering) {
        this.pageNumPending = num;
      } else {
        this.renderPage(num);
      }
    },
    onNextPage() {
      if (this.pageNum >= this.pdfDoc.numPages) {
        return;
      }

      this.pageNum++;
      this.queueRenderPage(this.pageNum);
    },
    onPrevPage() {
      if (this.pageNum <= 1) {
        return;
      }

      this.pageNum--;
      this.queueRenderPage(this.pageNum);
    },
  },
  computed: {
    getFilePath() {
      return this.path;
    },
  },
  created() {
    if (!this.$route.params.id) {
      this.$router.push("/my-paket");
    }

    this.getMateri();
  },
};
</script>
<style>
#pageContainer {
  margin: auto;
  width: 80%;
}

div.page {
  display: inline-block;
}
</style>