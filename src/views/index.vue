<template>
	<div  v-if="!$loadingRouteData" >
		<header-bar :title='title'></header-bar>
		<div class="page" transition="fade">
			<section v-for="sections in modules">
				<header v-link="{name:'list',params:{type:sections.name}}">
					<span class="title">{{sections.title}}</span>
					<span class="num" >{{sections.total}}个 ></span>
				</header>
				<ul>
					<li v-for='movie in sections.allMovie' v-link="{name:'show',params:{id:movie.id}}">
						<img :src="movie.images.large" :alt="movie.alt">
						<p>{{movie.title}}</p>
					</li>
				</ul>

			</section>
		</div>
	</div>
	<div v-if="$loadingRouteData">
		<load></load>
	</div>
		
	
	
</template>
<script>
	export default {
		data(){
			return {
				title:'电影',
				modules:[],
				hotwords: []
			}
		},
		route:{
			data(transition){
				document.title=this.title;
				
				const actions = ['in_theaters', 'coming_soon', 'top250']
				var modules = this.modules;
				var hotwords=this.hotwords;
				var promiseActions=actions.map((item,index)=>{
					return ()=>{
						return this.$http.jsonp('http://api.douban.com/v2/movie/' + item,{count:6}).then((response) => {
							 console.log(response);
							modules[index]={
								name:item,
								title:response.data.title.split('-')[0],
								allMovie:response.data.subjects,
								total:response.data.total
							}
							response.data.subjects.forEach((item)=>{
								hotwords.push({
									title:item.title,
									id:item.id
								})
							})
						})
					}
				})
				promiseActions.reduce((curr,next)=>{
					return curr.then(next)
				}, Promise.resolve()).then(()=>{
					transition.next();
					sessionStorage.hotwords = JSON.stringify(hotwords)
					this.$loadingRouteData = false
				})
				
			}
		},
		 
		components:{
			headerBar:require('../components/header-bar.vue'),
			load:require('../components/loading.vue')
		}
	}
</script>

<style scoped>
	.page{margin-top:45px;}
	section{background-color: #fff}
	header{height: 3rem;padding:0 20px;overflow: hidden}
	.title{font-size: 1.6rem;color:#000;line-height: 3rem;float:left;}
	.num{float:right;font-size: 1rem;line-height: 3rem}
	ul{overflow: hidden;padding-left: 15px;padding-right: 10px;overflow: hidden;}
	li{width:33.3333333%;position: relative;color:#fff;text-align: center;font-size: 4rem;overflow: hidden;padding-right: 5px;padding-bottom: 10px;display: inline-block;}
	li img{width:100%;height: 100%;height: 168px}
	li p{position: absolute;bottom:10px;width:100%;background: rgba(0,0,0,.6);font-size: 2rem;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;width:95%;margin-bottom:0;}
</style>