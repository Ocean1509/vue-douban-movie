<!--星级评定组件-->
<template>
	<div class="start">
		<ul>
			<li v-for='n in 5'>
				<span class="glyphicon glyphicon-star"></span>
			</li>
		</ul>
		<div class="show" :style="{width:rate*10+'%'}">
			<ul class='star'>
				<li v-for='n in 5'>
					<span class="glyphicon glyphicon-star"></span>
				</li>
			</ul>
		</div>

	</div>

</template>
<script>
	export default {
		props:['rate']
	}
</script>
<style scoped>
.start{position: relative;width:110px;display: inline-block;}
ul{overflow: hidden;padding:0;margin:0;}

li{list-style: none;display:inline-block;padding:0 2px;font-size: 18px}
.glyphicon{color:#ccc;}
/*.star{position:absolute;top:0;left:0; width:20px;}*/
.show{overflow: hidden;position: absolute;height: 20px;top:0;}
.star{position: absolute;top:0;left:0;width:170px;}
.star li{float:left;}
.star .glyphicon{color:#f3e319;}
</style>