<template>
	<view class="signa">
		<view class="btn">
			<view class="cancel-btn" @click="clear">
				重写
			</view>
			<view class="save-btn" @click="save">
				保存
			</view>
		</view>
		<canvas id="canvas" disable-scroll="true" :style="{'width':width,'height':height}" canvas-id="cid" @touchstart="starts"
		 @touchmove="moves" @touchend="end"></canvas>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				resultUrl: '',
				dom: null,
				line: [],
				width: '0px',
				height: '0px',
				radius: 0
			}
		},
		created() {
			uni.getSystemInfo({
				success: (res) => {

					this.width = res.windowWidth - 50 + 'px';
					this.height = res.windowHeight - 15 + 'px';

				}
			});
			this.dom = uni.createCanvasContext('cid');
		},
		methods: {
			end(e) {

			},
			distance(a, b) {
				let x = b.x - a.x;
				let y = b.y - a.y;
				return Math.sqrt(x * x + y * y);
			},
			starts(e) {
				this.line.push({
					points: [{
						time: new Date().getTime(),
						x: e.touches[0].x,
						y: e.touches[0].y,
						dis: 0
					}]
				})
				let currentPoint = {
					x: e.touches[0].x,
					y: e.touches[0].y
				}
				this.currentPoint = currentPoint
				this.drawer(this.line[this.line.length - 1])
			},
			moves(e) {
				let point = {
					x: e.touches[0].x,
					y: e.touches[0].y
				}
				this.lastPoint = this.currentPoint,
					this.currentPoint = point
				this.line[this.line.length - 1].points.push({
					time: new Date().getTime(),
					x: e.touches[0].x,
					y: e.touches[0].y,
					dis: this.distance(this.currentPoint, this.lastPoint)
				})
				this.drawer(this.line[this.line.length - 1])
			},
			drawer(item) {
				let x1, x2, y1, y2, len, radius, r, cx, cy, t = 0.5,
					x, y;
				var time = 0;
				if (item.points.length > 2) {
					let lines = item.points[item.points.length - 3];
					let line = item.points[item.points.length - 2];
					let end = item.points[item.points.length - 1];
					x = line.x;
					y = line.y;
					x1 = lines.x;
					y1 = lines.y;
					x2 = end.x;
					y2 = end.y;
					// console.log(x, y)
					var dis = 0;
					time = (line.time - lines.time) + (end.time - line.time)
					dis = line.dis + lines.dis + end.dis;
					var dom = uni.createCanvasContext('cid');
					var or = Math.min(time / dis * this.linePressure + this.lineMin, this.lineMax);
					// P0（x1,y1）,P2(x2,y2), P1(cx,cy)起点，终点，控制点
					cx = (x - (Math.pow(1 - t, 2) * x1) - Math.pow(t, 2) * x2) / (2 * t * (1 - t))
					cy = (y - (Math.pow(1 - t, 2) * y1) - Math.pow(t, 2) * y2) / (2 * t * (1 - t))
					dom.setLineCap('round')
					dom.beginPath();
					// dom.strokeStyle = 'black';
					dom.setStrokeStyle('black')
					dom.setLineWidth(5)
					// dom.lineWidth = 5;
					// 起始点
					dom.moveTo(x1, y1);
					dom.quadraticCurveTo(cx, cy, x2, y2);
					dom.stroke();
					dom.draw(true)
				}

			},
			clear() {
				this.dom.clearRect(0, 0, 1000, 1000)
				this.dom.draw()
			},
			save() {
				var t=this;
				uni.canvasToTempFilePath({
					canvasId: 'cid',
					fileType: 'png',
					quality: 1, //图片质量
					success(res) {
						// console.log(res.tempFilePath)
						t.$emit('getImg',res.tempFilePath)
						uni.navigateBack({
							delta:1
						})
					}
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	.signa {
		position: relative;
		overflow: hidden;
		background-color: #fbfbfb;
		// background-color: #aa55ff;
		height: 100vhh;
		width: 100vw;
		#canvas {
			background-color: white;
			margin-left: 110rpx;
			border: 1px solid #d6d6d6;
		}

		.content {
			margin-left: 60px;
			height: 100vh;
			background-color: white;
		}

		.btn {
			height: 100vh;
			position: fixed;
			font-size: 32rpx;
			.cancel-btn {
				width: 42vh;
				position: fixed;
				left: 50rpx;
				border: 1rpx solid #a9a1a1;
				transform: rotate(90deg);
				color: #666;
				margin-left: -21vh;
				margin-top: 21vh;
				height: 65rpx;
				line-height: 65rpx;
				border-radius: 3px;
				text-align: center;
				justify-content: center;
			}

			.save-btn {
				position: absolute;
				z-index: 999;
				display: inline-flex;
				margin-top: 67vh;
				margin-left: -21vh;
				transform: rotate(90deg);
				background: #A91F52;
				width: 42vh;
				left: 50rpx;
				border-radius: 3px;
				border: 1rpx solid #A91F52;
				color: #fff;
				height: 65rpx;
				line-height: 65rpx;
				text-align: center;
				justify-content: center;
			}
		}
	}
</style>
