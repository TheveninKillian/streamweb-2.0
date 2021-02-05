<template>
  <!-- Ne pas oublier de changer le parent avant le deploiement : streamweb.netlify.app -->
  <iframe src="https://player.twitch.tv/?channel=mastersnakou&parent=streamweb.netlify.app" allowfullscreen="true"></iframe>

  <div class="twitch-details" v-if="live">
    <div class="twitch-details__logo">
      <img :src="twitch.gameImg" alt="logo-game">
    </div>

    <div class="twitch-details__title">
      <p>{{ twitch.title }}</p>
      <p>{{ twitch.game }}</p>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'EmbedTwitch',
    data(){
      return{
        client_id: 'hjupgzhnkwgkiozsesy4fxby3337t5',
        redirect_uri: 'https://streamweb.netlify.app',
        scopes: ['user:read:email'],
        channel: 'mastersnakou',
        live: false,
        initLive: null,
        twitch_last_follow: null,
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

                    console.log(urlImg)
                    
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
  iframe{
    position: absolute;
    top: 75px;
    
    height: 32%;
    width: 100%;

    border: none;
  }

  .twitch-details{
    position: absolute;
    top: calc(32% + 75px);

    display: flex;
    padding: 15px 5px;
    width: 100%;

    background-color: #0a090a96;
    color: #fff;

    &__logo img{
      width: 85px;
      height: 100px;
    }
  }
</style>