<template>
	<view class="bg">
		<view class="input">
			<view class="paste" v-if="pasteInfo!=''" @tap="tappaste">
				{{pasteInfo}}
			</view>
			<textarea @focus="focus" v-model="info" maxlength="-1" placeholder="粘贴请求头"/>
		</view>
		<view class="view" v-if="info1 != ''">
			<view>
				<text style="font-size: 14px; font-weight: bold;font-size: #333;">格式化:</text>
				<text class="box"@tap="paste(info1)">{{info1}}</text>	
			</view>
		</view>
		<view class="view" v-if="method != ''">
			<view>
				<text style="font-size: 14px; font-weight: bold;font-size: #333;">请求方式:</text>
				<text class="box"  @tap="paste(method)">{{method}}</text>
			</view>
		</view>
		<view class="view" v-if="url != ''">
			<view>
				<text style="font-size: 14px; font-weight: bold;font-size: #333;">请求地址:</text>
				<text class="box"  @tap="paste(url)">{{url}}</text>
			</view>
		</view>
		<view class="view" v-if="http != ''">
			<view>
				<text style="font-size: 14px; font-weight: bold;font-size: #333;">协议版本:</text>
				<text class="box"  @tap="paste(http)">{{http}}</text>
			</view>
		</view>
		<view class="view" v-for="(value,key) in allList">
			<view>
				<text style="font-size: 14px; font-weight: bold;font-size: #333;">{{key}}:</text>
				<view style="display: flex;">
					<input type="text"  style="flex: 1;" v-model="allList[key]"/>
					<text class="box"  @tap="paste(allList[key])">复制</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				pasteInfo:'',
				allList:{},
				info:'',
				info1:'',
				method:'',
				url:'',
				http:''
			}
		},
		watch:{
			info(){
				let list = this.info.split(/\n/)
				let allList = []
				
				for(let index in list){
					if(index == 0&&list[index].indexOf('HTTP/')!=-1){
						allList = [...allList,list[index].split(' ')]
					}else{
						allList = [...allList,list[index].split(/\:/)]
					}
				}
				
				if(allList[0].length == 3){
					this.method = allList[0][0]
					this.url = allList[0][1]
					this.http = allList[0][2]
					allList.splice(0,1)
				}
				
				let data = {}
				let iSinfo = true
				for(let i of allList){
					if(i.length == 2&&i[1]!=''){
						data[i[0].replace(/\s+/g,"")] = i[1].replace(/\s+/g,"")
					}else{
						let x = i[0]
						i.splice(0,1)
						let y = i.join(':')
						i[0] = x
						i[1] = y
					}
				}
				console.log(33333)
				console.log(data)
				console.log(3333)
				if(iSinfo){
					this.allList = data
					this.info1 = JSON.stringify(data).split(/\"\,\"/).join('",\n\r"').split(/\{\"/).join('{\n"').split(/\"\}/).join('"\n}')
					uni.setClipboardData({
						data:this.info1
					})
				}
			},
		},
		methods: {
			tappaste(){
				this.info = this.pasteInfo
				this.pasteInfo = ''
			},
			paste(info){
				uni.setClipboardData({
					data:info
				})
			},
			focus(){
				uni.getClipboardData({
					success: (data) => {
						this.pasteInfo = data.data
					}
				})
				
			}
		}
	}
</script>

<style lang="scss">
	.bg{
		padding: 30rpx;
	}
	.input{
		margin-top: 30rpx;
		padding: 30rpx;
		border: 1rpx #007AFF solid;
		border-radius: 10rpx;
		position: relative;
		.paste{
			width: 200rpx;
			background-color: #f2f2f2;
			border-radius:15rpx;
			padding: 15rpx;
			color: #999;
			position: absolute;
			right: 30rpx;
			top: 30rpx;
			z-index: 9999;
			overflow: hidden;
			text-overflow:ellipsis;
			white-space: nowrap;
		}
	}
	.view{
		display: flex;
		flex-direction: column;
		view{
			display: flex;
			flex-direction: column;
			padding: 15rpx 0;
			.box{
				font-size: 12px; 
				font-size: #999;
				background-color: #eee;
				border-radius: 10rpx;
				padding:15rpx;
				word-wrap:break-word;
				&:active{
					background-color: #f2f2f2;
				}
			}
		}
	}
	
</style>
