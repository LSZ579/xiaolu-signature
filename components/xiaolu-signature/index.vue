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
		<canvas class="canvas" disable-scroll="true" :style="{'width':width,'height':height}" canvas-id="designature" @touchstart="starts"
		 @touchmove="moves" @touchend="end"></canvas>
	</view>
</template>

<script>
	
	/*
	*	已兼容h5和小程序端
	* 
	*	如有问题可以加qq：122720267
	* 	里面的计算都是自己转换公式得出的，不容易啊
	*	使用该插件的朋友请给个好评，或者到git start一下
	* 
	*/
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
			this.dom = uni.createCanvasContext('designature',this);
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
				console.log(e)
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
				console.log(e)
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
					var dis = 0;
					time = (line.time - lines.time) + (end.time - line.time)
					dis = line.dis + lines.dis + end.dis;
					var dom = this.dom;
					var or = Math.min(time / dis * this.linePressure + this.lineMin, this.lineMax);
					cx = (x - (Math.pow(1 - t, 2) * x1) - Math.pow(t, 2) * x2) / (2 * t * (1 - t))
					cy = (y - (Math.pow(1 - t, 2) * y1) - Math.pow(t, 2) * y2) / (2 * t * (1 - t))
					dom.setLineCap('round')
					dom.beginPath();
					dom.setStrokeStyle('black')
					dom.setLineWidth(5)
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
				console.log(555)
				uni.canvasToTempFilePath({
					canvasId: 'designature',
					fileType: 'png',
					quality: 1, //图片质量
					success:function(res) {
						console.log(res.tempFilePath)
						t.$emit('getImg',res.tempFilePath)
						// uni.navigateBack({
						// 	delta:1
						// })
					},
					fail(e){
						console.log(e)
					}
				},this)
			}
		}
	}
</script>

<style scoped lang="scss">
	.signa {
		position: relative;
		overflow: hidden;
		background-color: #fbfbfb;
		height: 100vh;
		width: 100vw;
		.canvas {
			background-color: #FFFFFF;
			position: absolute;
			z-index: 9999;
			left: 45px;
			border: 1px solid #d6d6d6;
		}
		.btn {
			height: 100vh;
			position: fixed;
			background-color: #007AFF;
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
