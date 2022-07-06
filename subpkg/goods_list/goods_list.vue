<template>
	<view>
		<view class="goods-list">
			<view v-for="(item, i) in goodsList" :key="i" @click="gotoDetail(item)">
				<!-- 为 my-goods 组件动态绑定 goods 属性的值 -->
				<my-goods :goods="item"></my-goods>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				queryObj: {
					query: '',
					cid: '',
					pagenum: 1,
					pagesize: 10
				},
				goodsList: [],
				total: 0,
			};
		},
		onLoad(options) {
			this.queryObj.query = options.query || '';
			this.queryObj.cid = options.cid || '';
			this.getGoodsList();
		},
		methods: {
			async getGoodsList(fun) {
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/goods/search', this.queryObj);
				fun && fun();
				if (res.meta.status !== 200) return uni.$showMsg();
				this.goodsList = [...this.goodsList, ...res.message.goods];
				this.total = res.message.total;
			},
			gotoDetail(item){
				uni.navigateTo({
					url:'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
				})
			}
		},
		onReachBottom() {
			if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕！')
			this.queryObj.pagenum += 1;
			this.getGoodsList();
		},
		onPullDownRefresh(){
			 this.queryObj.pagenum = 1
			  this.total = 0
			  this.isloading = false
			  this.goodsList = []
			this.getGoodsList(() => uni.stopPullDownRefresh())
		}
	}
</script>

<style lang="scss">

</style>
