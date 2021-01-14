<template>
	<view class="content">
		<view class="main" id="main">
			<text>renderjs示例</text>
		</view>
		<button type="default" @click="html2canvas.onClick">生成图片</button>
		<view class="image-container">
			<image :src="imageUrl" mode="" class="img"></image>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imageUrl: ''
			}
		},
		methods: {
			generatorImage(url){
				this.imageUrl = url;
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
	.image-container{
		width: 300rpx;
		height: 300rpx;
		margin: 30rpx auto;
	}
	.image-container .img{
		width: 100%;
		height: 100%;
	}
</style>
