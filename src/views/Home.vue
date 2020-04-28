/* eslint-disable prettier/prettier */
<template>
  <div class="home">
    <input @click="load" type="button" value="Load Image" />
    <br />
    <img v-if="show && publicUrl" class="publicImg" :src="publicUrl" alt="" />
    <p v-else-if="show">Loading ...</p>
  </div>
</template>

<script>
// @ is an alias to /src
var Minio = require("minio");

const Home = {
  name: "Home",
  data() {
    return {
      publicUrl: "",
      show: false
    };
  },
  mounted() {
    this.minioClient = new Minio.Client({
      endPoint: "minio.nostratech.com",
      port: 80,
      useSSL: false,
      accessKey: "admin",
      secretKey: "Welcome1$"
    });
  },
  methods: {
    load() {
      this.show = !this.show;
      let base64Data = new Promise((resolve, reject) => {
        let data64 = "";
        this.minioClient.getObject(
          "aspro",
          "36383dbc-8fde-49c1-acc8-36ec9a654bb5.png",
          function(err, dataStream) {
            if (err) {
              return console.log(err);
            }
            dataStream.on("data", function(obj) {
              console.log("data");
              data64 = btoa(String.fromCharCode.apply(null, obj));
            });
            dataStream.on("end", function() {
              console.log("end");
              resolve(data64);
            });
            dataStream.on("error", function(err) {
              reject(err);
              console.log(err);
            });
          }
        );
      });

      base64Data
        .then(response => {
          console.log(response);
          this.publicUrl = `data:image/png;base64,${response}`;
        })
        .catch(err => console.log(err));
    }
  }
};

export default Home;
</script>

<style scoped>
.publicImg {
  transition: 1s;
}
</style>
