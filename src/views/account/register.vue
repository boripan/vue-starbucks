<template>
	<div>
		<article class="wrapper">
			<nav class="nav">
				<nav-container v-on:showMenuOverlay="showMenuExec" v-show="!show_menu_bol">
					<span slot="navbody">
						<section class="navbody-menu body">
							<div class="accountbtn menubtn account">
								<h2>登录或创建一个新帐户 🌟 </h2>
								<ul>
									<li @click="pageRedir(2, 'login')">
										<a href="javascript:;" :class="{'active-menu':curActive == 'login'}">
											登录
										</a>
									</li>
									<li @click="pageRedir(4, 'register')">
										<a href="javascript:;" :class="{'active-menu':curActive == 'register'}">
											注册
										</a>
									</li>
									<li @click="pageRedir(5, 'starbucks-rewards')">
										<a href="javascript:;" :class="{'active-menu':curActive == 'starbucks-rewards'}">
											关于星享俱乐部
										</a>
									</li>
								</ul>
							</div>
						</section>
					</span>
				</nav-container>
				<nav-overlay v-on:closeMenuOverlay="closeMenuExec" v-show="show_menu_bol">
				</nav-overlay>
				<nav-mobile v-on:showMenuOverlay="showMenuExec"></nav-mobile>
			</nav>
			<article class="content" v-if="lgMedia || !show_menu_bol">
				<section class="account-welcome">
					<a href="javascript:;">
						<img src="/static/images/account-join/header_cn.jpg" alt="">
					</a>
				</section>
				<section class="register-actions">
					<section class="register-tab" v-if="!mbMedia">
						<div class="register-tab-group clearfix">
							<div class="register-tab-app" :class="{'register-tab-active': curActTab === 'app'}" @click="toggleActTab('app')">
								<span>使用星巴克App注册</span>
							</div>
							<div class="register-tab-card" :class="{'register-tab-active': curActTab === 'card'}" @click="toggleActTab('card')">
								<span>卡号密码注册</span>
							</div>
						</div>
					</section>
					<component v-bind:is="view"></component>
				</section>
			</article>
		</article>
	</div>
</template>

<script>

	import '@/assets/css/bootstrap.min.css'
	import '@/assets/css/swiper.min.css'
	import '@/assets/css/init.css'
	import '@/assets/css/styles.css'
	import '@/assets/css/account.css'
	import NavContainer from '@/components/navContainer'
	import NavOverlay from '@/components/NavOverlay'
	import NavMobile from '@/components/navMobile'
	import AppRegister from '@/components/appRegister'
	import CardRegister from '@/components/cardRegister'
	import PageviewTools from '@/utils/pageviewTools'

	export default {
		name: 'account',
		metaInfo: {
			title: '星享俱乐部',
			titleTemplate: '%s | 星巴克',
			meta: [
				{
					name: 'keywords',
					content: ''
				},
				{
					name: 'description',
					content: '星享俱乐部'
				}
			]
		},
		data(){
			return {
				show_menu_bol: false,
				lgMedia: window.matchMedia('(min-width: 1025px)').matches,
				mbMedia: window.matchMedia('(max-width: 640px)').matches,
				curActive: 'register',
				username: '',
				password: '',
				curActTab: 'app',
				view: 'AppRegister'
			}
		},
		mounted(){
			const _self = this;
			window.matchMedia('(min-width: 1025px)').addListener(()=>{
				_self.lgMedia = window.matchMedia('(min-width: 1025px)').matches;
			});
			window.matchMedia('(max-width: 640px)').addListener(()=>{
				_self.mbMedia = window.matchMedia('(max-width: 640px)').matches;
			});
			this.trackingVisitor();
		},
		components: {
			NavContainer: NavContainer,
			NavOverlay: NavOverlay,
			NavMobile: NavMobile,
			AppRegister: AppRegister,
			CardRegister: CardRegister
		},
		methods: {
			showMenuExec(){
				this.show_menu_bol = true;
			},
			closeMenuExec(){
				this.show_menu_bol = false;
			},
			pageRedir(item, curName){
				if(curName){
					this.curActive = curName;
				}
				this.$store.commit('pageRedir', item);
			},
			toggleActTab(tab){
				this.curActTab = tab;
				this.view = (tab === 'app' ? 'AppRegister' : 'CardRegister');
			},
			trackingVisitor() {
				let storage = window.sessionStorage || null;
				if(storage) {
					let VisitorID = storage.getItem('VisitorID'),
						page = '我的账户-注册',
						time = PageviewTools.GetTime();
					if(!VisitorID) return;
					axios.post('users/tracking',{
						visitorID: VisitorID,
						page: page,
						time: time
					})
				}
				
			}
		}
	}

</script>
