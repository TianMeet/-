<template>
	<view class="container">
		<view class="ct_record_box">
			<view class="ct_img" v-for="(item,index) in dataList" @click="showDetails(item.id)" :key="item.id">
				<view class="left-box">
					<text>{{nickname+"-"+((index+1)+"")}}</text>
					<text>{{item.imgVo.createTime}}</text>
				</view>
				<image class="img" :src="item.imgVo.url" mode="aspectFit"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				paginationObj: {
					current: 1, // 当前页
					pageSize: 4, // 每页条数
					pages: 1, // 总页数
					total: 0 // 总条数
				},
				isLoadMore: false,
				dataList: [],
				nickname:""
			}

		},
		onLoad() {
			this.loadData(); // 在页面显示时自动加载数据
		},
		methods: {
			loadData() {
				const store = uni.getStorageSync("ctToken")
				const token = store.token; // 获取本地存储中的Token
				const userId = store.info.id;
				this.nickname = store.info.nickname;
				uni.request({
					url: `http://81.68.99.212:8080/record/user/${userId}`, 
					method: 'GET', 
					header: {
						token:token,
						'content-type': 'application/x-www-form-urlencoded',
					},
					data: {

						pageNum: this.paginationObj.current,
						pageSize: this.paginationObj.pageSize

					},
					success: (res) => {
						this.paginationObj.total = res.data.data.total;
						this.dataList = [...this.dataList, ...res.data.data.rows];
					},
					fail: (err) => {
						console.error('请求失败', err);
					},
				});
			},
			showDetails(id) {
				uni.navigateTo({
					url: `/pages/messageDetails/messageDetails?param=` + id
				});
			},


		},
		// 下拉刷新
		onPullDownRefresh() {
			let that = this
			that.dataList = []; //清空页面数据
			that.paginationObj.current = 1; //初始查询页数
			that.loadData()
			setTimeout(function() {
				uni.stopPullDownRefresh(); //停止页面加载动画
			}, 1000);
		},
		onReachBottom() {
			// 触底的时候请求数据，即为上拉加载更多
			if (this.paginationObj.current * this.paginationObj.pageSize < +this.paginationObj.total && !this.isLoadMore) {
				this.isLoadMore = true
				this.paginationObj.current++
				this.loadData()
			} else {
				console.log(this.paginationObj.current, this.paginationObj.pageSize, +this.paginationObj.total);
				this.isLoadMore = false
			}
		},
	};
</script>
<style scoped lang="scss">
	.ct_img {
		display: flex;
		justify-content: space-between;
		padding: 60rpx;

		.left-box {
			display: flex;
			flex-direction: column;
			justify-content: space-between;
		}

		.img {
			width: 200rpx;
			height: 160rpx;
		}
	}
</style>