<template>
  <!-- Ne pas oublier de changer le parent avant le deploiement : streamweb.netlify.app -->
  <iframe loading="lazy" class="player" src="https://player.twitch.tv/?channel=mastersnakou&parent=streamweb.netlify.app" allowfullscreen="true"></iframe>

  <div class="twitch-details" v-if="live" :class="{active: isActive}">
    <div class="twitch-details__logo">
      <img :src="twitch.gameImg" alt="logo-game">
    </div>

    <div class="twitch-details__title">
      <p>{{ twitch.title }}</p>
      <p>{{ twitch.game }}</p>
    </div>

    <div class="hide-details" @click="revealDetails">
      <i class="las la-chevron-down" v-if="!isActive"></i>
      <i class="las la-chevron-up" v-if="isActive"></i>
    </div>
  </div>

  <iframe loading="lazy" class="chat" src="https://www.twitch.tv/embed/mastersnakou/chat?parent=streamweb.netlify.app&darkpopout"></iframe>
</template>

<script>
  export default {
    name: 'EmbedTwitch',
    data(){
      return{
        client_id: 'hjupgzhnkwgkiozsesy4fxby3337t5',
        // Ne pas oublie de modifier redirect_uri avant le deploiement https://streamweb.netlify.app : http://localhost:8080
        redirect_uri: 'https://streamweb.netlify.app',
        scopes: ['user:read:email'],
        channel: 'mastersnakou',
        live: false,
        initLive: null,
        twitch_last_follow: null,
        isActive: false,
        helpers: {
          encodeQueryString: (params) => {
            const queryString = new URLSearchParams();
            for (let paramName in params) {
              queryString.append(paramName, params[paramName]);
            }
            return queryString.toString();
          },

          decodeQueryString: (string) => {
            const params = {};
            const queryString = new URLSearchParams(string);
            for (let [paramName, value] of queryString) {
              params[paramName] = value;
            }
            return params;
          },

          getUrlParams: () => {
            return this.helpers.decodeQueryString(location.hash.slice(1));
          },
        },
        twitch: {
          isAuthenticated: () => {
            const params = this.helpers.getUrlParams();
            return params['access_token'] !== undefined;
          },
          authentication: () => {
            const params = {
              client_id: this.client_id,
              redirect_uri: this.redirect_uri,
              response_type: 'token',
              scope: this.scopes.join(' '),
            };
            const queryString = this.helpers.encodeQueryString(params);
            const authenticationUrl = `https://id.twitch.tv/oauth2/authorize?${queryString}`;
            location.href = authenticationUrl;
          },
          title: '',
          game: '',
          gameImg: ''
        },
        headerFetch: () => {
          let BEARER_TOKEN = this.helpers.getUrlParams();

          let myHeaders = new Headers();
          myHeaders.append('Client-Id', 'hjupgzhnkwgkiozsesy4fxby3337t5');
          myHeaders.append('Authorization', `Bearer ${BEARER_TOKEN.access_token}`);

          let myInit = {
            method: 'GET',
            headers: myHeaders,
          };

          return myInit;
        },
        getTwitch: {
          userId: async () => {
            try {
              let response = await fetch(
                `https://api.twitch.tv/helix/users?login=${this.channel}`,
                this.headerFetch()
              );

              if (response.ok) {
                let dataUser = await response.json();

                return dataUser.data[0].id;
              } else {
                console.error('Retour du serveur : ' + response.status);
              }
            } catch (e) {
              console.error(e);
            }
          },

          streamInfo: async () => {
            let id = await this.getTwitch.userId();

            try {
              let response = await fetch(
                `https://api.twitch.tv/helix/streams?user_id=${id}`,
                this.headerFetch()
              );

              if (response.ok) {
                let dataStream = await response.json();

                if(dataStream.data[0] !== undefined){
                  this.live = true
                  return dataStream.data[0]
                }else{
                  this.live = false
                  return []
                }
              } else {
                console.error('Retour du serveur : ' + response.status);
              }
            } catch (e) {
              console.error(e);
            }
          },
          
          gameInfo: async (gameId) => {
            try {
              let response = await fetch(
                `https://api.twitch.tv/helix/games?id=${gameId}`,
                this.headerFetch()
              );

              if (response.ok) {
                let dataGame = await response.json();

                return dataGame.data[0];
              } else {
                console.error('Retour du serveur : ' + response.status);
              }
            } catch (e) {
              console.error(e);
            }
          }
        },
        callTwitch: () => {
          this.getTwitch.streamInfo()
            .then((data) => {
              if(data.length !== 0){
                this.twitch.title = data.title
                this.twitch.game = data.game_name

                this.getTwitch.gameInfo(data.game_id)
                  .then((dataGame) => {
                    let urlImg = dataGame.box_art_url
                    urlImg = urlImg.replace('{width}', '')
                    urlImg = urlImg.replace('{height}', '')
                    
                    this.twitch.gameImg = urlImg
                  })
              }
            })
        },
        timer: setInterval(() => {
          this.getTwitch.streamInfo()

          if(this.initLive !== this.live){
            this.initLive = this.live

            this.callTwitch()
          }else if (this.live === true){
            this.callTwitch()
          }
        }, 30 * 1000)
      }
    },
    methods:{
      revealDetails(){
        if(this.isActive === false){
          this.isActive = true
        }else{
          this.isActive = false
        }
      }
    },
    created(){
      if (!this.twitch.isAuthenticated()) {
        this.twitch.authentication();
      } else {
        console.log("L'utilisateur a bien autorisé l'application !");
      }
    },
    beforeMount(){
      this.callTwitch()

      this.initLive = this.live
    },
    mounted(){
      this.timer
    },
    beforeUnmount(){
      clearInterval(this.timer)
    }
  }
</script>

<style lang="scss">
  @import '../assets/variables';

  .player{
    position: absolute;
    top: 75px;
    
    height: 32%;
    width: 100%;

    border: none;
  }

  .twitch-details{
    position: absolute;
    top: calc((32% + 75px) - 85px);
    transition: top .3s;
    z-index: -1;

    display: flex;
    align-items: center;
    padding: 0px 5px;
    height: 85px;
    width: 100%;

    background-color: $colorBlackTwo;
    color: #fff;

    &.active{
      top: calc(32% + 75px);
    }

    &__logo {
      width: 60px;
      height: 75px;

      img{
        width: 60px;
        height: 75px;
      }
    }

    &__title{
      margin-left: 10px;

      p:nth-child(1){
        font-size: 13px;
      }

      p:nth-child(2){
        margin-top: 5px;
        color: $colorPrimary;
        font-size: 12px;
      }
    }

    .hide-details{
      position: absolute;
      bottom: -35px;
      right: 15px;

      height: 35px;
      line-height: 36px;
      width: 35px;

      background-color: $colorBlackTwo;

      text-align: center;

      i{
        color: $colorPrimary;
        font-size: 20px;
      }
    }
  }

  .chat{
    position: absolute;
    top: calc(32% + 75px);
    z-index: -2;

    height: calc(100% - (75px + 32%));
    width: 100%;

    border: none;
  }
</style>