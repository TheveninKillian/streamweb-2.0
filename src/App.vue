<script>
import EmbedTwitch from "./components/EmbedTwitch";
import Social from "./components/Social";
import FeedTwitter from "./components/FeedTwitter";

export default {
  name: "App",
  components: {
    EmbedTwitch,
    Social,
    FeedTwitter,
  },
  data() {
    return {
      isActive: false,
      sizeWidth: 0,
    };
  },
  mounted() {
    this.screenSizeWidth();

    window.addEventListener("resize", this.screenSizeWidth);
  },
  methods: {
    revealBtnSocial() {
      if (this.isActive === false) {
        this.isActive = true;
      } else {
        this.isActive = false;
      }
    },

    screenSizeWidth() {
      this.sizeWidth =
        window.innerWidth ||
        document.documentElement.clientWidth ||
        document.body.clientWidth;
    },
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.screenSizeWidth);
  },
};
</script>

<template>
  <div
    :class="{ overlay: isActive === true }"
    @click="revealBtnSocial"
    v-if="sizeWidth < 1024"
  ></div>

  <FeedTwitter v-if="sizeWidth >= 1024" />

  <video autoplay loop id="bgvid" v-if="sizeWidth >= 1024">
    <source src="./assets/video.webm" type="video/webm" />
  </video>

  <header class="header">
    <div class="header__title">
      <img src="./assets/logo.png" alt="logo de mastersnakou" />
      <h1>Mastersnakou</h1>
    </div>

    <div class="header__btn" @click="revealBtnSocial" v-if="sizeWidth < 1024">
      <i class="las la-ellipsis-h"></i>
    </div>

    <div class="header__btn-social" v-if="sizeWidth >= 1024">
      <Social />
    </div>
  </header>

  <nav class="btn-social" :class="{ active: isActive }" v-if="sizeWidth < 1024">
    <Social />
  </nav>

  <EmbedTwitch />

  <footer>
    Développé par
    <a href="https://github.com/TheveninKillian">@Killian Thevenin</a>. Ce site
    a été créé a titre d'exemple.
  </footer>
</template>

<style lang="scss">
@import "./assets/variables";

.overlay {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 5;

  height: 100%;
  width: 100%;

  background: transparent;
}

video {
  position: fixed;
  left: -50%;

  display: none;

  min-width: 100%;
  height: 100%;

  z-index: -1;
}

.header {
  position: relative;
  display: flex;
  justify-content: flex-end;
  align-items: center;
  height: 70px;

  color: #fff;

  &__title {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);

    display: flex;
    flex-direction: column;
    align-items: center;

    margin-top: 5px;

    img {
      width: 45px;
    }

    h1 {
      position: relative;
      color: $colorPrimary;

      text-transform: uppercase;
      font-style: italic;
    }
  }

  &__btn {
    position: absolute;
    right: 15px;

    cursor: pointer;

    i {
      font-size: 25px;
    }
  }

  &__btn-social {
    position: relative;

    display: flex;
    margin-right: 20px;

    div {
      margin: 0 5px;
    }

    i {
      color: #fff;
      transition: color 0.3s;
      font-size: 20px;

      &:hover {
        color: $colorPrimary;
      }
    }
  }
}

.btn-social {
  position: absolute;
  top: -70px;
  z-index: 6;

  display: flex;
  align-items: center;
  justify-content: center;
  height: 70px;
  width: 100%;

  background: $colorBlack;

  transition: top 0.3s;

  &.active {
    top: 0;
  }

  div {
    margin: 0 10px;
  }

  i {
    color: #fff;
    transition: color 0.3s;

    font-size: 25px;

    &:hover {
      color: $colorPrimary;
    }
  }
}

footer {
  display: none;
}

@media screen and (orientation: landscape) {
  video {
    left: 0;
  }
}

@media screen and (min-width: 1024px) and (orientation: landscape) {
  video {
    display: block;
  }

  .header {
    &__title {
      flex-direction: row;
      margin-top: 0px;

      h1 {
        margin-left: 5px;

        font-size: 30px;
        font-weight: bold;
      }
    }

    &__btn-social i {
      font-size: 25px;
    }
  }

  footer {
    position: fixed;
    bottom: 5px;
    left: 50%;
    transform: translateX(-50%);

    display: block;

    color: #fff;

    a {
      color: $colorPrimary;
    }
  }
}
</style>
