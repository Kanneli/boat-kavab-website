<script setup>
import zayanImg from '@/assets/Zayan.png'
</script>

<template>
  <main v-on:mousemove="cursorPosition">
    <div id="mainContent" class="h-screen flex-row items-center">
      <div class="frame" id="kavab" :style="`background-image: url('${zayanImg}'); background-position: 0% 70%`">
        <div style="position: relative;">
          <div class="eyeball left"></div>
          <span class="pupil" id="kavab-eye-1"></span>
        </div>
        <div style="position: relative;">
          <div class="eyeball right"></div>
          <span class="pupil" id="kavab-eye-2"></span>
        </div>
        <div class="inner" id="kavab-inner"></div>
      </div>
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
      e1Radius: 2.4,
      e2Lft: 80.59,
      e2Top: 0,
      e2Radius: 2.4,
      e1x: null,
      e1y: null, // eye centre relative to top left of doc
      r1: null,
      e1xLoc: null,
      e1yLoc: null, // eye top left relative to top left of parent
      e2x: null,
      e2y: null,
      r2: null, // eye radii
      e2xLoc: null,
      e2yLoc: null
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
      var faceWidth = 380;
      var faceHeight = 380;
      // get left,top of eyes relative to parent
      this.e1xLoc = 0.01 * this.e1Lft * faceWidth - 190 / 2;
      this.e1yLoc = 0.01 * this.e1Top * faceHeight + 165 / 2;
      this.e2xLoc = 0.01 * this.e2Lft * faceWidth - 200 / 2;
      this.e2yLoc = 0.01 * this.e2Top * faceHeight + 165 / 2;
      // get absolute position of centre of eyes wrt to top left of document body
      let tmp = this.getPosition(this.faceObj);
      this.e1x = tmp.x + 0.01 * this.e1Lft * faceWidth - 190 / 2;
      this.e1y = tmp.y + 0.01 * this.e1Top * faceHeight + 165 / 2;
      this.e2x = tmp.x + 0.01 * this.e2Lft * faceWidth - 200 / 2;
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
  mounted () {
    if (document.readyState === "loading") {
      document.addEventListener("DOMContentLoaded", this.Xeyes)
      this.eyesInit()
    } else {
      this.Xeyes()
      this.eyesInit()
    }
  }
}
</script>

<style scoped>
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

.frame .eyeball {
  background: url('https://i.cdn.turner.com/adultswim/big/img/2013/10/24/eyeball.png') no-repeat;
  width: 41px;
  height: 41px;
  position: absolute;
}

.frame .eyeball span,
.pupil {
  z-index: 10;
  background: url('https://i.cdn.turner.com/adultswim/big/img/2013/10/24/pupil.png') no-repeat;
  display: block;
  width: 13px;
  height: 13px;
  position: absolute;
  top: 40%;
  left: 30%
}

.frame#kavab .eyeball.left {
  left: 119px;
  top: 70px;
}

.frame#kavab .eyeball.right {
  left: 188px;
  top: 70px;
}
</style>