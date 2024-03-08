<script>
	export default {
		onLaunch: function() {
			console.log('App Launch111'+uni.getStorageSync('ctToken').token);
			// token标志来判断
			let token = uni.getStorageSync('ctToken')?.token;
			console.log(token);
			if (token) {
				//存在则关闭启动页进入首页
				console.log(111);
				plus.navigator.closeSplashscreen();

			} else {
				//不存在则跳转至登录页
				console.log(111);
				uni.reLaunch({
					url: "/pages/login/login",
					success: () => {
						plus.navigator.closeSplashscreen();
					}
				})
			}
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	/*每个页面公共css */
	@import '@/uni_modules/uni-scss/index.scss';
	/* #ifndef APP-NVUE */
	@import '@/static/customicons.css';

	// 设置整个项目的背景色
	page {
		background-color: #f5f5f5;
	}

	/* #endif */
	.example-info {
		font-size: 14px;
		color: #333;
		padding: 10px;
	}
</style>