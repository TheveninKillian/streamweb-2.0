<script>
import EmbedTwitch from './components/EmbedTwitch'

export default {
  name: 'App',
  components: {
    EmbedTwitch
  },
  data(){
    return{
      isActive: false,
      sizeWidth: 0
    }
  },
  mounted(){
    this.screenSizeWidth()

    window.addEventListener("resize", this.screenSizeWidth);
  },
  methods: {
    revealBtnSocial(){
      if(this.isActive === false){
        this.isActive = true
      }else{
        this.isActive = false
      }
    },

    screenSizeWidth(){
      this.sizeWidth =
        window.innerWidth ||
        document.documentElement.clientWidth ||
        document.body.clientWidth;

      // console.log(this.sizeWidth)
    }
  },
  beforeUnmount(){
    window.removeEventListener("resize", this.screenSizeWidth);
  }
}
</script>

<template>
  <div :class="{overlay: isActive === true}" @click="revealBtnSocial" v-if="sizeWidth < 1024"></div>

  <video autoplay loop id="bgvid" v-if="sizeWidth >= 1024">
    <source src="./assets/video.webm" type="video/webm">
  </video>

  <header class="header">
    <div class="header__title">
      <img src="./assets/logo.png" alt="logo de mastersnakou">
      <h1>Mastersnakou</h1>
    </div>

    <div class="header__btn" @click="revealBtnSocial" v-if="sizeWidth < 1024">
      <i class="las la-ellipsis-h"></i>
    </div>
  </header>
  
  <nav class="btn-social" :class="{active: isActive}" v-if="sizeWidth < 1024">
    <div class="btn-social__twitch">
      <a href="http://www.twitch.tv/mastersnakou"><i class="lab la-twitch"></i></a>
    </div>

    <div class="btn-social__twitter">
      <a href="https://twitter.com/MasterSnakou"><i class="lab la-twitter"></i></a>
    </div>

    <div class="btn-social__facebook">
      <a href="https://www.facebook.com/MasterSnakou"><i class="lab la-facebook-f"></i></a>
    </div>

    <div class="btn-social__youtube">
      <a href="https://www.youtube.com/user/MasterSnakou"><i class="lab la-youtube"></i></a>
    </div>

    <div class="btn-social__instagram">
      <a href="https://www.instagram.com/MasterSnakou"><i class="lab la-instagram"></i></a>
    </div>
  </nav>

  <EmbedTwitch />
</template>

<style lang="scss">
  @import './assets/variables';

  .overlay{
    position: fixed;
    top: 0;
    left: 0;
    z-index: 5;

    height: 100%;
    width: 100%;

    background: transparent;
  }

  video{
    position: fixed;
    left: -50%;

    min-width: 100%;
    height: 100%;

    z-index: -1;
  }

  .header{
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 70px;

    color: #fff;

    &__title{
      display: flex;
      flex-direction: column;
      align-items: center;

      margin-top: 5px;

      img{
        width: 45px;
      }

      h1{
        position: relative;
        color: $colorPrimary;

        text-transform: uppercase;
        font-style: italic;
      }
    }

    &__btn{
      position: absolute;
      right: 15px;

      cursor: pointer;

      i{
        font-size: 25px;
      }
    }
  }

  .btn-social{
    position: absolute;
    top: -70px;
    z-index: 6;

    display: flex;
    align-items: center;
    justify-content: center;
    height: 70px;
    width: 100%;

    background: $colorBlack;

    transition: top .3s;

    &.active{
      top: 0;
    }

    div{
      margin: 0 10px;
    }

    i{
      color: #fff;
      transition: color .3s;

      font-size: 25px;

      &:hover{
        color: $colorPrimary;
      }
    }
  }

  @media screen and (orientation: landscape){
    video{
      left: 0;
    }
  }
</style>
