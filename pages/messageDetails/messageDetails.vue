<template>
	<view class="container">
		<view class="ct_potho">
			<text>CT图片</text>
			<image class="image" :src="dataForCt.url" mode="aspectFit"></image>
		</view>
		<uni-table border stripe >
			<!-- 表格数据行 -->
			<uni-tr>
				<uni-td>昵称</uni-td>
				<uni-td>{{dataForCt.nickname}}</uni-td>
			</uni-tr>
			<uni-tr>
				<uni-td>年龄</uni-td>
				<uni-td>{{dataForCt.age}}</uni-td>
			</uni-tr>
			<uni-tr>
				<uni-td>性别</uni-td>
				<uni-td>{{dataForCt.gender}}</uni-td>
			</uni-tr>
			<uni-tr>
				<uni-td>分析结果</uni-td>
				<uni-td>{{dataForCt.analyse}}</uni-td>
			</uni-tr>
		</uni-table>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				id:null,
				dataForCt:{
					age:0,
					analyse:"",
					nickname:"",
					status:0,
					gender:"",
					isAnalysing:0,
					url:""
				},
			}
		},
		methods: {
			showRecodeDetails(){
				const store = uni.getStorageSync("ctToken")
				const token = store.token; // 获取本地存储中的Token
				console.log(this.id,this.dataForCt);
				uni.request({
					url:`http://81.68.99.212:8080/record/record/${this.id}`,
					method:"GET",
					header:{
						token:token,
						'content-type': 'application/x-www-form-urlencoded',
					},
					data:{},
					success:(res) => {
						console.log(res.data.data);
						console.log(this);
						try{
							this.dataForCt = {...res.data.data};
							
						}catch(e){
							console.log(e);
						}
					},
					fail:(err)=> {
						console.log(err);
					}
				})
			}
		},
		onLoad(options) {
			console.log();
			this.id = options.param;
			
			this.showRecodeDetails();
		}
	}
</script>
	
<style scoped lang="scss">
	.ct_potho {
		.image {
			display: block;
			margin: 0 auto;
		}
	}
	
</style>
