<template>
  <div class="body">
    <!-- <img src="/static/assets/bg2.png" style="z-index:1;position:absolute;right:0;bottom:0" alt=""> -->
    <iframe v-if="showMusic" id="music" frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=1385796832&auto=1&height=66"></iframe>
    <div class="navigator">
      <div @click="toMoeGirl()" class="li">她是谁</div>
      <div @click="showContent = 'imageList'" class="li">二创图列表</div>
      <div @click="showContent = 'blessList'" class="li">祝福列表</div>
      <div @click="showContent = 'thanks'" class="li">鸣谢</div>
    </div>
    <div class="center">
      <img class="main" :style="{'opacity': showMoasic ? 0 : 1}" :src="main" alt=""/>
      <img class="main" :style="{'opacity': showMoasic ? 1 : 0}" :src="main625" alt=""/>
      <img class="masoic" :style="{'opacity': showMoasic ? 0.4 : 0.1}" :src="moasic"/>
      <div @mouseover="showMoasic = true" @mouseleave="showMoasic = false" v-for="(item,index) of grid" @click.stop="clickGrid(index)" v-bind:key="item.id" class="grid"></div>
      <div class="arrow">
        <img src="/static/assets/arrow.png" alt="">
        <div class="suprise">点开每个小图有惊喜哦！</div>
      </div>
    </div>
    <div v-if="currentData" @click="currentData = null" class="blackCover">
      <div class="postcard">
        <div class="image">
          <img :src="`/static/${currentData.url}`" alt="">
          <div class="author" @click.stop="toSpace(currentData.uid)">图：<span :class="currentData.uid ? 'toUid' : ''">{{currentData.author}}</span></div>
        </div>
        <div class="bless" @click.stop="()=>{}">
          <div class="content">{{currentData.content}}</div>
          <div class="dduser">{{currentData.dduser}}</div>
        </div>
      </div>
    </div>
    <div v-if="showContent" @click="showContent = null" class="blackCover">
      <div class="postcard">
        <div @click.stop="()=>{}" v-if="showContent == 'blessList'" class="blessList">
          <div style="border-bottom: 1px solid rgba(0, 0, 0, 0.5);" class="blessItem">
            <div class="dduser">帅老DD</div>
            <div style="text-align:center" class="content">祝福内容</div>
          </div>
          <div v-for="bless of blessList" class="blessItem">
            <div class="dduser">{{bless.dduser}}</div>
            <div class="content">{{bless.content}}</div>
          </div>
        </div>
        <div @click.stop="()=>{}" v-if="showContent == 'thanks'" class="thanks">
          <div class="staff">
            策划、外联、图片算法、网页设计、网站部署及备案
          </div>
          <div class="name">
            琛琛（没错我就是六边形战士！）
          </div>
          <div class="staff">
            制作协力
          </div>
          <div class="name">
            月上弦月下弦、凉城暮暖、烧煤型驱逐舰、不忘那抹天蓝、镜下茗、-SiroccO-、t541h、头快裂开了、窝窝头想要变得好吃、月华锦袖、中国梦点亮星辰大海、克比斯特
          </div>
          <div class="staff">
            画师 or 表情包工匠
          </div>
          <div class="name">
            <span>幽灵子辰（没错大图是他画的）、</span>
            <span v-for="author of authorList">{{author.author}}、</span>
          </div>
          <div class="staff">
            虚研会名场面截图
          </div>
          <div class="name">
            时光荏苒、烧煤型驱逐舰、太阳系系王、么鱼、月上弦月下弦、凉城暮暖、诸葛谭所、骑士贝狄威尔
          </div>
        </div>
        <div @click.stop="()=>{}" v-if="showContent == 'imageList'" class="imageList">
          <div @click="author.showImg = !author.showImg" v-for="author of authorList" class="author">
            <span>{{author.author}}</span>
            <span style="float:right">{{author.showImg?'⮟':'⮞'}}</span>
            <div v-if="author.showImg" class="list">
              <img @click.stop="showImg(img)" v-for="img of filterImgByUid(author.uid)" :src="`/static/${img}`" alt="">
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import _ from "lodash"

import main from "../assets/image/main.png"
import main625 from "../assets/image/main625.png"
import mosaic from "../assets/image/result.png"

import database from "../assets/database"

function preloadImg(url) {
    var img = new Image();
    img.src = url;
    if(img.complete) {
        //接下来可以使用图片了
        //do something here
    }
    else {
        img.onload = function() {
            //接下来可以使用图片了
            //do something here
        };
    }
}

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
      currentData: null,
      database: database,
      blessList: _.uniqBy(database.map(item => { return {dduser: item.dduser,content: item.content} }), 'dduser'),
      showContent: null,
      authorList: _.uniqBy(database.filter(item=>item.uid).map(item => { return {author: item.author,uid: item.uid, showImg: false} }), 'uid'),
      imgList: _.uniqBy(database.filter(item=>item.uid).map(item => { return {author: item.author,uid: item.uid} }), 'uid'),
      showMusic: false
    }
  },
  mounted() {
    this.database.forEach(item => {
      preloadImg(`/static/${item.url}`)
    })
    this.showMusic = true
  },
  methods: {
    toMoeGirl() {
      window.open("https://zh.moegirl.org.cn/%E5%B0%8F%E5%B8%8C(%E8%99%9A%E6%8B%9FUP%E4%B8%BB)",'__blank')
    },
    clickGrid(index) {
      this.currentData = database.find(item=>item.index == index)
    },
    toSpace(uid) {
      if (!uid) return
      window.open(`https://space.bilibili.com/${uid}`,'__blank')
    },
    filterImgByUid(uid) {
      console.log(database.filter(item=>item.uid === uid).map(item=>item.url))
      return _.uniq(database.filter(item=>item.uid === uid).map(item=>item.url))
    },
    showImg(img) {
      window.open(`/static/${img}`,'__blank')
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* 滚动槽 */
::-webkit-scrollbar {
    width: 6px;
    height: 6px;
}
::-webkit-scrollbar-track {
    border-radius: 3px;
    background: rgba(0,0,0,0.06);
    -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.08);
}
/* 滚动条滑块 */
::-webkit-scrollbar-thumb {
    border-radius: 3px;
    background: rgba(0,0,0,0.12);
    -webkit-box-shadow: inset 0 0 10px rgba(0,0,0,0.2);
}
@font-face {
  font-family: 'bhwnn';
  src: url('/static/assets/bhwnn.TTF');
}
.navigator {
  position: absolute;
  left: 150px;
  bottom: 100px;
  font-size: 40px;
  font-family: "bhwnn";
  /* color: white; */
}
.navigator .li:hover {
  text-decoration: underline;
  cursor:pointer;
}
.center {
  width: 80vh;
  height: 80vh;
  margin: auto;
  /* margin-left: 200px; */
  position: relative;
  top: 0;
  left: 0;
  display: flex;
  flex-wrap: wrap;
}
.arrow {
  position: absolute;
  top: 35vh;
  left: 85vh;
}
.arrow img {
  height: 10vh;
}
.suprise {
  position: absolute;
  bottom: -70px;
  left: 0;
  font-size: 30px;
  width: 350px;
  font-family: 'bhwnn';
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
  padding-top: 60px;
  width: 100vw;
  height: 100vh;
  background: #d2e9fb url("/static/assets/bg1.png");
  background-size: 100% 100%;
  overflow: hidden;
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
.blackCover::after {
  content: "×";
  font-size: 100px;
  position: absolute;
  right: 30px;
  top: 15px;
  color:rgba(255, 255, 255, .7);
}
.blackCover .postcard {
  width: 700px;
  height: 400px;
  background-size: 100% 100%;
  display: flex;
  align-items: center;
  padding: 50px;
  background-image:url(/static/assets/postcard.png)
}
.blackCover .postcard .image {
  flex-direction: column;
  display: flex;
  justify-content: center;
  width: 40%;
  height: 100%;
  flex-shrink: 0;
  align-items: center;
  border-right: 1px solid rgba(0, 0, 0, 0.1);
}
.blackCover .postcard .image img {
  width: 80%;
}
.blackCover .postcard .image .author {
  margin-top: 30px;
}
.blackCover .postcard .image .toUid {
  cursor: pointer;
  text-decoration: underline;
}
.blackCover .postcard .bless {
  font-size: 20px;
  flex-direction: column;
  display: flex;
  justify-content: center;
  height: 100%;
  font-family: 'bhwnn';
  padding: 0 50px;
  flex-grow: 1;
}
.blackCover .postcard .bless .content {
  text-align: left;
}
.blackCover .postcard .bless .dduser {
  margin-top: 50px;
  text-align: right;
}
.blackCover img {
  width: 45%;
  flex-shrink: 0;
}
.blessList {
  height: 100%;
  width: 100%;
  overflow-y: scroll;
  overflow-x: hidden;
  text-align: left;
}
.blessList .blessItem {
  display: flex;
  margin-bottom: 10px;
  align-items: center;
  padding-bottom: 5px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}
.blessList .blessItem .dduser {
  width: 30%;
  flex-shrink: 0;
}
.blessList .blessItem .content {
  flex-grow: 1;
}
.thanks {
  height: 80%;
  overflow-x: hidden;
  overflow-y: scroll;
  padding: 0 10px;
}
.thanks .staff {
  font-size: 25px;
  font-weight: 600;
  border-bottom:1px solid rgba(0, 0, 0, 0.2);
  padding-bottom: 10px;
}
.thanks .name {
  margin: 20px 0;
}
.imageList {
  height: 80%;
  overflow-y: scroll;
  overflow-x: hidden;
  width: 100%;
}
.imageList .author {
  font-size: 25px;
  font-weight: 600;
  text-align: left;
  padding: 10px 0;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  cursor: pointer;
}
.imageList .author .list {
  display: flex;
  align-items: flex-start;
  flex-wrap: wrap;
}
.imageList .author .list img{
  width: 200px;
  margin-right: 10px;
  margin-top: 10px;
  cursor: pointer;
}
#music {
  position: absolute;
  right: 100px;
  bottom: 100px;
}
</style>
