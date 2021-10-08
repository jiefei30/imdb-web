<template>
	<view>
		<uni-collapse-item :title="changemoviename(moviename,number)" showAnimation="true">
			<view style="padding: 10rpx;">
				<uni-card :title="genreChange(moviegenre)" mode="style" :is-shadow="true" :thumbnail="getMovieImg(moviename)"
				  :extra="timestampChange(moviedate,moviecountry)" :note="moviemotto">
					<uni-swipe-action>
						<uni-swipe-action-item :options="options" @click="onClick" @change="change">
							<view style="margin-top: 20rpx;">
								<view style="position: relative;top: 20rpx;">
									<uni-rate style="float: right;position: relative;left: 10rpx;" disabled="true" :value="movierate" max="10" size="12">
									</uni-rate>
								</view>
								<view style="float: right;position: relative;bottom: 15rpx;left:310rpx">
									<text>{{movierate}}</text>
								</view>			
							</view>
						</uni-swipe-action-item>
					</uni-swipe-action>
				</uni-card>
				<view style="display: flex;justify-content: space-around;width: 100%;height: 70rpx;position: relative;right: 50rpx;">
					<view>
						<uni-icons type="hand-thumbsup" color='#F5C518' size="20"></uni-icons>
						<uni-icons style="float: right;position: relative;left: 10rpx;" type="hand-thumbsdown" color='#000' size="20"></uni-icons>
					</view>
					<view style="position: relative;left:100rpx;">
						<uni-fav :checked="true" class="favBtn" circle="true" bg-color-checked='#F5C518' fg-color="#666666" fg-color-checked='#000'
						bg-color="#eee" @click="clickFav" :content-text="contentFav"></uni-fav>
					</view>
				</view>
			</view>
		</uni-collapse-item>

	</view>
</template>

<script>
	var _self;
	import {
		uniCard,
		uniCollapse,
		uniCollapseItem,
		uniRate,
		uniSwipeAction,
		uniSwipeActionItem,
		uniFav,
		uniIcons,
		uniBadge
	}
	from "@dcloudio/uni-ui"
	export default {
		name: "movie",
		props: ["number","movieid","moviename","moviecountry", "moviemotto", "moviedate", "moviegenre", "movierate"],
		components: {
			uniCard,
			uniCollapse,
			uniCollapseItem,
			uniRate,
			uniSwipeAction,
			uniSwipeActionItem,
			uniFav,
			uniIcons,
			uniBadge
		},

		data() {
			return {
				options: [{
					text: '修改',
					style: {
						backgroundColor: '#F5C518'
					}
				}, {
					text: '删除',
					style: {
						backgroundColor: '#000'
					}
				}],
				contentFav:{
					contentDefault: '收藏',
					contentFav:'已收藏'
				}
			}
		},
		created(){
		},
		methods: {
			onClick(e) {
				if(e.index==1)this.startDelete()
				else{
					uni.navigateTo({
						url:'../add/add?mid='+this.movieid
					})
				}
			},
			change(open) {
				console.log('当前开启状态：' + open)
			},
			clickFav:function(e){
				console.log(e)
			},
			changemoviename: (name, index) => (index+1) + ".  " + name,
			timestampChange: function(timestamp, country) {
				var newDate = new Date();
				newDate.setTime(timestamp * 1000);
				var res = newDate.toDateString() + '   ' + '(' + country + ')'
				return res;
			},
			genreChange: function(genre) {
				var res = "";
				genre.map((g) => res += (g.name + "   "));
				return res;
			},
			getMovieImg: function(name) {
				var str = name.replace(/\s*/g, "");
				var res = getApp().globalData.imgUrl + str + ".jpg";
				return res;
			},
			startDelete:function(){
					_self = this;
					uni.showModal({
					    title: '警告',
					    content: '您确定要删除这个电影吗？',
					    success: function (res) {
					        if (res.confirm) {
								uni.request({
								    url: getApp().globalData.baseUrl+'/imdb/movies/deleteMovie', 
								    data:{
											id:_self.movieid
										},
								    success: (res) => {
								        console.log(res.data);
								    },
									fail: (res) => {
										console.log(res);
									}
								});
					    }
					}
				})
			}
		}	
	}
	
</script>

<style>
</style>
