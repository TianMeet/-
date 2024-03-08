<template>
	<view class="container">
		<view class="title">
			登录
		</view>
		<!--登录表单 -->
		<view class="login-box">
			<uni-forms ref="form" :modelValue="loginData" :rules="rules">
				<uni-forms-item label-width="60" label-align="center" label="账号" name="username">
					<uni-easyinput type="text" v-model="loginData.username" placeholder="请输入账号" />
				</uni-forms-item>
				<uni-forms-item label-width="60" label-align="center" label="密码" name="password">
					<uni-easyinput v-model="loginData.password" type="password" placeholder="请输入密码" />
				</uni-forms-item>
			</uni-forms>
			<view class="btn-box">
				<button @click="register">注册</button>
				<button type="primary" @click="submit">登录</button>
			</view>
		</view>
		<view>
			<!-- 提示信息弹窗 -->
			<uni-popup ref="message" type="message">
				<uni-popup-message class="message" :type="msgType" :message="messageText" :duration="2000"></uni-popup-message>
			</uni-popup>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 表单数据
				loginData: {
					username: '',
					password: ''
				},

				// 登录成功的提示信息
				type: 'center',
				msgType: 'success',
				messageText: '登录成功',

				rules: {
					// 对username字段进行必填验证
					username: {
						rules: [{
								required: true,
								errorMessage: '请输入账号',
							},
							{
								minLength: 6,
								maxLength: 10,
								errorMessage: '账号长度在 {minLength} 到 {maxLength} 个字符',
							}
						]
					},
					// 对password字段进行必填验证
					password: {
						rules: [{
							required: true,
							errorMessage: '请输入密码',
						}]
					}
				}
			}
		},
		methods: {
			// 点击提交的方法
			submit() {
				this.$refs.form.validate().then((res) => {
					console.log('表单数据信息：', res);
					if (res) {
						this.login();

					} else {

					}
				}).catch(err => {
					console.log('表单错误信息：', err);
				})
			},
			messageToggle(type) {
				this.msgType = type
				if (type === "success") {
					this.messageText = "登录成功"
				} else {
					this.messageText = "登录失败，请检查账号密码是否错误"
				}
				this.$refs.message.open()
			},
			//登录的方法
			login() {
				const requestData = {
					username: this.loginData.username,
					password: this.loginData.password,
				};
				console.log(requestData);
				uni.request({
					url: 'http://81.68.99.212:8080/user/login',
					method: 'POST',
					data: requestData,
					header: {
						'content-type': 'application/json',
					},
					success: (res) => {
						if (res.data.code === 200) {
							// 登录成功，将ctToken存储在本地存储中
							console.log(res);
							this.messageToggle("success")
							uni.setStorage({
								key: 'ctToken',
								data: {
									token: res.data.data.ctToken,
									info: res.data.data.loginUserVo,
								},
								success: () => {
									console.log('ctToken存储成功');
								},
								fail: (err) => {
									console.error('存储ctToken失败', err);
								},
							});

							setTimeout(() => {
								uni.redirectTo({
									url: '/pages/index/index', // 首页页面的路径
								});
							}, 1000)
							console.log('登录成功', res.data);
						} else {
							console.error('登录失败', res.data);
							this.messageToggle("error")
						}
					},
					fail: (err) => {

						console.error('登录请求失败', err);
					},
				});
			},
			//跳转到注册页面的方法
			register() {
				uni.navigateTo({
					url: '/pages/register/register', // 注册页面的路径
					success: () => {
						console.log('跳转到注册页面成功');
					},
					fail: (err) => {
						console.error('跳转到注册页面失败', err);
					},
				});
			},
		},
		onShow() {
			if (uni.hideHomeButton) {
				// console.log('uni.hideHomeButton');
				wx.hideHomeButton();
			}
		},
	};
</script>
<style scoped lang="scss">
	.title {
		text-align: center;
		font-size: 54rpx;
		margin: 30rpx 0 30rpx 0;
	}

	.login-box {
		padding: 0 40rpx 0 40rpx;

		.btn-box {
			display: flex;
		}
	}

	.popup-success {
		color: #fff;
		background-color: #e1f3d8;
	}

	.popup-error {
		color: #fff;
		background-color: #fde2e2;
	}
	.message {
		text-align: center;
	}
</style>