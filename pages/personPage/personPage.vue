<template>
	<view class="container">
		<!-- 头部区域展示头像，用户名，用户ID等信息 -->
		<view class="header">
			<view class="header-avatar">
				<u-avatar size="60" :text="text"></u-avatar>
				<view class="text-xl">{{nickname}}</view>
				<text class="text-s">id:{{id}}</text>
			</view>
		</view>
		<u-divider text="分割线" :dot="true"></u-divider>
		<view class="body">
			<view class="body-function">
				<navigator url="../personDetails/personDetails">
					<text>账户信息</text>
					<u-icon name="arrow-right"></u-icon>
				</navigator>
			</view>
		</view>
		<view class="footer">
			<button @click="confirmLogout" type="primary">登出</button>
		</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				nickname: '',
				id: null,
			}
		},
		onLoad() {
			this.initUserInfo();
		},
		onShow() {
			this.initUserInfo();
		},
		methods: {
			// 初始化个人信息的方法
			initUserInfo() {
				const store = uni.getStorageSync("ctToken")
				const token = store.token; // 获取本地存储中的Token
				const userId = store.info.id;
				// 发起请求来获取个人信息
				uni.request({
					url: `http://81.68.99.212:8080/user/info/${userId}`,
					method: 'GET',
					header: {
						// Authorization: 'Bearer ' + token,
						token:token,
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: (res) => {
						if (res.data.code === 200) {
							const userInfo = res.data.data;
							this.id = userInfo.id;
							this.nickname = userInfo.nickname;
						} else {
							console.error('获取个人信息失败', res.data.msg);
							// 处理获取个人信息失败逻辑，比如弹出错误提示
						}
					},
					fail: (err) => {
						console.error('获取个人信息请求失败', err);
					},
				});
			},
			confirmLogout() {
				uni.showModal({
					title: '确认登出',
					content: '确定要登出吗？',
					success: (res) => {
						if (res.confirm) {
							// 用户点击确定，执行登出操作
							this.logout();
						}
					},
				});
			},
			logout() {
				this.sendLogoutRequest();
			},
			sendLogoutRequest() {
				const store = uni.getStorageSync("ctToken")
				const token = store.token; // 获取本地存储中的Token
				console.log(token);
				uni.request({
					url: 'http://81.68.99.212:8080/user/logout',
					method: 'POST',
					header: {
						// Authorization: 'Bearer ' + token,
						token:token,
						'content-type': 'application/json',
					},
					success: (res) => {
						if (res.data.code === 200) {
							console.log('登出成功');
							uni.removeStorage({
								key: 'ctToken',
								success: () => {
									console.log('本地存储的 token 已删除');
									// 发送 POST 请求登出
								},
								fail: (err) => {
									console.error('删除本地存储的 token 失败', err);
								},
							});
							// 跳转到登录界面
							uni.redirectTo({
								url: '/pages/login/login', // 登录页面的路径
							});
						} else {
							console.error('登出失败', res.data.msg);
							// 处理登出失败逻辑，比如弹出错误提示
						}
					},
					fail: (err) => {
						uni.redirectTo({
							url: '/pages/login/login', // 登录页面的路径
						});
						console.error('登出请求失败', err);
					},
				});
			},
		},
	};
</script>


<style lang="scss" scoped>
	.body {
		padding-left: 20rpx;
		padding-right: 20rpx;
		background-color: #fff;

		.body-function:nth-child(1) {
			border-bottom: 1px solid #eee;
		}

		.body-function {
			width: 100%;
			height: 100rpx;
			line-height: 100rpx;

			navigator {
				width: 100%;
				height: 100%;
				display: flex;
				justify-content: space-between;
			}
		}
	}

	.header {
		background-color: #fff;
		padding-top: 60rpx;
		padding-bottom: 60rpx;
		border-radius: 10rpx;

		.header-avatar {
			display: flex;
			justify-content: center;
			align-items: center;
			flex-direction: column;

			view {
				margin-top: 30rpx;
			}

			text {
				padding: 10rpx;
				color: #999;
			}
		}
	}
</style>