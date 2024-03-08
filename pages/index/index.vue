<template>
	<view class="container">
		<view class="title">
			CT图片上传
		</view>
		<view class="ct-photo">
			<button @click="chooseImage">选择图片</button>
			<view class="image-box" :style="{ backgroundImage: `url(${imageUrl})` }" v-if="imageUrl"></view>
			<button @click="uploadImage" v-if="imageUrl">上传图片</button>
			<button @click="clearImage" v-if="imageUrl">清除图片</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imageUrl: '',
				tempFilePath: '',
				imgId:'',
				isAnalyze:false
			}
		},
		onLoad() {
			uni.getStorage({
				key:'ctToken',
				success(res) {
					console.log(res);
				}
			})
		},
		methods: {
			// 选择图片
			chooseImage() {
				uni.chooseImage({
					count: 1,
					success: (res) => {
						this.tempFilePath = res.tempFilePaths[0];
						this.imageUrl = res.tempFilePaths[0];
					},
					fail: (err) => {
						console.error('选择图片失败', err);
					},
				});
			},
			// 上传图片
			async uploadImage() {
				uni.getStorage({
					key: 'ctToken',
					success: (res) => {
						const token = res.data.token; // 获取本地存储中的Token
						uni.uploadFile({
							url: 'http://81.68.99.212:8080/img/upload',
							filePath: this.tempFilePath,
							name: 'multipartFile',
							header: {
								token:token,
								'Content-Type': 'multipart/form-data',
							},
							formData: {},
							success: (res) => {
								this.imgId = JSON.parse(res.data).data.id;
								console.log(uni.getStorageSync("ctToken"));
								this.ctAnalyze();
								this.clearImage();
							},
							fail: (err) => {
								console.error('上传失败', err);
							},
						});
					},
					fail: (err) => {
						console.error('获取Token失败', err);
					},
				});
			},
			
			// 上传成功进行图片分析
			ctAnalyze(){
				uni.showLoading({
					title:"正在分析中，请稍等"
				})
				const store = uni.getStorageSync("ctToken")
				const token = store.token; // 获取本地存储中的Token
				const userId = store.info.id;
				uni.request({
					url: 'http://81.68.99.212:8080/record/analyse',
					method:"POST",
					header:{
						token:token,
						'content-type': 'application/json',
					},
					data:{
						userId:userId,
						imgId:this.imgId
					},
					success(res) {
						console.log(res);
						uni.hideLoading();
						
						uni.showToast({
								title: '分析成功',//显示的文字
						        duration: 1500,  //显示多少时间，默认1500毫秒
						        icon: "success"  //自定义显示的图标，默认成功success，错误error,加载loading，不显示图标是none
							})
					},
					fail(err) {
						console.log(err);
					}
				})
			},
			
			// 清除图片
			clearImage() {
			      this.imageUrl = ''; // 清除图片预览
			      this.tempFilePath = ''; // 清除临时文件路径，方便下次重新选择图片
			},
		}
	}
</script>

<style scoped lang="scss">
	.title {
		width: 100%;
		height: 80rpx;
		font-size: 54rpx;
		line-height: 80rpx;
		text-align: center;
	}

	.ct-photo {
		width: 100%;
		height: 420rpx;
		// background-color: red;
	}
	.image-box {
	  width: 100%; /* 设置盒子宽度 */
	  height: 500rpx; /* 设置盒子高度 */
	  background-size: cover; /* 图片尺寸自适应盒子大小，平铺整个盒子 */
	  background-repeat: no-repeat; /* 禁止图片重复平铺 */
	}
</style>