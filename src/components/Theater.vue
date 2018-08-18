<template lang="pug">
  .theater(@wheel.prevent="wheel")

    .cards(:class="{isCartOpen:isCartOpen}")
      .card(v-for="movie in movies")
        .left
          .cover(:style="bgcss(movie.cover)")
        .right
          h2 {{movie.name}}
          h4 {{movie.type}}
          p(v-html="movie.description")
          .price $ {{movie.price}}
          button.add(@click="addCart(movie, $event)") + add to your cart

    .buybox(v-if="currentMovie", :style="bgcss(currentMovie.cover)")

    .fixed-control(@click="toggleCart()")
      i.fa.fa-shopping-cart
      span {{cart.length}}

    .control(:class="{isCartOpen: isCartOpen}")
      .panel
        i.fa.fa-shopping-cart(style="font-size: 40px")
        h2 my movie card
        ul(v-if="cart.length>0")
          li(v-for="(movie, mid) in cart")
            .thumbnail(:style="bgcss(movie.cover)")
            h3 {{movie.name}}
              .remove(@click="removeItemInCard(mid)") -
              .price ${{movie.price}}
        h4(v-else) no movie
        hr
        h2 ${{totalPrice}}
</template>


<script>
import axios from "axios";
import TweenMax from "gsap/TweenMax";
export default {
  name: "Theater",

  data: function() {
    return {
      wheelLeft: 0,
      movies: [],
      cart: [],
      currentMovie: null,
      isCartOpen: false
    };
  },

  created() {
    const apiURL = `https://awiclass.monoame.com/api/command.php?type=get&name=movies`;
    axios.get(apiURL).then(res => (this.movies = res.data));
  },

  methods: {
    bgcss(url) {
      return {
        "background-image": `url(${url})`,
        "background-size": `cover`,
        "background-position": `center center`
      };
    },
    wheel(e) {
      this.wheelLeft = this.wheelLeft + e.deltaY * -2;
      if (this.wheelLeft > 0) this.wheelLeft = 0;
      if (this.wheelLeft < -2000) this.wheelLeft = -2000;
      TweenMax.to(`.cards`, 0.5, {
        left: `${this.wheelLeft}px`
      });
    },
    addCart(movie, e) {
      this.currentMovie = movie;

      const { offsetLeft, offsetTop } = e.target;

      this.$nextTick(() => {
        TweenMax.from(`.buybox`, 0.8, {
          left: offsetLeft,
          top: offsetTop,
          opacity: 1,
          ease: Power1.easeOut
        });
        setTimeout(() => {
          this.cart.push(movie);
        }, 800);
      });
    },
    toggleCart() {
      this.isCartOpen = !this.isCartOpen;
    },
    removeItemInCard(mid) {
      this.cart.splice(mid, 1);
    }
  },

  // 動態計算
  computed: {
    totalPrice() {
      return this.cart
        .map(movie => movie.price)
        .reduce((accumulator, curVal) => accumulator + curVal, 0);
    }
  },

  // 偵測變動
  watch: {
    cart() {
      TweenMax.from(`.fa-shopping-cart`, 0.3, {
        scale: 0.5
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
@import "../style/mixin.scss";

.theater {
  @include size(100%);
  flex: 1;

  .cards {
    @include flexCenter();
    height: 100%;
    justify-content: left;
    margin: 0 20vw;
    position: relative;
    left: 0px;
    transition: 0.5s, left 0s;

    &.isCartOpen {
      transform: scale(0.8);
    }
  }

  .card {
    @include size(460px, auto);
    @include bxshadow();
    background-color: rgba(white, 0.9);
    border-radius: 5px;
    color: #888;
    padding: 20px 20px 0px 20px;
    display: inline-flex;
    flex-shrink: 0;
    margin: 60px;
    transition: 0.5s;

    &:hover {
      transform: translateY(-10px);
      .left > .cover {
        transform: translateY(-10px);
      }
    }

    .left {
      flex: 1;

      .cover {
        @include size(180px, 240px);
        @include bxshadow();
        border-radius: 5px;
        position: relative;
        top: -50px;
      }
    }

    .right {
      flex: 2;
      padding: 10px 20px 0px 20px;

      h2 {
        margin: 0;
        font-weight: bold;
        font-size: 24px;
        color: #444;
      }

      h4 {
        margin: 10px 0;
        opacity: 0.8;
        font-weight: normal;
      }

      p {
        font-size: 13px;
        line-height: 1.3;
        text-align: justify;
        opacity: 0.8;
        min-height: 5em;
      }

      .price {
        display: inline-block;
        margin-right: 20px;
      }

      button {
        padding: 5px 10px;
        background-color: #bbb;
        color: rgba(white, 0.9);
        border-radius: 50px;
        cursor: pointer;
        // transition: 0.5s;
        &:hover {
          color: white;
          background-color: #f95e5e;
        }
      }
    }
  }

  .buybox {
    @include size(50px, 80px);
    background-color: #ffffff;
    position: fixed;
    right: 30px;
    top: 30px;
    opacity: 0;
  }

  .fixed-control {
    position: fixed;
    right: 30px;
    top: 30px;
    color: white;
    z-index: 100;
    opacity: 0.5;
    cursor: pointer;
    transition: 0.25s;

    &:hover {
      opacity: 1;
    }

    i {
      font-size: 30px;
      margin-right: 10px;
    }
  }

  .control {
    @include size(100%);
    @include fixedTo(0, 0);
    @include flexCenter();
    color: #eee;
    padding: 5vw;
    box-sizing: border-box;
    background-image: linear-gradient(
      10deg,
      #111 0%,
      #111 50%,
      transparent 100%
    );
    opacity: 0;
    pointer-events: none;
    transition: 0.5s;

    &.isCartOpen {
      opacity: 1;
      pointer-events: initial;
    }

    .panel {
      width: 70%;
    }

    ul {
      padding: 0;
      list-style: none;
      li {
        display: flex;
        padding: 5px 10px;
        margin: 5px 0;
        cursor: pointer;
        border-radius: 5px;
        transition: 0.25s;
        &:hover {
          background-color: rgba(white, 0.1);
          transform: translateY(-10px);
        }
        h3 {
          font-size: 17px;
          font-weight: normal;
          display: inline-block;
          width: 100%;
          opacity: 0.8;
          .price {
            float: right;
          }
          .remove {
            display: inline-block;
            padding: 4px 15px;
            background-color: rgba(white, 0.3);
            border-radius: 50px;
            &:hover {
              background-color: #ef4c4c;
            }
          }
        }
        .thumbnail {
          @include size(50px, 70px);
          display: inline-block;
          margin-right: 20px;
        }
      }
    }
  }
}
</style>
