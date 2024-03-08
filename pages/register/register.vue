<template>
	<view class="container">
		<view class="title">
			注册
		</view>
		<view class="register-box">
			<uni-forms ref="form" :modelValue="registerData">
				<uni-forms-item label="昵称" name="nickname">
					<uni-easyinput v-model="registerData.nickname" placeholder="请输入昵称" />
				</uni-forms-item>
				<uni-forms-item label="性别" name="gender">
					<uni-data-checkbox v-model="registerData.gender" :localdata="genders" />
				</uni-forms-item>
				<uni-forms-item label="年龄" name="age">
					<uni-easyinput v-model="registerData.age" placeholder="请输入年龄" />
				</uni-forms-item>
				<uni-forms-item label="账号" name="username">
					<uni-easyinput v-model="registerData.username" placeholder="请输入账号" />
				</uni-forms-item>
				<uni-forms-item label="密码" name="password">
					<uni-easyinput v-model="registerData.password" type="password" placeholder="请输入密码" />
				</uni-forms-item>
				<uni-forms-item label="确认密码" name="confirm_password">
					<uni-easyinput v-model="registerData.confirm_password" type="password" placeholder="请确认密码" />
				</uni-forms-item>
			</uni-forms>
			<view class="btn-box">
				<button @click="gotoLoginPage">登录</button>
				<button type="primary" @click="submit">注册</button>
			</view>
		</view>
	</view>
</template>


<script>
	export default {
		data() {
			return {
				// 性别数据
				genders: [{
					text: '男',
					value: "男"
				}, {
					text: '女',
					value: "女"
				}],
				// 校验规则
				rules: {
					nickname: {
						rules: [{
							required: true,
							errorMessage: '请输入昵称',
						}]
					},
					gender: {
						rules: [{
							required: true,
							errorMessage: '请选择性别',
						}]
					},
					age: {
						rules: [{
							required: true,
							errorMessage: '请输入年龄',
						}, {
							pattern: '^(?:100|[1-9]?\\d)$',
							errorMessage: '年龄不合法'
						}]
					},
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
					},
					confirm_password: {
						rules: [{
							required: true,
							errorMessage: '请确认密码',
						}, {
							validateFunction: function(rule, value, data, callback) {
								console.log(value,data.password);
								if (value !== data.password) {
									console.log(value);
									callback('密码不一致')
								}
								console.log(12);
								return true
							}
						}]
					},
				},
				//注册数据
				registerData: {
					username: '',
					password: '',
					confirm_password: '',
					nickname: '',
					gender: '男',
					age: 18,
				}
			};
		},
		methods: {
			submit(form) {
				this.$refs.form.validate().then((res) => {
					console.log('表单数据信息：', res);
					if (res) {
						this.register();
					}
				}).catch(err => {
					console.log('表单错误信息：', err);
				})
			},
			register() {
				const requestData = {
					username: this.registerData.username,
					password: this.registerData.password,
					confirm_password: this.registerData.confirm_password,
					nickname: this.registerData.nickname,
					gender: this.registerData.gender,
					age: this.registerData.age,
				};
				console.log(requestData);
				uni.request({
					url: 'http://81.68.99.212:8080/user/register',
					method: 'POST',
					data: requestData,
					header: {
						'content-type': 'application/json',
					},
					success: (res) => {
						if (res.data.code === 200) {
							// 注册成功
							uni.redirectTo({
								url: '/pages/login/login'
							});
							console.log('注册成功', res.data);
							
						} else {
							// 注册失败
							console.error('注册失败', res.data.msg);
							
						}
					},
					fail: (err) => {
						console.error('注册请求失败', err);
						
					},
				});
			},
			gotoLoginPage() {
				// 使用uni.navigateTo API来跳转到登录界面
				uni.navigateTo({
					url: '/pages/login/login', // 登录页面的路径
					success: () => {
						console.log('跳转到登录界面成功');
					},
					fail: (err) => {
						console.error('跳转到登录界面失败', err);
					},
				});
			},
			onReady() {
					// 需要在onReady中设置规则
					this.$refs.form.setRules(this.rules)
			},
		},
	};
</script>

<style scoped lang="scss">
	.title {
		text-align: center;
		font-size: 54rpx;
		margin: 30rpx 0 30rpx 0;
	}

	.register-box {
		padding: 0 40rpx 0 40rpx;

		.btn-box {
			display: flex;
		}
	}

	.segmented-control {
		margin-bottom: 15px;
	}

	.button-group {
		margin-top: 15px;
		display: flex;
		justify-content: space-around;
	}

	.form-item {
		display: flex;
		align-items: center;
	}

	.button {
		display: flex;
		align-items: center;
		height: 35px;
		margin-left: 10px;
	}
</style>