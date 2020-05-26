<template>
  <div id="wrap">
      <button @click="showTrackbuild">빌드보기</button>
    <TrackBuild v-if="flag"></TrackBuild>
  </div>
</template>

<script>
import TrackBuild from './components/TrackBuild.vue'

export default {
  name: 'App',
  components: {
    TrackBuild
  },
  methods: {
    showTrackbuild: function(){
        this.$data.flag = !this.$data.flag
    }  
  },
  data(){
      return {
          flag: false
      }
  }
}
</script>

<style>
body, html {
    font-size: 12px;
    font-weight: 400;
    letter-spacing: -1px;
}

#wrap #buildview {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0,0,0,0.5);
    display: table;
    transition: opacity .3s ease
}

    #wrap #buildview #wrapper {
        display: table-cell;
        vertical-align: middle
    }

        #wrap #buildview #wrapper #container {
            position: relative;
            width: 1300px;
            height: 640px;
            margin: 0px auto;
            background-color: #fff;
            box-shadow: 10px 10px 10px rgba(0,0,0,0.1);
            z-index: 10
        }

@media (max-width: 1630px) {
    #wrap #buildview #wrapper #container {
        width: 1000px
    }
}

#wrap #buildview #wrapper #container a {
    position: absolute;
    top: -25px;
    right: 0px
}

    #wrap #buildview #wrapper #container a img {
        width: 20px
    }

#wrap #buildview #wrapper #container .title {
    position: relative;
    padding-left: 40px;
    line-height: 60px;
    font-size: 16px;
    border-bottom: 1px solid #ccc
}

    #wrap #buildview #wrapper #container .title i {
        margin-left: 5px;
        font-size: 14px;
        color: #a1a1a1
    }

    #wrap #buildview #wrapper #container .title .track {
        position: absolute;
        top: 22px;
        right: 40px;
        height: 25px;
        line-height: 25px
    }

        #wrap #buildview #wrapper #container .title .track img {
            height: 25px;
            vertical-align: top
        }

#wrap #buildview #wrapper #container .build {
    position: relative;
    height: 580px;
    overflow: auto
}

    #wrap #buildview #wrapper #container .build .svg {
        text-align: center
    }

    #wrap #buildview #wrapper #container .build #pointdesc {
        position: fixed;
        padding: 10px;
        opacity: 0;
        color: #fff;
        background-color: rgba(0,0,0,0.7);
        border-radius: 5px;
        font-size: 16px
    }

#wrap #buildview #wrapper #container .remote {
    position: absolute;
    bottom: 40px;
    right: 40px;
    display: flex;
    width: 300px;
    border: 1px solid #07f;
    border-radius: 5px;
    padding: 15px 20px;
    background-color: #fff;
    box-shadow: 5px 5px 5px rgba(0,0,0,0.1);
    opacity: 0.5
}

@media (max-width: 1630px) {
    #wrap #buildview #wrapper #container .remote {
        width: 260px
    }
}

#wrap #buildview #wrapper #container .remote:hover {
    opacity: 1
}

#wrap #buildview #wrapper #container .remote .desc {
    flex: 1;
    font-size: 14px
}

    #wrap #buildview #wrapper #container .remote .desc ul li i {
        margin-right: 5px
    }

        #wrap #buildview #wrapper #container .remote .desc ul li i.start {
            color: #9bd728
        }

        #wrap #buildview #wrapper #container .remote .desc ul li i.boost {
            color: #0077FF
        }

        #wrap #buildview #wrapper #container .remote .desc ul li i.normal {
            color: rgba(0,119,255,0.2)
        }

        #wrap #buildview #wrapper #container .remote .desc ul li i.drift {
            color: #f86684
        }

#wrap #buildview #wrapper #container .remote .buttons {
    flex: 2;
    text-align: right;
    height: 80px
}

    #wrap #buildview #wrapper #container .remote .buttons button {
        width: 50px;
        height: 50px;
        margin-top: 15px;
        margin-left: 5px;
        border: none;
        background-color: #07f;
        color: #fff;
        font-size: 30px;
        line-height: 50px;
        border-radius: 50px
    }

        #wrap #buildview #wrapper #container .remote .buttons button:focus {
            outline: 0
        }

        #wrap #buildview #wrapper #container .remote .buttons button:active {
            background-color: rgba(0,119,255,0.4)
        }

        #wrap #buildview #wrapper #container .remote .buttons button:hover {
            background-color: rgba(0,119,255,0.4)
        }
</style>
