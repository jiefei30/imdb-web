<template>
	<view>
		<!-- #ifdef MP-WEIXIN -->
			<button type="primary" open-type="getUserInfo" 
			@getuserinfo="getUserInfo" withCredentials="true">微信登陆</button>
		<!-- #endif --> 
		
		<!-- #ifdef APP-PLUS -->
			<button type="primary" open-type="getPhoneNumber" @click="appWxLogin()">APP登陆</button>
		<!-- #endif --> 
		
		
	</view>
</template>

<script> 
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			//app专用
			appWxLogin:function(){
				uni.getProvider({
				    service: 'oauth',
				    success: function (res) {
				        console.log(res.provider)
				        if (~res.provider.indexOf('weixin')) {
				            uni.login({
				                provider: 'weixin',
				                success: function (loginRes) {
				                    console.log(JSON.stringify(loginRes));
				                }
				            });
				        }
				    }
				});
			},
			//小程序专用
			getUserInfo:function(res){
				if(!res.detail.iv){
					uni.showModal({
						title:"您取消了授权，登录失败",
						icon:"none"
					})
				}else{
					uni.showModal({
						title:"欢 迎回来 "+res.detail.userInfo.nickName,
						icon:"none",
						complete:function(){
							uni.login({
								provider:'weixin',
								success:function(res2){
									console.log(res2.code);
									//获取sessionKey
									uni.request({
										url:'https://hoa.hcoder.net/xcxencode/?c=sk&appid=wxbb7f9f1f2c6f4f33&secret=739b970b832f0df158f54c494a08e440&code='+res2.code,
										success:function(res3){
											console.log(res3);
										}
									});
									
								}
							});
							// var pages = getCurrentPages();
							// var prevPage = pages[pages.length-2].setData({
							// 	username : res.detail.userInfo.nickName,
							// 	userAvator:res.detail.userInfo.avatarUrl,
							// 	isLogin:""
							// })
							uni.navigateBack()
						}
					})
				}
			}
		}
	}
</script>

<style>

</style>
