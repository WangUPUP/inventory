<template>
    <el-container class="home-container">
        <el-container>
            <!-- 侧边栏 -->
            <el-aside width="200px">
                <!-- 侧边栏菜单 -->
                <el-menu
                    class="el-menu-vertical-demo"
                    background-color="#333744"
                    text-color="white"
                    :router="true"
                    :default-active="this.$route.path"
                >
                    <!-- 一级菜单模板区域 -->
                    <el-submenu
                        index="1"
                        v-for="item in $router.options.routes"
                        :key="item.index"
                        default-active="this.$route.path"
                        text-color="white"
                    >
                        <template slot="title">
                            <!-- <i class="el-icon-location"></i> -->
                            <!-- 文本 -->
                            <span>{{ item.title }}</span>
                        </template>
                        <!-- 二级菜单 -->
                        <el-menu-item :index="'' + subItem.path" v-for="subItem in item.children" :key="subItem.index">
                            <template slot="title">
                                <!-- <i class="el-icon-user"></i> -->
                                <!-- 文本 -->
                                <span>{{ subItem.title }}</span>
                                <!-- <router-link :to="subItem.path">
                </router-link>-->
                            </template>
                        </el-menu-item>
                    </el-submenu>
                </el-menu>
            </el-aside>
            <el-container>
                <!-- 右侧主体 -->
                <el-main>
                    <router-view ></router-view>
                </el-main>
            </el-container>
        </el-container>
    </el-container>
</template>

<script>
export default {
    name: 'home',
    data() {
        return {

        }
    },
    methods: {

    }
}
</script>
<style>
html,
body,
#app,
.el-container,
.el-card {
    padding: 0px;
    margin: 0px;
    height: 100%;
}
.el-header {
    background-color: #373d41;
}
.el-aside {
    background-color: #333744;
}

.el-main {
    background-color: #eaedf1;
}

a {
    text-decoration: none;
    /* color: white; */
}

/* a:hover {
  color: red;
} */

.el-menu {
    border-right-width: 0;
}
</style>
