<template>
  <view class="container">
		<u-navbar :is-back="false" title="" :background="{ backgroundColor: '#F8F8F8' }">
			<view class="navbar">
				<view class="app-name">快聊</view>
				<view class="app-operate">
					<u-icon name="plus-circle" size="50" @click="showMenu" />
				</view>
			</view>
		</u-navbar>
		<view class="main">
			<u-search height="70" :show-action="false" :disabled="true" />
			<view class="list">
				<view class="list-item" v-for="(item, index) in list" :key="index" @click="chat2user(item.friend_id)">
					<view class="avatar">
						<image :src="item.friend_info.friend_avatar" class="image">
						<u-badge type="error" count="7" :offset="[-10, -10]" />
					</view>
					<view class="content">
						<view class="name">{{item.friend_info.remark || item.friend_info.friend_name}}</view>
						<view class="message">{{item.last_message.content}}</view>
					</view>
					<view class="time">{{item.last_message.time}}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script lang="ts">
import Vue from 'vue';
import { mapState } from 'vuex';
import Util from '../../helper/util';

declare let uni: any;

export default Vue.extend({
  name: 'ChatList',
	data() {
		return {};
	},
  computed: {
    ...mapState({
			allFriendsMap: (state: any) => state.user.allFriendsMap,
			messages:  (state: any) => state.message.messages,
		}),
		list() {
			const res = [];
			this.messages.forEach(item => {
				const { friend_id, list } = item;
				const last_message = list[list.length - 1];
				last_message.time = Util.formatTime(last_message.create_time);
				res.push({
					...item,
					friend_info: this.allFriendsMap[friend_id],
					last_message,
				});
			});
			return res;
		}
  },
	created() {
		this.$store.dispatch('message/getUnreadMessage');
	},
	methods: {
		showMenu() {
			const subNVue = uni.getSubNVueById('menu');
			subNVue.show('fade-in', 300, function(){});
			uni.$once('menu-hide', () => {
				subNVue.hide('fade-out', 300);
			});
		},
		chat2user(uid: number) {
			uni.navigateTo({
				url: `/pages/chat/chat?uid=${uid}`,
			});
		},
	}
});
</script>

<style lang="scss" scoped>
.line-1 {
	-webkit-box-orient: vertical;
	-webkit-line-clamp: 1;
	display: -webkit-box;
	overflow: hidden;
	white-space: normal;
	text-overflow: ellipsis;
}
.container {
	.navbar {
		display: flex;
		align-items: center;
		flex: 1;
		padding: 0 30rpx;
		font-size: 32rpx;
		& > view {
			flex: 1;
			&.app-name {
				font-weight: bold;
			}
			&.app-operate {
				text-align: right;
			}
		}
	}
	.main {
		padding: 30rpx;
		.list {
			.list-item {
				display: flex;
				align-items: center;
				margin-top: 30rpx;
				.avatar {
					position: relative;
					width: 84rpx;
					height: 84rpx;
					margin-right: 24rpx;
					.image {
						width: 100%;
						height: 100%;
						border-radius: 10%;
					}
				}
				.content {
					flex: 1;
					.name {
						font-size: 32rpx;
						color: #333;
					}
					.message {
						@extend .line-1;
						font-size: 26rpx;
						color: #999;
					}
				}
				.time {
					color: #999;
					margin-left: 26rpx;
				}
			}
		}
	}
}
</style>