<template>
  <view class="signa">
    <view
      class="placeholder"
      :style="{ left: width / 2 + 'px', top: height / 2 + 'px' }"
      v-if="!line.length"
    >
      <image :src="writeIcon" class="write-icon"></image>
      <text>{{ placeholder }}</text>
    </view>
    <canvas
      class="canvas"
      disable-scroll="true"
      :style="{ width: width + 'px', height: height + 'px' }"
      canvas-id="designature"
      @touchstart="starts"
      @touchmove="moves"
      @touchend="end"
    ></canvas>
    <view class="btns" :style="{ width: width + 'px' }">
      <button @click="clear">重写</button>
      <button @click="save" type="primary">保存</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      dom: null,
      line: [],
      writeIcon: require("./write.png")
      // radius: 0,
    }
  },
  props: {
    /** 点一下的情况是否显示一个黑点 */
    showDot: {
      type: Boolean,
      default: true
    },
    /** 占位文本 */
    placeholder: {
      type: String,
      default: "请签名"
    },
    /** 画布宽度 */
    width: {
      type: [String, Number],
      default: 400
    },
    /** 画布高度 */
    height: {
      type: [String, Number],
      default: 200
    }
  },
  created() {
    this.dom = uni.createCanvasContext("designature", this)
  },
  methods: {
    end(e) {},
    distance(a, b) {
      let x = b.x - a.x
      let y = b.y - a.y
      return Math.sqrt(x * x + y * y)
    },
    starts(e) {
      let currentPoint = {
        x: e.touches[0].x,
        y: e.touches[0].y
      }
      this.currentPoint = currentPoint
      this.line.push({
        points: [
          {
            // time: new Date().getTime(),
            x: currentPoint.x,
            y: currentPoint.y
            // dis: 0
          }
        ]
      })
      // 点一下的情况设置一个黑点
      if (this.showDot) {
        this.line[this.line.length - 1].points.push(
          {
            x: currentPoint.x,
            y: currentPoint.y
          },
          {
            x: currentPoint.x,
            y: currentPoint.y
          }
        )
      }
      this.drawer(this.line[this.line.length - 1])
    },
    moves(e) {
      let point = {
        x: e.touches[0].x,
        y: e.touches[0].y
      }
      this.lastPoint = this.currentPoint
      this.currentPoint = point
      this.line[this.line.length - 1].points.push({
        // time: new Date().getTime(),
        x: e.touches[0].x,
        y: e.touches[0].y
        // dis: this.distance(this.currentPoint, this.lastPoint)
      })
      this.drawer(this.line[this.line.length - 1])
    },
    drawer(item) {
      let x1,
        x2,
        y1,
        y2,
        len,
        radius,
        r,
        cx,
        cy,
        t = 0.5,
        x,
        y
      // let time = 0
      if (item.points.length > 2) {
        let lines = item.points[item.points.length - 3]
        let line = item.points[item.points.length - 2]
        let end = item.points[item.points.length - 1]
        x = line.x
        y = line.y
        x1 = lines.x
        y1 = lines.y
        x2 = end.x
        y2 = end.y
        // time = line.time - lines.time + (end.time - line.time)
        const dom = this.dom
        // let dis = 0
        // dis = line.dis + lines.dis + end.dis
        // let or = Math.min((time / dis) * this.linePressure + this.lineMin, this.lineMax)
        cx = (x - Math.pow(1 - t, 2) * x1 - Math.pow(t, 2) * x2) / (2 * t * (1 - t))
        cy = (y - Math.pow(1 - t, 2) * y1 - Math.pow(t, 2) * y2) / (2 * t * (1 - t))
        dom.setLineCap("round")
        dom.beginPath()
        dom.setStrokeStyle("black")
        dom.setLineWidth(5)
        dom.moveTo(x1, y1)
        dom.quadraticCurveTo(cx, cy, x2, y2)
        dom.stroke()
        dom.draw(true)
      }
    },
    clear() {
      this.dom.clearRect(0, 0, 1000, 1000)
      this.dom.draw()
      this.line = []
    },
    save() {
      uni.canvasToTempFilePath(
        {
          canvasId: "designature", // 画布id
          quality: 1, // 图片质量
          success: res => {
            console.log("图片临时保存路径", res.tempFilePath)
            this.$emit("getImg", res.tempFilePath)
          },
          fail(e) {
            console.log(e)
          }
        },
        this
      )
    }
  }
}
</script>

<style scoped lang="scss">
.signa {
  position: relative;
  .placeholder {
    position: absolute;
    transform: translateX(-50%) translateY(-50%);
    z-index: 1;
    font-size: 18rpx;
    pointer-events: none;
    display: flex;
    align-items: center;
    .write-icon {
      flex-shrink: 0;
      width: 20rpx;
      height: 20rpx;
    }
  }
  .canvas {
    background-color: #f9f9fb;
    border: 1px solid #d6d6d6;
  }
  .btns {
    display: flex;
    button {
      flex: 1;
    }
  }
}
</style>
