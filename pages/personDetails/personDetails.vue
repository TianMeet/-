<template>
	<view class="container">
		<uni-section title="个人信息" type="line">
			<view class="example">
				<!-- 基础用法，不包含校验规则 -->
				<uni-forms ref="baseForm" :modelValue="personFormData">
					<uni-forms-item label="昵称">
						<uni-easyinput v-model="personFormData.nickname" placeholder="小手一挥out" />
					</uni-forms-item>
					<uni-forms-item label="年龄">
						<uni-easyinput v-model="personFormData.age" placeholder="18" />
					</uni-forms-item>
					<uni-forms-item label="性别">
						<uni-data-checkbox v-model="personFormData.gender" :localdata="sexs" />
					</uni-forms-item>

				</uni-forms>
			</view>
			<button type="primary" @click="changeInfo">修改个人信息</button>
		</uni-section>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 基础表单数据
				personFormData: {
					nickname: '',
					age: '18',
					gender: "",
					id:null
				},
				sexs: [{
					text: '男',
					value: "男"
				}, {
					text: '女',
					value: "女"
				}],
			}
		},
		onLoad() {
			// 页面加载时初始化个人信息
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
						token:token,
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: (res) => {
						if (res.data.code === 200) {
							const userInfo = res.data.data;
							// 将获取到的数据赋值给 personFormData
							this.personFormData.id = userInfo.id;
							this.personFormData.age = userInfo.age;
							this.personFormData.gender = userInfo.gender;
							this.personFormData.nickname = userInfo.nickname;
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
			// 修改个人信息的方法
			changeInfo() {
				uni.request({
					url: 'http://81.68.99.212:8080/user/update',
					method: 'PUT',
					data: this.personFormData,
					header: {
						token:token,
						'content-type': 'application/json',
					},
					success: (res) => {
						if (res.data.code === 200) {
							console.log('个人信息修改成功');
							
						} else {
							console.error('个人信息修改失败', res.data.msg);
						}
					},
					fail: (err) => {
						console.error('个人信息修改请求失败', err);
					},
				});
			}
		}
	}
</script>
<style lang="scss">
	.example {
		padding: 15px;
		background-color: #fff;
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