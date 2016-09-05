<template>
	<div v-if='!$loadingRouteData'>
		<header-bar :title='title' left='back'></header-bar>
		<ul>
			<li v-for='list in lists' v-link="{name:'show',params:{id:list.id}}">
				<div><img :src="list.images.medium" alt=""></div>
				<div class='detail'>
					<p class='title'>{{list.title}}</p>
					<star :rate="list.rating.average"></star><span class="rate">{{list.rating.average}}</span>
					<p class='type'>类型：{{list.genres.join(' ')}}</p>
					<p class='directors'>主演：<span v-for='r in list.casts'>{{r.name}} </span></p>
					<span class='dire'>></span>
				</div>
			</li>
		</ul>
		

		<div class="spinner" v-show='more'>
			<div class="bounce1"></div>
			<div class="bounce2"></div>
			<div class="bounce3"></div>
		</div>
		
	</div>
	<div v-if='$loadingRouteData'>
		<load></load>
	</div>
</template>
<script>
	export default{
		data(){
			return{
				count:10,
				type:'',
				page:1,
				lists:[],
				more:true
			}
		},
		route:{
			data(transition){
				this.type=transition.to.params.type;
				this.pageData();
				window.addEventListener('scroll',this.scroll)
			},
			deactivate(transition){
				window.removeEventListener('scroll',this.scroll);
				transition.next()
			}
		},
		methods:{
			scroll(e){
				if(document.body.scrollHeight-window.screen.height-document.body.scrollTop<=0){
					this.pageData()
				}
			},
			pageData () {
				console.log(this.type)
				this.$http.jsonp('http://api.douban.com/v2/movie/' + this.type, {
					count: this.count,
					start: (this.page - 1) * this.count
				}).then((response) => {
					console.log(response)
					if(this.page === 1){
						this.$loadingRouteData = false
						document.title = this.title = response.data.title.split('-')[0]
					}

					if(response.data && response.data.subjects.length){
						this.page ++
						this.lists = this.lists.concat(response.data.subjects)
					}else{
						this.more = false
					}
				})
			}
		},
		components:{
			headerBar:require('../components/header-bar.vue'),
			star:require('../components/star.vue'),
			load:require('../components/loading.vue')
		}
	}
</script>
<style scoped>
p{margin:0;}
p.title{font-size:20px;margin:5px;}
ul{margin-top:50px;margin-left:22px;padding:0;}
ul li{list-style:none;overflow: hidden;margin:10px 0;}
ul li div{float:left;}
.detail{width:230px;position: relative;}
.detail p{overflow: hidden;white-space: nowrap;text-overflow: ellipsis;max-width: 100%;}
.type,.directors{font-size: 12px;margin-top:6px;}
ul li img{width:86px;height: 116px;margin-right: 10px}
.dire{position: absolute;top:10px;right:0px;font-size: 27px;height: 84px;line-height: 70px;float: left;color:#c7c7c7;margin-left:40px;}

/*加载中样式*/
.spinner {
  margin: 15px auto 0;
  width: 150px;
  text-align: center;
}
.spinner > div {
  width: 15px;
  height: 15px;
  background-color: #2ba8f4;
  border-radius: 100%;
  display: inline-block;
  -webkit-animation: bouncedelay 1.4s infinite ease-in-out;
  animation: bouncedelay 1.4s infinite ease-in-out;
  -webkit-animation-fill-mode: both;
  animation-fill-mode: both;
}
.spinner .bounce1 {
  -webkit-animation-delay: -0.32s;
  animation-delay: -0.32s;
}
.spinner .bounce2 {
  -webkit-animation-delay: -0.16s;
  animation-delay: -0.16s;
}
@-webkit-keyframes bouncedelay {
  0%, 80%, 100% { -webkit-transform: scale(0.0) }
  40% { -webkit-transform: scale(1.0) }
}
@keyframes bouncedelay {
  0%, 80%, 100% { 
    transform: scale(0.0);
    -webkit-transform: scale(0.0);
  } 40% { 
    transform: scale(1.0);
    -webkit-transform: scale(1.0);
  }

} 


</style>