<template>
  <div class="body">
    <div v-if="!hideLoading" class="loading">
      <div v-if="goumienasai" style="display:flex;align-items:center;justify-content:center;flex-direction:column">
        <div style="font-family:'bhwnn';font-size:10vw;padding:0 10vw">抱歉！由于技术力和时间的问题，没有做手机的适配，请到电脑上看吧！</div>
        <div style="font-family:'bhwnn';font-size:1vw">但是不忙的话，请和我一起大声喊：希爹是我爹，我是希爹儿！</div>
      </div>
      <div v-else>
        <img class="d-block" src="/static/assets/sunny-light.svg" style="width:96px;opacity:0.5">
        <div v-if="loading" class="loadingText">加载中，请稍候({{Math.floor(imgloaded / database.length * 100)}}%)……</div>
        <div v-else @click="hideLoading = true" class="loaded">进入</div>
      </div>
    </div>
    <div class="decoration">
      <img src="/static/assets/title.png" alt="" class="title">
      <img src="/static/assets/leftxi.png" alt="" class="leftXi">
      <img src="/static/assets/rightxi.png" alt="" class="rightXi">
      <img src="/static/assets/seal.png" alt="" class="seal">
      <img src="/static/assets/upDe.png" alt="" class="upDe">
      <img src="/static/assets/downDe.png" alt="" class="downDe">
    </div>
    <audio ref="supriseMusic" src="/static/assets/suprise.ogg"></audio>
    <audio v-if="showSurpirse" autoplay src="/static/assets/suprise.ogg"></audio>
    <!-- <img src="/static/assets/bg2.png" style="z-index:1;position:absolute;right:0;bottom:0" alt=""> -->
    <iframe v-if="hideLoading && showMusic" id="music" frameborder="no" border="0" marginwidth="0" marginheight="0" style="width:36vh" height=86 src="//music.163.com/outchain/player?type=2&id=1385796832&auto=1&height=66"></iframe>
    <div class="navigator">
      <div @click="toMoeGirl()" class="li">她是谁</div>
      <div @click="showContent = 'imageList'" class="li">二创图列表</div>
      <div @click="showContent = 'blessList'" class="li">祝福列表</div>
      <div @click="showContent = 'thanks'" class="li">鸣谢</div>
    </div>
    <div class="center">
      <div class="supriseBless">
        {{supriseBless}}
      </div>
      <img v-if="showResetPage" @click="resetPage()" src="/static/assets/reset.png" class="resetIcon" alt="">
      <img class="main" :style="{'opacity': showMoasic ? 0 : 1}" :src="main" alt=""/>
      <img class="main" :style="{'opacity': showMoasic ? 1 : 0}" :src="main625" alt=""/>
      <img class="masoic" :style="{'opacity': showMoasic ? 0.4 : 0.1}" :src="moasic"/>
      <div :style="{'background-position':`${index % 25 * 4}% ${Math.floor(index / 25) * 4}%`}" :class="{'gridRead':item,'showBackground':item}" @mouseover="showMoasic = true" @mouseleave="showMoasic = false" v-for="(item,index) of grid" @click.stop="clickGrid(index)" v-bind:key="item.id" class="grid"></div>
      <div v-if="!showSurpirse" class="arrow">
        <img src="/static/assets/arrow.png" alt="">
        <div class="suprise">点开每个小图有惊喜哦！</div>
      </div>
      <div v-if="grid.some(item=>item) && !showSurpirse" class="btns">
        <div @click.stop="happyBirthDay()" class="btn">懒得翻辣！直接翻开吧！</div>
        <div @click.stop="reset()" class="btn">我要重新翻！</div>
      </div>
    </div>
    <div v-if="currentData" @click="hideImg()" class="blackCover">
      <!-- <div @click="before()" class="before">❮</div>
      <div @click="next()" class="next">❯</div> -->
      <div class="postcard">
        <div class="image">
          <img @click="showImg(currentData.url)" style="cursor:pointer" :src="`/static/${currentData.url}`" alt="">
          <div class="author" @click.stop="toSpace(currentData.uid)">图：<span :class="currentData.uid ? 'toUid' : ''">{{currentData.author}}</span></div>
        </div>
        <div class="bless" @click.stop="()=>{}">
          <div class="content">{{currentData.content}}</div>
          <div class="dduser">{{currentData.dduser}}</div>
        </div>
      </div>
    </div>
    <div v-if="showContent" @click="showContent = null" class="blackCover" style="z-index:7">
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
            <span>幽灵子辰（没错大图是他画的）、_真名_（标题Q版小希）、凉城暮暖（标题制作）</span>
          </div>
          <div class="name">
            <span v-for="author of authorList">{{author.author}}、</span>
          </div>
          <div class="staff">
            虚研会名场面截图
          </div>
          <div class="name">
            （抱歉虽然后来企划变更没有用上……但是感谢你们的付出）
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
    <a id="beian" href="https://beian.miit.gov.cn/" target="_blank">浙ICP备2021021403号-1</a>
  </div>
</template>

<script>

import _ from "lodash"

import main from "../assets/image/main.png"
import main625 from "../assets/image/main625.png"
import mosaic from "../assets/image/result.png"

import database from "../assets/database"

function preloadImg(url,cb) {
    var img = new Image();
    img.src = url;
    if(img.complete) {
        //接下来可以使用图片了
        //do something here
        cb()
    }
    else {
        img.onload = function() {
            //接下来可以使用图片了
            //do something here
            cb()
        };
        img.onerror = function() {
            //接下来可以使用图片了
            //do something here
            cb()
        };
    }
}

export default {
  name: 'HappyBirchdayXi',
  data () {
    let grid = JSON.parse(localStorage.getItem('grid')) || new Array(625).fill(false)
    return {
      grid,
      loading: true,
      hideLoading: false,
      imgloaded: 0,
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
      showSurpirse: false,
      showMusic: true,
      supriseBless: "",
      showResetPage: false,
      goumienasai: false
    }
  },
  mounted() {
    if (window.innerHeight > innerWidth) {
      this.goumienasai = true
    } else {
      this.database.forEach(item => {
        preloadImg(`/static/${item.url}`,()=>{
          this.imgloaded++
          if (this.imgloaded === this.database.length) {
            this.loading = false
          }
        })
        preloadImg("/static/assets/postcard.png",()=>{})
      })
    }
    this.$refs.supriseMusic.muted = true
    this.$refs.supriseMusic.play()
  },
  methods: {
    toMoeGirl() {
      window.open("https://zh.moegirl.org.cn/%E5%B0%8F%E5%B8%8C(%E8%99%9A%E6%8B%9FUP%E4%B8%BB)",'__blank')
    },
    clickGrid(index) {
      if (this.showSurpirse) return
      this.currentData = database.find(item=>item.index == index)
      let url = this.database.find(item=>index === item.index).url
      this.database.filter(item=>item.url === url).forEach(item => {
        this.grid[item.index] = true
      })
      // this.grid = this.grid.map(item=>true)
      // this.grid[624] = false
      localStorage.setItem('grid',JSON.stringify(this.grid))
    },
    // before() {
    //   let currentIndex = this.currentData.index
    //   if (currentIndex == 0) return
    //   if (this.grid.some)
    // },
    // after() {
    //   if (this.currentData.index == this.grid.length) return

    // },
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
    },
    hideImg() {
      this.currentData = null
      if(!this.grid.some(item => item == false)) {
        this.happyBirthDay()
      }
    },
    reset() {
      this.supriseBless = ""
      this.grid = new Array(625).fill(false)
      localStorage.setItem('grid',JSON.stringify(this.grid))
    },
    happyBirthDay() {
      // this.$refs.music.pause()
      this.showMusic = false
      // this.$refs.supriseMusic.currentTime = 0
      // this.$refs.supriseMusic.muted = false
      let bless = `选择了一幅你自己的画作为礼物,这幅画让我想到那个因为整晚没有击败盖侬而哭鼻子的小希，她却不知道出了台地就单挑盖侬已经击败了多少玩家,看着这个小希的背影,我知道她有时会很脆弱，但往往最后都会打起精神，甚至眼泪还没擦干，就会像这幅画一样，坚定的勇往直前。小希，四岁生日快乐！愿你一如既往，愿你心有暖阳~`
      let splitBless = bless.split("")
      let index = 0
      let interval = setInterval(() => {
        if (!splitBless[index]) {
          clearInterval(interval)
          this.showResetPage = true
          return
        }
        this.supriseBless += splitBless[index]
        index++
      },300)
      this.showSurpirse = true
      this.grid = new Array(625).fill(true)
      localStorage.setItem('grid',JSON.stringify(new Array(625).fill(false)))
    },
    resetPage() {
      location.href = location.href
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
.loading {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  z-index: 9999;
  background: #ee9ca7;  /* fallback for old browsers */
  background: -webkit-linear-gradient(to right, #ffdde1, #ee9ca7);  /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(to right, #ffdde1, #ee9ca7); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
.loadingText {
  font-size: 30px
}
.loaded {
  font-family: 'bhwnn';
  font-size: 30px;
  cursor: pointer;
}
@font-face {
  font-family: 'bhwnn';
  src: url('/static/assets/bhwnn.TTF');
}
.navigator {
  position: absolute;
  left: 30vh;
  bottom: 23vh;
  font-size: 5vh;
  font-family: "bhwnn";
  z-index: 3;
}
.navigator .li:hover {
  text-decoration: underline;
  cursor:pointer;
}
.center {
  width: 70vh;
  height: 70vh;
  margin: auto;
  /* margin-left: 200px; */
  position: relative;
  top: 0;
  left: 0;
  display: flex;
  flex-wrap: wrap;
  z-index: 4;
}
.supriseBless {
  position: absolute;
  padding: 0 50px;
  top: 30%;
  z-index: 5;
  font-size: 3vh;
  font-family: 'bhwnn';
  text-align: left;
}
.before, .next {
  height: 100%;
  font-size: 50px;
  color:rgba(255, 255, 255, .7);
  position: fixed;
  top: 0;
  display: flex;
  align-items: center;
  cursor: pointer;
  padding: 0 50px;
}
.before {
  left: 0;
}
.next {
  right: 0;
}
.resetIcon {
  width: 50px;
  height: 50px;
  position: absolute;
  left: calc(50% - 25px);
  bottom: 10%;
  cursor: pointer;
  z-index: 5;
}
.arrow {
  position: absolute;
  top: 30vh;
  left: 75vh;
}
.arrow img {
  height: 10vh;
}
.suprise {
  position: absolute;
  bottom: -7vh;
  left: 0;
  font-size: 4vh;
  width: 45vh;
  font-family: 'bhwnn';
}
.btns {
  position: absolute;
  top: 10vh;
  left: 75vh;
  white-space: nowrap;
}
.btns .btn {
  font-family: 'bhwnn';
  font-size: 4vh;
  text-align: left;
  margin-top: 20px;
  cursor:pointer;
}
.btns .btn:hover {
  text-decoration: underline;
}
.main {
  filter: blur(1px);
  transition: 0.5s all;
}
.grid {
  width: 4%;
  height: 4%;
  background: white;
  background-size: cover;
  opacity: 0.3;
  position: relative;
  z-index: 2;
}
.grid:hover {
  background-color: black;
}
.gridRead {
  background-image: url('/static/assets/图.jpg');
  opacity: 0.1;
  transition: all 1s;
}
.gridRead:hover {
  background-color: transparent;
}
.showBackground {
  opacity: 1;
  background-size: 80vh 80vh;
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
  height: 100vh;
  background: pink url("/static/assets/bg1.png");
  background-size: 100% 100%;
  overflow: hidden;
  display: flex;
  align-items: center;
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
  padding: 0 0 0 10px;
  flex-grow: 1;
}
.blackCover .postcard .bless .content {
  text-align: left;
}
.blackCover .postcard .bless .dduser {
  margin-top: 10px;
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
  left: 30vh;
  bottom: 10vh;
  z-index: 3;
}
#beian {
  position: absolute;
  bottom: 10px;
  width: 100%;
  text-align: center;
  color: #ee9ca7;
  font-family: 'bhwnn';
  z-index: 3;
}
.decoration {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.decoration .upDe,.decoration .downDe {
  width: 100%;
  position: absolute;
  left: 0;
  z-index: 1;
}
.decoration .upDe {
  top: -10px;
}
.decoration .downDe {
  bottom: -20px;
}
.decoration .title {
  height: 36vh;
  position: absolute;
  left: 3vh;
  top: 7vh;
  z-index: 2;
}
.decoration .leftXi {
  position: absolute;
  bottom: 2.5vh;
  left: 0;
  height: 40vh;
}
.decoration .rightXi {
  position: absolute;
  bottom: 2.5vh;
  right: 5vh;
  height: 42vh;
}
.decoration .seal {
  height: 28vh;
  position: absolute;
  top: 5vh;
  right: 0;
  z-index: 2;
}
</style>
