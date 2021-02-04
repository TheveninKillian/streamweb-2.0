<script>
export default {
  name: 'App',
  data(){
    return{
      isActive: false
    }
  },
  methods: {
    revealBtnSocial(){
      if(this.isActive === false){
        this.isActive = true
      }else{
        this.isActive = false
      }
    }
  }
}
</script>

<template>
  <div :class="{overlay: isActive === true}" @click="revealBtnSocial"></div>

  <video autoplay loop id="bgvid">
    <source src="./assets/video.webm" type="video/webm">
  </video>

  <header class="header">
    <div class="header__title">
      <img src="./assets/logo.png" alt="logo de mastersnakou">
      <h1>Mastersnakou</h1>
    </div>

    <div class="header__btn" @click="revealBtnSocial">
      <i class="las la-ellipsis-h"></i>
    </div>
  </header>
  
  <nav class="btn-social" :class="{active: isActive}">
    <div class="btn-social__twitch"><i class="lab la-twitch"></i></div>
    <div class="btn-social__twitter"><i class="lab la-twitter"></i></div>
    <div class="btn-social__facebook"><i class="lab la-facebook-f"></i></div>
    <div class="btn-social__youtube"><i class="lab la-youtube"></i></div>
    <div class="btn-social__instagram"><i class="lab la-instagram"></i></div>
  </nav>
</template>

<style lang="scss">
  @import './assets/variables';

  .overlay{
    position: fixed;
    top: 0;
    left: 0;
    z-index: 1;

    height: 100%;
    width: 100%;

    background: rgba(0, 0, 0, 0.4);
  }

  video{
    position: fixed;
    left: -50%;

    min-width: 100%;
    height: 100%;

    z-index: -1;

    background-color: #0a090a;
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
        top: -5px;
        color: $colorPrimary;
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
    z-index: 2;

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
