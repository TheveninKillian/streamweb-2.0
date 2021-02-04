<template>
  <iframe src="https://player.twitch.tv/?channel=mastersnakou&parent=localhost" allowfullscreen="true"></iframe>

  <div class="twitch-details">
    <div class="twitch-details__logo">{{ twitch.logo }}</div>
    <div class="twitch-details__title">{{ twitch.title }}</div>
    <div class="twitch-details__game">{{ twitch.game }}</div>
  </div>
</template>

<script>
  export default {
    name: 'EmbedTwitch',
    data() {
      return {
        twitch: {
          logo: 'logo',
          title: 'title',
          game: 'game'
        }
      }
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

    color: #fff;
  }
</style>