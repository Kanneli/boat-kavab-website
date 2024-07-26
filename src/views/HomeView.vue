<script setup>
import zayanImg from '@/assets/Zayan.png'
import kavabSong from '@/assets/Kavab.mp3'
import MACake from '@/assets/MA_Cake.svg'
</script>

<template>
  <main v-on:mousemove="cursorPosition">
    <div id="mainContent" class="h-screen flex-row items-center">
      <div class="frame" id="kavab" :style="`background-image: url('${zayanImg}'); background-position: 0% 70%`">
        <div style="position: relative;">
          <div class="eyeball1 left"></div>
          <span class="pupil1" id="kavab-eye-1"></span>
        </div>
        <div style="position: relative;">
          <div class="eyeball2 right"></div>
          <span class="pupil2" id="kavab-eye-2"></span>
        </div>
        <div class="inner" id="kavab-inner"></div>
      </div>
    </div>
    <!-- Code used from https://codepen.io/alicepopoff/pen/WdEjKa -->
    <!-- <div class="cake">
      <div class="plate"></div>
      <div class="layer layer-1"></div>
      <div class="layer layer-2"></div>
      <div class="layer layer-3"></div>
      <div class="layer layer-4"></div>
      <div class="layer layer-top"></div>
      <div class="layer layer-top-cream1"></div>
      <div class="layer layer-top-cream2"></div>
      <div class="layer candle"></div>
    </div> -->
    <div class="ma_cake">
      <img :src="MACake" alt="" style="width: 600px;">
    </div>
    <div class="top-text">
      GRATS EY? <br>
      KON GRATS EH
    </div>
  </main>
</template>

<script>
export default {
  data () {
    return {
      kavab: null,
      faceObj: null,
      eye1Obj: null,
      eye2Obj: null,
      e1Lft: 60.50,
      e1Top: 0,
      e1Radius: 3,
      e2Lft: 80.59,
      e2Top: 0,
      e2Radius: 3,
      e1x: null,
      e1y: null, // eye centre relative to top left of doc
      r1: null,
      e1xLoc: null,
      e1yLoc: null, // eye top left relative to top left of parent
      e2x: null,
      e2y: null,
      r2: null, // eye radii
      e2xLoc: null,
      e2yLoc: null,
      audio: null
    }
  },
  methods: {
    associateObjWithEvent(obj, methodName) {
      return (function(e) {
        // console.log(obj)
        if (!e)
          e = window.event;
        return obj[methodName](e);
      });
    },

    getPosition(element) {
      var xPosition = 0;
      var yPosition = 0;
      while (element) {
        xPosition += (element.offsetLeft - element.scrollLeft + element.clientLeft);
        yPosition += (element.offsetTop - element.scrollTop + element.clientTop);
        element = element.offsetParent;
      }
      return {
        x: xPosition,
        y: yPosition
      };
    },

    eyesInit () {
      var faceWidth = 400;
      var faceHeight = 400;
      // get left,top of eyes relative to parent
      this.e1xLoc = 0.01 * this.e1Lft * faceWidth - 230 / 2;
      this.e1yLoc = 0.01 * this.e1Top * faceHeight + 165 / 2;
      this.e2xLoc = 0.01 * this.e2Lft * faceWidth - 230 / 2;
      this.e2yLoc = 0.01 * this.e2Top * faceHeight + 165 / 2;
      // get absolute position of centre of eyes wrt to top left of document body
      let tmp = this.getPosition(this.faceObj);
      this.e1x = tmp.x + 0.01 * this.e1Lft * faceWidth - 230 / 2;
      this.e1y = tmp.y + 0.01 * this.e1Top * faceHeight + 165 / 2;
      this.e2x = tmp.x + 0.01 * this.e2Lft * faceWidth - 230 / 2;
      this.e2y = tmp.y + 0.01 * this.e2Top * faceHeight + 165 / 2;
      this.r1 = 0.01 * this.e1Radius * faceWidth;
      this.r2 = 0.01 * this.e2Radius * faceWidth;
      // now move the eyes to a less goggle-eye position until mouse moves
      this.eye1Obj.style.left = this.e1xLoc + "px"; // "12.4em";
      this.eye1Obj.style.top = this.e1yLoc + this.r1 / 1.5 + "px"; // "16.3em";
      this.eye2Obj.style.left = this.e2xLoc + "px"; // "21.0em";
      this.eye2Obj.style.top = this.e2yLoc + this.r2 / 1.5 + "px"; // "16.3em";
    },

    eyesMove (e) {
      var csrX, csrY;
      var x, y;
      var dx, dy;
      var R;
      var savThis = this;

      document.onmousemove = null; // turn off mouseMove events
      if (e.pageX == null) {
        // IE case
        var d = (document.documentElement &&
            document.documentElement.scrollLeft != null) ?
          document.documentElement : document.body;
        csrX = e.clientX + d.scrollLeft;
        csrY = e.clientY + d.scrollTop;
      } else {
        // all other browsers
        csrX = e.pageX;
        csrY = e.pageY;
      }
      // eye 1 first
      dx = csrX - this.e1x;
      dy = csrY - this.e1y;
      R = Math.sqrt(dx * dx + dy * dy); // distance from eye centre to csr
      x = (R < this.r1) ? dx : dx * this.r1 / R;
      y = (R < this.r1) ? dy : dy * this.r1 / R;
      this.eye1Obj.style.left = x + this.e1xLoc + "px";
      this.eye1Obj.style.top = y + this.e1yLoc + "px";
      // now for eye 2
      dx = csrX - this.e2x;
      dy = csrY - this.e2y;
      R = Math.sqrt(dx * dx + dy * dy);
      x = (R < this.r2) ? dx : dx * this.r2 / R;
      y = (R < this.r2) ? dy : dy * this.r2 / R;
      this.eye2Obj.style.left = x + this.e2xLoc + "px";
      this.eye2Obj.style.top = y + this.e2yLoc + "px";
      // set a timer to make a delayed call to setup mousemove event
      var self = this
      setTimeout(() => {
        this.$el.onmousemove = self.associateObjWithEvent(savThis, "eyesMove")
      }, 100);
    },

    Xeyes() {
      this.faceObj = document.getElementById("kavab-inner");
      this.eye1Obj = document.getElementById("kavab-eye-1");
      this.eye2Obj = document.getElementById("kavab-eye-2");
      this.e1x;
      this.e1y; // eye centre relative to top left of doc
      this.r1;
      this.e1xLoc;
      this.e1yLoc; // eye top left relative to top left of parent
      this.e2x;
      this.e2y;
      this.r2; // eye radii
      this.e2xLoc;
      this.e2yLoc;
      // setup initial eye locations

      // if the browser window is resized eye locations must be re-calculated
      window.onresize = this.associateObjWithEvent(this, "eyesInit");
      // setup the eyeMove event to be called when ever the cursor is moved
      //document.onmousemove = associateObjWithEvent(this, "eyesMove");
      var self = this;
      this.$el.addEventListener('mousemove', (e) => {
        self.eyesMove(e)
      }, false);
      // for debug use
      // this.$el.onclick = this.associateObjWithEvent(this, "eyesMove");
    },
  },
  // playAudio() {
  // },
  mounted () {
    this.Xeyes()
    this.eyesInit()
    // this.playAudio()
    this.audio = new Audio(kavabSong);
    this.audio.play()
  }
}
</script>

<style>
.ma_cake {
  position: fixed;
  width: 600px;
  left: 50%;
  transform: translateX(-50%);
}
.top-text {
  position: fixed;
  top: 5%;
  text-align: center;
  width: 100%;
  font-weight: bold;
}
@media only screen and (max-width: 1279px) {
  .top-text {
    font-size: 3rem;
  }
  .ma_cake {
    bottom: -40vh;
  }
}
@media only screen and (min-width: 1280px) {
  .top-text {
    font-size: 5rem;
  }
  .ma_cake {
    bottom: -15rem;
  }
}

#mainContent {
  display: flex;
  text-align: center;
  width: 100%;
}

#mainContent .frame {
  width: 358px;
  height: 442px;
  margin: 40px auto;
  position: relative;
  padding: 51px;
}

/* eyeball stuff */

.frame .eyeball1 {
  background: white;
  border-radius: 100%;
  border: 4px solid lightgray;
  width: 50px;
  height: 50px;
  position: absolute;
}
.frame .eyeball2 {
  background: white;
  border-radius: 100%;
  border: 4px solid lightgray;
  width: 40px;
  height: 40px;
  position: absolute;
  margin-top: 5px;
}

.frame .eyeball1.eyeball2 span,
.pupil1 {
  z-index: 10;
  background: black;
  border-radius: 100%;
  display: block;
  width: 25px;
  height: 25px;
  position: absolute;
  top: 40%;
  left: 30%;
  margin-left: -4px;
  margin-top: -4px;
}
.pupil2 {
  z-index: 10;
  background: black;
  border-radius: 100%;
  display: block;
  width: 20px;
  height: 20px;
  position: absolute;
  top: 40%;
  left: 30%;
  margin-left: -8px;
  margin-top: -4px;
}

.frame#kavab .eyeball1.left {
  left: 110px;
  top: 65px;
}
.frame#kavab .eyeball2.left {
  left: 110px;
  top: 65px;
}

.frame#kavab .eyeball1.right {
  left: 188px;
  top: 65px;
}
.frame#kavab .eyeball2.right {
  left: 188px;
  top: 65px;
}
</style>

<style lang="stylus">
$cake_bg_coffee = #BCAA99
$cake_bg_choco = #443850
$cake_bg_cream = #F2F7F2
$cake_bg_cherry = #8E5572
$cake_bg_spinach = #BBBE64
$cake_cream_side = #e5e7e5


$cake_width = 300px
$cake_height = 200px
$candle_width = 30px
$candle_height = 100px

.cake: {
  position: fixed
  top: 0
}
.layer {
   position: absolute
   margin: 0 auto
   width: $cake_width
   border-radius: "%s / %s" % ($cake_width/2 $cake_width/4)
   height: $cake_height
}

.layer-1 {
    top:0;
    z-index: 10;
    background-color: $cake_bg_coffee;
}

.layer-2 {
  top: "calc(%s)" % ($cake_height/4)
  z-index: 9
  background-color: $cake_bg_cream
}
.layer-3 {
  top: "calc(%s)" % (2*$cake_height/4)
  z-index: 8
  background-color: $cake_bg_coffee
}
.layer-4 {
  top: "calc(%s)" % (3*$cake_height/4)
  z-index: 7
  background-color: $cake_bg_cherry
 
}

.layer-top {
  top: 0
  z-index: 12
  background: $cake_bg_cream
  height: "calc(%s)" % ($cake_width / 2)
}

.layer-top-cream1 {
    position: absolute
    width: "calc(%s)" % ($cake_width / 4)
    transform: skew(0deg,18deg)
    border-radius: 0 0 78% 40% / 0 0 92% 74%
    height: "calc(%s)" % ($cake_height / 4)
    top: 123px
    left: 74px
    background: $cake_cream_side
    z-index: 11
}

.layer-top-cream2 {
    position: absolute
    width: "calc(%s)" % ($cake_width / 4)
    transform: skew(0deg,18deg)
    border-radius: 0 0 78% 40% / 0 0 92% 74%
    height: "calc(%s)" % ($cake_height / 4)
    top: 123px
    left: 16px
    background: $cake_cream_side
    z-index: 11
}

.candle {
    top: "calc(80px - %s)" % ($candle_height)
    left: "calc(%s - %s)" % ($cake_width/2  $candle_width/2)
    z-index: 12
    background: $cake_bg_spinach
    width: $candle_width
    height: $candle_height
    &:after {
      content: ""
      position: absolute
      top: 0
      left: 0
      width: $candle_width
      height: "calc(%s)" % ($candle_height/3)
      border-radius: 50%
      background: $cake_bg_spinach
      display:none
    }
    &:before {
      content: ""
      position: absolute
      background: #ffcb6a
      width: "calc(%s)" % ($candle_width)
      height: "calc(%s / 2)" % ($candle_height)
      border-radius: 80% / 70%
      top: "calc(-%s / 2)" % ($candle_height)
      left: "calc(50% - %s)" % ($candle_width/2)
      z-index: 15
      box-shadow: 0px 0px 40px #fff, 0 0 75px #ffffff, 0 0 90px #fff, 0 0 100px #fff
      animation: flame 3s infinite ease-in-out
     }

}


@keyframes flame {
    0% , 100% {
        transform: skewX(5deg);
            box-shadow: 0px 0px 40px #fff, 0 0 75px #ffffff, 0 0 90px #fff, 0 0 100px #fff}
    25% {
        transform: skewX(-10deg);
            box-shadow: 0px 0px 40px white, 0 0 75px orange, 0 0 90px orange, 0 0 100px white}
    50% {
        transform: skewX(15deg);
            box-shadow: 0px 0px 40px orange, 0 0 75px orange, 0 0 90px black, 0 0 100px #fff}
    75% {
        transform: skewX(-2deg); 
            box-shadow: 0px 0px 40px #fff, 0 0 75px #ffffff, 0 0 90px #fff, 0 0 100px black}
}

.cake {
    position: fixed
    bottom: 35vh
    width: $cake_width
    left: 50%;
    transform: translateX(-50%);
}
</style>