<template>
	<div v-if='!$loadingRouteData'>
		<header-bar :title="title" left="back"></header-bar>
		<div class="banner">
			<div class="backbanner" :style="{backgroundImage:'url('+bimage+')'}"></div>
			<div class="info">
				<div class="pic"><img :src="mimage" :alt="alt"></div>
				<div class="detail">
					<p class="title">{{title}}</p>
					<p class="start">
						<star :rate="rate"></star><span class="rate">{{rate}}</span>	
					</p>
					<p>类型：{{type}}</p>
					<p>主演：{{direct}}</p>
					<p>地区：{{countries}}</p>
				</div>
			</div>
		</div>
		<section>
			<h2>剧情介绍</h2>
			<p>{{summary}}</p>
		</section>
		<section>
		<h2>导演</h2>
			<ul>
				<li v-for='director in directors'>
					<p><img :src="director.pic" alt=""></p>
					<p class="name">{{director.name}} (<a v-link="{name:'director',params:{id:director.id}}">查看</a>)</p>

				</li>
			</ul>
			
			
		</section>
	</div>
	<div v-if='$loadingRouteData'>
		<load></load>
	</div>
</template>
<script>
	export default {
		data(){
			return {
				title:'',
				bimage:'',
				mimage:'',
				alt:'',
				rate:'',
				direct:[],
				countries:[],
				type:[],
				summary:'',
				directors:[]
				// stars:''
			}
		},
		route:{
			data(transition){
				this.$http.jsonp('http://api.douban.com/v2/movie/subject/'+transition.to.params.id).then((response)=>{
					document.title=response.data.title;
					this.title=response.data.title;
					this.bimage=response.data.images.large;
					this.mimage=response.data.images.medium;
					this.alt=response.data.images.alt;
					this.rate=response.data.rating.average;
					this.$loadingRouteData=false;
					response.data.casts.forEach((item)=>{
						this.direct.push(item.name)
					})
					this.countries=response.data.countries;
					this.summary=response.data.summary;
					this.type=response.data.genres;
					var director={};
					response.data.directors.forEach((item)=>{
						director.pic=item.avatars.medium;
						director.id=item.id;
						director.name=item.name;
						this.directors.push(director);
						director={}
					})
					this.directors.pic=response.data.directors[0].avatars.medium;
					this.directors.id=response.data.directors[0].id;
					this.directors.name=response.data.directors[0].name
					console.log(response);
					console.log(this.direct)
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
	.banner{margin-top:45px;overflow: hidden;}
	.backbanner{-webkit-filter: blur(5px);height: 175px;width: 110%}
	.backbanner:before{content: '';display: block;position: absolute;width: 120%;height: 100%;background: #000;opacity: .3;left: -20%;top: -20%;}
	.info{position: absolute;top:60px;color:#fff;}
	.start{line-height: 1}
	.pic{float: left;border:3px solid #fff;margin-left:10px;}
	.detail{float: left;margin-left:12px;margin-top:5px;width:205px;}
	.detail .title{font-size: 18px;margin-bottom:6px;}
	section{padding:10px 15px;background-color: #fff;margin-bottom:10px;}
	.detail .rate{float: right;font-size: 20px;margin-right: 60px;}
	.detail p{overflow: hidden;  white-space: nowrap;text-overflow: ellipsis;max-width: 100%}
	section h2{font-size: 17px;color:#2ba8f4;margin:0;height: 35px;line-height: 35px}
	section p{color:#777;font-size: 14px;}
	section p.name{font-size: 15px;color:#000;}
	section a{text-decoration: none;color:#2ba8f4;}
	section ul{overflow: hidden;padding:0;}
	section ul li{float:left;list-style: none;margin-right:18px;}
</style>