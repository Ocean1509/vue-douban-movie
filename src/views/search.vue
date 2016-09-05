<template>
	<div class="header-top-search">
		<div class="search">
			<span class="search-top glyphicon glyphicon-search" aria-hidden="true"></span>
			<input type="text" placeholder="请输入关键字" v-model="keyword">
			<button @click="cancel">取消</button>
		</div>

	</div>
	<div class="title"  v-show='!search.length'>
		<h2>大家都在搜</h2>
		<div class="search-content">
			<ul>
				<li v-for="hotword in hotwords" v-link="{name:'show',params:{id:hotword.id}}">{{hotword.title}}</li>
			</ul>
		</div>
	</div>
	<div class="show-search" v-show='search.length'>
		<ul>
			<li v-for='list in search' transition="staggered" stagger="50" track-by="$index" v-link="{name:'show',params:{id:list.id}}">{{list.title}}<span class="icon glyphicon glyphicon-chevron-right" aria-hidden="true"></span></li>
		</ul>
	</div>
</template>
<script>
	export default {
		data(){
			return {
				title:'搜索',
				hotwords:[],
				keyword:'',
				search:[]
			}
		},
		methods:{
			cancel:function(){
				// window.location.href='/'
				history.back()
			}
		},
		route:{
			data(transition){
				document.title=this.title
				this.hotwords=JSON.parse(sessionStorage.hotwords)	
			}
		},
		watch:{
			keyword(val){
				if(val.trim()){
					this.$http.jsonp('http://api.douban.com/v2/movie/search?q='+val).then((response)=>{
						this.search=response.data.subjects;
						console.log(this.search)
					})
				}else{
					this.search=[]
				}
			}
		}
	}
</script>
<style scoped>
.header-top-search{height: 45px;padding:0 20px;background-color: #ebeced;padding:6px 0;position:fixed;top:0;width:100%;z-index:999;}

.header-top-search .search{height: 30px;line-height: 30px;margin-left:10px;}
input{width:86%;height: 100%;margin:0 auto;border-radius:4px;outline:0;border:0;display: inline-block;padding-left:35px;}
button{border:none;background-color: #ebeced;cursor: default;color:#2ba8f4;color: #00a5e0;font-size: 16px;outline:none;}
.search-top{position: absolute;top:15px;left:20px;}
h2{margin:0;font-size: 1.6rem;margin:10px 18px;}
.search-content{background-color: #fff;padding:15px 5px}
.search-content ul{overflow: hidden;padding:0;}
.search-content ul li{float:left;list-style: none;border:1px solid #cacccd;border-radius:15px;padding:0 12px;line-height: 2.5rem;height: 2.5rem;color:#777;font-size: 14p x;margin:5px 5px;}
.show-search ul{overflow: hidden;padding-left:25px;padding-right: 25px}
.show-search ul li{list-style: none; width:100%;transition: all .4s ease;overflow: hidden;height: 40px;padding: 0;line-height: 40px;color:#777;position: relative;}
.show-search .icon{position: absolute;right: 0;top:10px;color:#c7c7c7;}
</style>