<template>
	<view class="content">
		<view class="main" id="main">
			<text>renderjs示例</text>
		</view>
		<button class="btn" type="default" @click="html2canvas.onClick">html2canvas生成图片</button>
		<button class="btn" type="default" @click="plusClick">plus生成图片</button>
		<view class="image-container" v-if='html2canvasUrl'>
			<image :src="html2canvasUrl" mode="" class="img"></image>
			<text>html2canvas图片</text>
		</view>
		<view class="image-container" v-if='plusUrl'>
			<image :src="plusUrl" mode="" class="img"></image>
			<text>plus图片</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				html2canvasUrl: '',
				plusUrl: ''
			}
		},
		methods: {
			generatorImage(url){
				this.html2canvasUrl = url;
			},
			plusClick(){
				const self = this;
				const {statusBarHeight} = uni.getSystemInfoSync()
				const query = uni.createSelectorQuery().in(this);
				query.select('#main').boundingClientRect(data => {
					console.log(data);
					const { top,left,width,height } = data;
					// #ifdef APP-PLUS
					const pages = getCurrentPages();
					const page = pages[pages.length - 1];
					const ws = page.$getAppWebview();
					const bitmap = new plus.nativeObj.Bitmap('test');
					ws.draw(bitmap,
						function() {
							bitmap.save(`_doc/${new Date().getTime()}.jpg`, {
								overwrite: true,
								quality: 100,
								clip: {
									top:top + 44 + statusBarHeight,
									left,
									width,
									height
								},
							}, function(result) {
								self.plusUrl = result.target;
							}, function(e) {
								console.log('保存图片失败：' + JSON.stringify(e));
							});
						},
						function(e) {
							console.log('截屏绘制图片失败：' + JSON.stringify(e));
						});
					
					// #endif
				}).exec();
				
			}
		}
	}
</script>
<script module="html2canvas" lang="renderjs">
	export default {
		mounted() {
			// 动态引入较大类库避免影响页面展示
			const script = document.createElement('script')
			// view 层的页面运行在 www 根目录，其相对路径相对于 www 计算
			script.src = 'static/html2canvas.min.js'
			document.head.appendChild(script)
		},
		methods: {
			onClick(event, instance) {
				html2canvas(document.querySelector("#main")).then(canvas => {
					const url = canvas.toDataURL();
					// 调用 service 层的方法
					instance.callMethod('generatorImage', url)
				});
			}
		}
	}
</script>

<style>
	.main{
		width: 300rpx;
		height: 300rpx;
		background-color: #F0AD4E;
		margin: 30rpx auto;
		display: flex;
		align-items: center;
		justify-content: center;
		color: #fff;
		font-size: 40rpx;
		font-weight: bold;
	}
	.btn{
		margin: 10rpx 0;
	}
	.image-container{
		width: 300rpx;
		height: 300rpx;
		margin: 30rpx auto;
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.image-container .img{
		width: 100%;
		height: 100%;
	}
</style>
