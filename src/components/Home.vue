<template>
	<el-container class="home-container">
		<!-- 头部区域 -->
		<el-header>
			<div>
				<img src="../assets/heima.png" alt="">
				<span>电商后台管理系统</span>
			</div>
			<el-button type="info" @click="logout">退出</el-button>
		</el-header>
		<!-- 页面主体区域 -->
		<el-container>
			<!-- 侧边栏区域 -->
			<el-aside :width="isCollapse ? '64px' : '200px'">
				<div class="toggle-button" @click="toggleCollapse">|||</div>
				<!-- 菜单区域 -->
				<el-menu background-color="#333744" text-color="#fff"
				         active-text-color="#409EFF" unique-opened
				         :collapse="isCollapse" :collapse-transition="false" router :default-active="activePath">
					<!-- 一级菜单 -->
					<el-submenu :index="item.id + ''" v-for="item in menulist" :key="item.id">
						<!-- 一级菜单的模板区域 -->
						<template slot="title">
							 <!-- 图标 -->
							<i :class="iconsObj[item.id]"></i>
							<!-- 文本 -->
							<span>{{item.authName}}</span>
						</template>

						<!-- 二级菜单 -->
						<!-- 提供唯一的key值 -->
						<el-menu-item :index="'/' + subItem.path" v-for="subItem in item.children"
						              :key="subItem.id" @click="saveNavState('/' + subItem.path)">
							<template slot="title">
								<!-- 图标 -->
								<i class="el-icon-menu"></i>
								<!-- 文本 -->
								<span>{{subItem.authName}}</span>
							</template>
						</el-menu-item>
					</el-submenu>
				</el-menu>
			</el-aside>
			<!-- 右侧内容主体 -->
			<el-main>
				<router-view/>
			</el-main>
		</el-container>
	</el-container>
</template>

<script>
export default {
	name: "Home",
	data() {
		return {
			// 左侧菜单数据
			menulist: [],
			iconsObj: {
				'125': 'iconfont icon-user',
				'103': 'iconfont icon-tijikongjian',
				'101': 'iconfont icon-shangpin',
				'102': 'iconfont icon-danju',
				'145': 'iconfont icon-baobiao'
			},
			// 是否折叠
			isCollapse: false,
			// 被激活的链接地址
			activePath: ''
		}
	},
	created() {
		this.getMenuList()
		this.activePath = window.sessionStorage.getItem('activePath')
	},
	methods: {
		logout() {
			window.sessionStorage.clear()
			this.$router.push('/login')
		},
		// 获取所有的菜单
		async getMenuList() {
			// 通过menus接口获取数据
			const {data: res} = await this.$http.get('menus')
			if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
			this.menulist = res.data
			console.log(res);
		},
		// 点击按钮，切换菜单的折叠与展开
		toggleCollapse() {
			this.isCollapse = !this.isCollapse;
		},
		// 保存链接的激活状态
		saveNavState(activePath) {
			window.sessionStorage.setItem('activePath', activePath);
			// 这里需要重新赋值！！！
			this.activePath = activePath
		}
	}
}
</script>

<style lang="less" scoped>
.home-container {
	height: 100%;
}

.el-header {
	background-color: #373D41;
	display: flex;
	justify-content: space-between;
	padding-left: 0;
	align-items: center;
	color: #fff;
	font-size: 20px;

	> div {
		display: flex;
		align-items: center;

		span {
			margin-left: 15px;
		}
	}
}

.el-aside {
	background-color: #333744;
	.el-menu {
		// 代表没有边框线
		border-right: none;
	}
}

.el-main {
	background-color: #eaedf1;
}

.iconfont {
	margin-right: 10px;
}

.toggle-button {
	background-color: #4A5064;
	font-size: 10px;
	line-height: 24px;
	color: #fff;
	// 文字居中对齐
	text-align: center;
	// 竖线之间太近
	//letter-spacing: 2em;
	// 鼠标放上去，有小手
	cursor: pointer;
}
</style>