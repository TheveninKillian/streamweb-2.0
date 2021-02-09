<template>
  <div class="wrapper-twitter" :class="{ active: activeFeed }">
    <div class="wrapper-twitter__feed">
      <a
        class="twitter-timeline"
        data-theme="dark"
        href="https://twitter.com/MasterSnakou"
      ></a>
    </div>

    <div class="wrapper-twitter__btn" @click="revealFeed">
      <i class="lab la-twitter"></i>
    </div>
  </div>
</template>

<script>
export default {
  name: "FeedTwitter",
  data() {
    return {
      activeFeed: false,
    };
  },
  methods: {
    revealFeed() {
      if (this.activeFeed === false) {
        this.activeFeed = true;
      } else {
        this.activeFeed = false;
      }
    },
  },
};
</script>

<style lang="scss">
@import "../assets/variables";

.wrapper-twitter {
  position: absolute;
  top: 5%;
  left: -360px;
  z-index: 10;

  display: flex;
  align-items: center;
  width: 360px;
  height: 90%;

  background-color: $colorTwitter;

  transition: left 0.3s;

  &.active {
    left: 0;
  }

  &__feed {
    width: 95%;
    margin: auto;
    height: 98%;
    overflow: auto;

    &::-webkit-scrollbar {
      display: none;
    }
  }

  &__btn {
    position: absolute;
    top: 35px;
    right: -50px;

    width: 50px;
    height: 50px;
    line-height: 50px;

    background-color: $colorTwitter;
    border-radius: 0 10px 10px 0;
    color: #fff;

    font-size: 35px;
    text-align: center;

    cursor: pointer;
  }
}

@media screen and (max-width: 1024px) and (orientation: portrait) {
  .wrapper-twitter {
    display: none;
  }
}
</style>