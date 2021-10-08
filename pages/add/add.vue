<template>
	<view>
		<uni-nav-bar left-icon="back" @clickLeft="back" left-text=" " right-text="" title="ADD" shadow="true" right-icon="" background-color="#000"
		 color="#fff">
		</uni-nav-bar>
		<view class="content">
			<!-- 头部logo -->
			<view class="header">
				<image :src="logoImage" @click="uploadImg"></image>
			</view>
			<!-- 主体表单 -->
			<view class="main">
				<input v-model="movieData.name" class="inputName" type="text" maxlength="50" placeholder="请输入电影名称"></input>
				
				<picker mode="date" :value="movieData.date" start="1970-01-01" end="2020-12-12" @change="dateChange">
					<view>请选择日期： {{movieData.date}}</view>
				</picker>
				
				<view style="margin: 30rpx 0 40rpx 0">
					<text style="position: relative;top: 20rpx;">请选择分数:</text>
					<uni-number-box style="float: right;position: relative;top:5rpx;right: 100rpx;" :min="0" :max="10" step="0.1" 
					:value="movieData.rating" @change="changeRating"></uni-number-box>
				</view>
				
				<view style="margin-bottom: 50rpx;">
					<picker mode="selector" :range="countries" @change="countryChange">
						<view>请选择国家： {{movieData.country}}</view>
					</picker>
				</view>
				
				<view style="margin-top: 50rpx 0 50rpx 0;">
					<text>请选择电影类别（最多3个）</text>
					<checkbox-group @change="checkboxChange">
						<label class="" v-for="item in items" :key="item.value">
							<view style="float: left;margin: 10rpx;">
								<checkbox :value="item.value" :checked="item.checked" color="#F5C518" />
								{{item.name}}
							</view>
							<view></view>
						</label>
					</checkbox-group>
				</view>
				
				<view>
					<textarea id="textArea" :value="movieData.motto" style="margin: 30rpx 30rpx 0 0;width: 96%;" placeholder="请输入电影简介" 
					auto-height maxlength="256" @confirm="inputMotto" @input="inputMotto" />
				</view>
			</view>
			<button class="btnAdd" @click="startAdd">{{buttonText}}</button>		
		</view>
	</view>
</template>

<script>
	var _self;
	import
	{
		uniNavBar,
		uniNumberBox,
		uniIcons
	} from "@dcloudio/uni-ui"
	export default {
		components: {
			uniNavBar,
			uniNumberBox,
			uniIcons
			},
		name:"add",
		props:["moviename","movietitle","movieimg","moviedate","moviegenre","movierate"],
		data() {
			return {
				mid:0,
				buttonText:"添   加",
				tempFilePaths:"",
				logoImage:"../../static/icon/camera.png",
				items: [],
				countries:[
					"USA","China","Italy","France","Korea","Japan"
				],
				movieData:{
					name:"",
					date:"2000-01-01",
					country:"USA",
					motto:"",
					rating:8,
					genres:"",
				},
				genres:[]
			}
		},
		onLoad(e) {
			if(e.mid){
				uni.showLoading({
					title:'加载数据中',
					mark:true
				})
				console.log(e.mid)
				uni.request({
					url: getApp().globalData.baseUrl + '/imdb/movies/findById',
					data: {
					        id: e.mid
					    },
					success: function(res) {
						// _self.movieData.name = res.data.name;
						// _self.movieData.date = _self.timestampChange(res.data.date);
						// _self.movieData.country = res.data.country;
						// _self.movieData.rating = res.data.rating;
						// _self.movieData.motto = res.data.motto;
						// _self.buttonText = "更新";
						// _self.logoImage = _self.getMovieImg(_self.movieData.name);
						// res.data.genres.map((g)=>_self.genres.push(g.id));
						// _self.genres.map((g)=>_self.items[(parseInt(g)-1)].checked=true);
						uni.hideLoading()
					},
					fail:function(res){
						console.log(res)
					}
				})
				console.log(e.mid)
			}
		},
		created() {
			_self = this;
			uni.request({
				url: getApp().globalData.baseUrl + '/imdb/movies/findAllGenre',
				success: function(res) {
					for(var i = 0;i<res.data.length;i++){
						var g = {value:res.data[i].name,name:res.data[i].name}
						_self.items.push(g)
					}
					// for(var g in _self.genres){
					// 	_self.items[g.id-1].chekced=true;
					// }
				}
			})
		},
		methods: {
			timestampChange: function(timestamp) {
				var date = new Date(parseInt(timestamp) * 1000)
				var tt = [date.getFullYear(), date.getMonth() + 1, date.getDate()].join('-');
				return tt;
			},
			getMovieImg: function(name) {
				var str = name.replace(/\s*/g,"");
				var res = getApp().globalData.imgUrl + str + ".jpg";
				return res;
			},
			uploadImg:function(){
					uni.chooseImage({
					    success: (chooseImageRes) => {
					       _self.tempFilePaths = chooseImageRes.tempFilePaths[0];
							_self.logoImage = _self.tempFilePaths;
					//         const uploadTask = uni.uploadFile({
					//             url: 'https://localhost:8098/imdb/movies/fileupload', 
					//             filePath: tempFilePaths[0],
					//             name: 'file',
					// 			header:{"Content-Type": "multipart/form-data"},
					//             formData:_self.movieData,
					//             success: (uploadFileRes) => {
					//                 console.log(uploadFileRes);
					//             },fail:function(res){
					//             	console.log(res)
					//             }
					//         });
					
					//         uploadTask.onProgressUpdate((res) => {
					//             console.log('上传进度' + res.progress);
					//             console.log('已经上传的数据长度' + res.totalBytesSent);
					//             console.log('预期需要上传的数据总长度' + res.totalBytesExpectedToSend);
					//         });
					    }
					});
				},
				checkboxChange: function (e) {
					var items = this.items,
						values = e.detail.value;
						_self.genres = [];
					for (var i = 0, lenI = items.length; i < lenI; ++i) {
						const item = items[i]
						if(values.includes(item.value)){
							this.$set(item,'checked',true)
							_self.genres.push((i+1))
						}else{
							this.$set(item,'checked',false)
							_self.genres.filter((g)=>g!=(i+1))
						}
					}
				},
				dateChange:function(e){
					this.movieData.date = e.detail.value;
				},
				countryChange:function(e){
					this.movieData.country = this.countries[e.detail.value];
				},
				changeGenre:function(){
					this.genres.map((g)=>this.movieData.genres+=(g+" "))
				},
				changeRating:function(e){
					this.movieData.rating = e;
				},
				inputMotto:function(e){
					this.movieData.motto = e.detail.value;
				},
				toTimestamp:function(){
					this.movieData.date = Date.parse(this.movieData.date)/1000;
				},
				back:function(){
					uni.navigateBack({
						
					})
				},
				startAdd:function(){
					this.changeGenre()
					console.log(this.movieData)
					var readyData = this.movieData
					if(readyData.name.length==0 || readyData.genres.length==0 || readyData.motto.length==0 || _self.logoImage=='../../static/icon/camera.png'){
						uni.showToast({
							title:"电影信息不完整",
							position:'center',
							icon:'none',
							duration:1500
						})
					}else if(_self.genres.length>3){
						uni.showToast({
							title:"类别最多选取3个",
							position:'center',
							icon:'none',
							duration:1500
						})
					}else{
						uni.showModal({
							title:'确认ing',
							content:'您确定要'+_self.buttonText+'吗？',
							success:function(res){
								if(res.confirm)uni.showLoading({
									title:'正在上传中',
									mask:true,
									success:function(){
										_self.toTimestamp()
										uni.uploadFile({
										    url: getApp().globalData.baseUrl+'/imdb/movies/fileupload', 
										    filePath: _self.tempFilePaths,
										    name: 'file',
											header:{"Content-Type": "multipart/form-data"},
										    formData:_self.movieData,
										    success: (uploadFileRes) => {
												uni.hideLoading();
												uni.showToast({
													title:'上传成功',
													duration:1500,
													mask:true,
													success:function(){
														uni.navigateBack({
															
														})
													}
												})
										    },fail:function(res){
										    }
										});
									}
								})
							}
							
						})
					}
				}
				
			}
	}
</script>

<style>
	.btnAdd{
		margin-top: 50rpx;
		margin-bottom: 100rpx;
		width: 500rpx;
		height: 100rpx;
		background-color: #F5C518;
		border-radius: 30rpx;
	}
	.content {
		display: flex;
		flex-direction: column;
		justify-content:center;
		/* margin-top: 128upx; */
	}
	
	/* 头部 logo */
	.header {
		width:500rpx;
		height:300rpx;
		border-radius:30rpx;
		box-shadow:0upx 0upx 60upx 0upx rgba(0,0,0,0.1);
		background-color: #000000; 
		margin-top: 30upx;
		margin-bottom: 30upx;
		margin-left: auto;
		margin-right: auto;
		overflow: hidden;
	}
	.header image{
		width:500rpx;
		height:300rpx;
		border-radius:30rpx;
		
	}
	
	/* 主体 */
	.main {
		display: flex;
		flex-direction: column;
		padding-left: 70upx;
		padding-right: 70upx;
	}
	.inputName{
		display: block;
		margin-top: 30rpx;
		margin-bottom: 30rpx;
		border: #808080;
		border-radius: 5%;
	}
	.brView{
		width: 100%;
		height: 1rpx;
		border-bottom: #000;
	}
</style>
