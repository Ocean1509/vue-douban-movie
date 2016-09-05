<template>
	<div v-if='!$loadingRouteData'>
		<header-bar :title="title" left="back"></header-bar>
		<div class="banner">
			<div class="blur" :style="{backgroundImage:'url('+bimage+')'}"></div>
			<div class="info">
				<div class="pic"><img :src="mimage" :alt="alt"></div>
				<div class="detail">
					<p class="title">{{title}}</p>
					<p>性别：{{sex}}</p>
					<p>地区：{{country}}</p>
				</div>
			</div>
		</div>
		<section>
			<h2>作品<span>{{works.length}}个</span></h2>
			<ul>
				<li v-for='work in works' v-link="{name:'show',params:{id:work.id}}">
					<div class="spic"><img :src="work.image" alt="work.alt"></div>
					<div class="wdetail">
						<p class="title">[{{work.roles}}] {{work.title}}</p>
						<star :rate='work.rate'></star><span class="rate">{{work.rate}}</span>
						<p class="type">{{work.type}}</p>
						<p class='name'>{{work.name}}</p>
					</div>
					<span class='dire'>></span>
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
				bimage:'',
				mimage:'',
				title:'',
				sex:'',
				conntry:'',
				works:[],
			}
		},
		route:{
			data(transition){
				console.log(transition.to.params.id);
				this.$http.jsonp('http://api.douban.com/v2/movie/celebrity/' + transition.to.params.id).then((response) =>{
					document.title=response.data.name;
					this.title=response.data.name;
					this.bimage=response.data.avatars.large;
					this.mimage=response.data.avatars.medium;
					this.sex=response.data.gender;
					this.country=response.data.born_place;
					// this.works=response.data.works;
					// console.log(this.works)
					var work={};
					var name=[];
					response.data.works.forEach((item)=>{
						item.subject.casts.forEach((item)=>{
							name.push(item.name)
						})
						work={
							roles:item.roles,
							rate:item.subject.rating.average,
							title:item.subject.title,
							id:item.subject.id,
							name:name,
							type:item.subject.genres,
							image:item.subject.images.small,
							alt:item.subject.alt
						}
						this.works.push(work);
						work={};
						name=[];
					})
					console.log(response);
					this.$loadingRouteData=false;
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
	ul{padding:0;}
	li{list-style:none;}
	p{margin:0;}
	.banner{margin-top:45px;overflow: hidden;}
	.blur{-webkit-filter: blur(5px);height: 175px;width: 110%;background-size: cover}
	.blur:before{content: '';display: block;position: absolute;width: 120%;height: 55%;background: #000;opacity: .3;left: -20%;top: -20%;}
	.info{position: absolute;top:60px;color:#fff;}
	/*.start{line-height: 1}*/
	.pic{float: left;border:3px solid #fff;margin-left:10px;}
	.detail{float: left;margin-left:12px;margin-top:5px;width:247px;}
	.detail .title{font-size: 18px;margin-bottom:6px;}
	.detail .rate{float: right;font-size: 20px;margin-right: 60px;}
	.detail p{overflow: hidden;  white-space: nowrap;text-overflow: ellipsis;max-width: 100%}
	.pic img{width: 100px;height: 140px;display: block;border:2px solid #fff;}
	section{padding:5px 15px;background-color: #fff;margin-bottom:10px;}
	section h2{font-size: 17px;color:#2ba8f4;margin:0;height: 35px;line-height: 35px}
	section h2 span{float: right;color:#777;}
	section ul li{overflow: hidden;margin-bottom:12px;}
	.spic{float:left;}
	.spic img{width: 60px;height: 84px;}
	section .wdetail{float:left;font-size: 14px;margin-left:20px;width:60%;}
	.wdetail .rate{position: absolute;left:220px;font-size: 14px;margin-left: 16px;color:#777;}
	section .wdetail p{max-width: 100%;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;}
	section .wdetail .title{font-size: 18px}
	section .wdetail .type{margin-top:-9px;color:#777;}
	section .wdetail .name{color:#777;}
	.dire{font-size: 27px;height: 84px;line-height: 70px;float: left;color:#c7c7c7;margin-left:40px;}
</style>