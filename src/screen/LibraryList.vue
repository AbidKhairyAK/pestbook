<template>
	<view class="container">
		<view class="search-wrapper">
			<nb-item rounded class="search-item">
		    <nb-input class="search-input" v-model="query" :placeholder="ui.search+' '+s+'...'"/>
		    <touchable-opacity :on-press="() => getData()">
			    <nb-icon name="search" class="search-icon"/>
			  </touchable-opacity>
		  </nb-item>
		</view>

		<activity-indicator :style="{marginTop: 30}" :size="50" color="#255d00" v-if="loading"/>

	  <scroll-view v-else>
	  	<view class="list">
	  		<touchable-opacity :on-press="() => navigate({id: item.id})" v-for="(item, index) in data" class="list-item" :key="index">
		  		<view>
		  			<view class="list-image-wrapper" :on-layout="(event) => getListWidth(event)">
		  				<image class="list-image" 
		  					resize-mode="contain" 
		  					:source="{uri: $url+'/libraries/image/'+item.thumbnail}" 
		  					:style="{height: listWidth}"
		  				/>
		  			</view>
		  			<c-text class="list-title" align="c" :size="11">{{ is_ind ? item.name : item.name_en }}</c-text>
		  		</view>
		  	</touchable-opacity>
	  	</view>
	  </scroll-view>
	</view>
</template>

<script>
	import CText from '../item/CText';

	export default {
		components: {CText},
		props: ['navigation', 'title', 's'],
		data: () => ({
			listWidth: 0,
			data: {},
			loading: true,
			query: ''
		}),
		computed: {
			ui() {
				return this.$store.getters.ui.libraryScreen;
			},
			is_ind() {
				return this.$store.getters.is_ind;
			}
		},
		methods: {
			getListWidth(event) {
				this.listWidth = event.nativeEvent.layout.width;
			},
			navigate(item) {
				this.navigation.navigate('Detail', item);
			},
			getData() {
				this.loading = true;
				this.$axios.get('/libraries/list/'+this.title+'?s='+this.query)
				.then(({data}) => {
					this.data = data.data;
					this.loading = false;
				});
			},
		},
		created() {
			this.getData();
		}
	}
</script>

<style scoped>
	.search-wrapper {
		margin-horizontal: 15;
		margin-top: 30;
		margin-bottom: 10;
	}
	.search-item {
		padding-horizontal: 10;
	}
	.search-input {
		font-family: Dosis-Regular;
	}
	.search-icon {
		background-color: #558b2f;
		padding-vertical: 5;
		border-radius: 20;
		color: #eee;
	}
	.list {
		padding-top: 20;
		padding-bottom: 100;
		padding-horizontal: 15;
		flex-direction: row;
		flex-wrap: wrap;
		/*justify-content: space-between;*/
	}
	.list-item {
		width: 22%;
		background-color: #fff;
		border-radius: 10;
		overflow: hidden;
		elevation: 5;
		margin-bottom: 15;
		margin-horizontal: 1.5%;
	}
	.list-image-wrapper {
		width: 100%;
		aspect-ratio: 1;
		overflow: hidden;
	}
	.list-image {
		width: 200%;
		align-self: center;
	}
	.list-title {
		padding: 5;
	}
</style>