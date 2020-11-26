<template>
  <div class="game">
    <div class="wrap">
      <div class="rank">
          <div class="title">倒计时</div>
          <div class="value">{{time}}s</div>
          <div class="title">当前关卡</div>
          <div class="value">第{{level}}关</div>
          <div class="title">排行榜</div>
          <div class="value">
            <div>😄1.童捷</div>
            <div>🍝2.徐晨</div>
            <div>⚾3.张三</div>
            <div>🍑4.李四</div>
            <div>🎀5.王五</div>
            <div>😄1.童捷</div>
            <div>🍝2.徐晨</div>
            <div>⚾3.张三</div>
            <div>🍑4.李四</div>
            <div>🎀5.王五</div>
          </div>
      </div>
      <div class="flex">
        <div class="back" v-if="status === 'pause'">
          <p @click="handleStartGame">开始游戏</p>
        </div>
         <div class="back" v-if="status === 'again'">
          <p @click="handleStartGame">重新开始</p>
        </div>
        <div class="img" v-for="(item,index) in choose" :key="index" :style="{height,width:height}">
          <img :src="item.url" @click="handleChooseItem(item)">
          <div v-if="item.choose === 1" class="choose">
            <div class="innerChoose"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ycy1 from '../assets/img/ycy/1.jpg'
import ycy2 from '../assets/img/ycy/2.jpg'
import ycy3 from '../assets/img/ycy/3.jpg'
import ycy4 from '../assets/img/ycy/4.jpg'
import notycy1 from '../assets/img/not_ycy/1.jpg'
import notycy2 from '../assets/img/not_ycy/2.jpg'
import notycy3 from '../assets/img/not_ycy/3.jpg'
import notycy4 from '../assets/img/not_ycy/4.jpg'
export default {
  name: 'game',
  props: {
    msg: String
  },
  data() {
    return {
      level:1,
      time:10,
      num:9,
      timer:null,
      status:'pause',
      list:[
        {
          type:'ycy',
          url:ycy1
        },
        {
          type:'ycy',
          url:ycy2
        },
        {
          type:'ycy',
          url:ycy3
        },
        {
          type:'ycy',
          url:ycy4
        },
        {
          type:'not_ycy',
          url:notycy1
        },
        {
          type:'not_ycy',
          url:notycy2
        },
        {
          type:'not_ycy',
          url:notycy3
        },
        {
          type:'not_ycy',
          url:notycy4
        },
      ],
      choose:[]
    }
  },
  created() {

  },
  computed:{
    height() {
      if(this.num === 9) {
        return 'calc(80vh / 3)'
      }else if(this.num === 16) {
        return 'calc(80vh / 4)'
      }else if(this.num === 25) {
        return 'calc(80vh / 5)'
      }
    },
  },
  methods:{
    handleStartGame() {
      this.status = 'start'
      this.handleStartStage()
    },
    handleRandomImage() {
      this.choose = []
      const list = JSON.parse(JSON.stringify(this.list))
      for(let i = 0;i< this.num;i++) {
        const num = Math.floor( Math.random() * 8 )
        this.choose.push({...list[num],choose:0})
      }
    },
    handleStartStage() {
      if(this.level < 5) {
        this.time = 10
        this.num = 9
      }else if(this.level < 10) {
        this.time = 8
        this.num = 16
      }else if(this.level < 15) {
        this.time = 5
        this.num = 16
      }else if(this.level <20) {
        this.time = 5
        this.num = 25
      }else {
        this.time = 3
        this.num = 25
      }
      this.handleRandomImage()
      this.timer = setInterval(() =>{
        -- this.time
        if(this.time === 0) {
          clearInterval(this.timer)
          const success = this.handleGetResultStatus()
          if(success) {
            ++ this.level
            setTimeout(() => {
              this.handleStartStage()
            },100)
          }else {
            this.choose = []
            this.status = 'again'
            this.level = 1
          }
        }
      },1000)
    },
    handleChooseItem(item) {
      item.choose = item.choose === 0 ? 1 : 0
      const success = this.handleGetResultStatus()
      if(success) {
        clearInterval(this.timer)
        ++ this.level
        setTimeout(() => {
          this.handleStartStage()
        },100)
      }

    },
    handleGetResultStatus() {
      let success = true
          for(let i of this.choose) {
            if(i.type === 'ycy' && i.choose === 0) {
              success = false
              break
            }
            if(i.type === 'not_ycy' && i.choose === 1) {
              success = false
              break
            }
          }
        return success
    }
  }
}
</script>
<style>
    .game{
        height:80vh;
        width:95vh;
        margin:auto;
        top:0;
        left:0;
        right:0;
        bottom:0;
        position:absolute;
        box-shadow: 0 3px 1px -2px rgba(0,0,0,.2),0 2px 2px 0 rgba(0,0,0,.14),0 1px 5px 0 rgba(0,0,0,.12)
    }
    .wrap{
      display: flex;

    }
    .back{
      position: absolute;
      top:0;
      left:15vh;
      height:100%;
      width:calc( 100% - 15vh);
      background:white;
      opacity: 1;
      z-index: 1000;
    }
    .back p{
      position: absolute;
      height:25px;
      width:120px;
      top:0;
      left:0;
      bottom:0;
      right:0;
      margin:auto;
      background:pink;
      border-radius: 10px;
      padding:10px 5px;
      color:white;
      font-size:20px;
      line-height:25px;
      cursor: pointer;
    }
    .flex{
      display:flex;
      flex-wrap: wrap;
      width:80vh;
    }
     .rank{
        height:80vh;
        width:15vh;
        background:white;
    }
    ul{
        list-style: none;
        flex-direction: column;
        align-items: center;
        padding:0;
        margin:0
    }
    li{
        text-align: center
    }
    .title{
      padding:5px 10px;
      margin: 5px 30px;
      color:rgba(0,0,0,.6);
      font-size:14px;
      border-bottom:2px solid red
    }
    .value{
      padding:5px 0 15px;
      color:rgba(0,0,0,.6);
      font-size:14px;
    }
    .value div{
      margin-bottom:10px
    }
    .img{
      width:33%;
      height:calc(80vh / 3);
      flex-grow: 1;
      position: relative;
    }
    .img img {
      width:100%;
      height:101%
    }
    .choose{
      position: absolute;
      top:0;
      right:0;
      background:greenyellow;
      border-radius: 50%;
      height:20px;
      width:20px;
      display:flex;
      justify-content: center;
      align-items: center;
    }
    .innerChoose{
      height:10px;
      width:10px;
      background:white;
      border-radius: 50%;
    }
</style>