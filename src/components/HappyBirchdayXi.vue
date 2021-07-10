<template>
  <div class="body">
    <div class="center" @mouseover="showMoasic = true" @mouseleave="showMoasic = false">
      <img class="main" :style="{'opacity': showMoasic ? 0 : 1}" :src="main" alt=""/>
      <img class="main" :style="{'opacity': showMoasic ? 1 : 0}" :src="main625" alt=""/>
      <img class="masoic" :style="{'opacity': showMoasic ? 0.4 : 0.1}" :src="moasic"/>
      <div v-for="(item,index) of grid" @click.stop="clickGrid(index)" v-bind:key="item.id" class="grid"></div>
    </div>
    <div v-if="currentImage" @click="currentImage = null" class="blackCover">
      <img :src="`/static/${currentImage}`" alt="">
    </div>
  </div>
</template>

<script>
import main from "../assets/image/main.png"
import main625 from "../assets/image/main625.png"
import mosaic from "../assets/image/result.png"

import database from "../assets/database"

export default {
  name: 'HappyBirchdayXi',
  data () {
    let grid = new Array(625).fill([])
    return {
      grid,
      main: main,
      moasic: mosaic,
      main625: main625,
      showMoasic: false,
      showImage: true,
      currentImage: "",
      database: database
    }
  },
  methods: {
    clickGrid(index) {
      this.currentImage = database.find(item=>item.index == index).url
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.center {
  width: 80vh;
  height: 80vh;
  margin: auto;
  position: relative;
  top: 0;
  left: 0;
  display: flex;
  flex-wrap: wrap;
}
.main {
  filter: blur(1px);
  transition: 0.5s all;
}
.grid {
  width: 4%;
  height: 4%;
  background: white;
  opacity: 0.3;
  position: relative;
  z-index: 2;
}
.grid:hover {
  background: black;
  
}
.main, .masoic {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.masoic {
  transition: 0.5s all;
  /* filter: blur(1px) */
}
.body {
  width: 100vw;
  text-align: center;
}
.blackCover {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 4;
}
.blackCover img {
  width: 500px;
}
</style>
