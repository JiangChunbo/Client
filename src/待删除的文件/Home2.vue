<template>
    <el-container style="height: 100%; border: 1px solid #eee">
        <el-aside width="150px" style="background-color: #f7f6fb; overflow: hidden">
            <div class="block" style="margin: 20px 0;">

                <div style="margin-top: 10px;" v-if="loggedIn === false">
                    <el-button type="primary" size="mini" round @click="loginFormVisible = true">登录</el-button>
                </div>

                <div v-else>
                    <span>欢迎, {{form.username}}</span>
                    <div style="margin: 10px 0">
                        <el-button type="primary" size="mini" round @click="logoutHandler">注销</el-button>
                    </div>
                </div>

                <el-dialog title="登录" :visible.sync="loginFormVisible" width="30%">
                    <el-form ref="form" status-icon :model="form" label-width="80px">
                        <el-form-item label="用户名" style="width: 100%;">
                            <el-input v-model="form.username"></el-input>
                        </el-form-item>
                        <el-form-item label="密码" style="width: 100%;">
                            <el-input :type="passwordType" v-model="form.password">
                                <i slot="suffix" class="el-icon-view" @mouseenter="passwordType = 'text'"
                                   @mouseleave="passwordType = 'password'"
                                   :style="{color: passwordType === 'password' ? '#c0c4cc' : '#409EFF', marginRight: '5px'}"></i>
                            </el-input>
                        </el-form-item>
                    </el-form>
                    <div slot="footer" class="dialog-footer">
                        <el-button @click="loginFormVisible = false">取 消</el-button>
                        <el-button type="primary" @click="login">登录</el-button>
                    </div>
                </el-dialog>
            </div>
            <!-- 解析表达式 -->

            <el-menu v-if="loggedIn"
                     :default-active="this.$route.path"
                     text-color="#000"
                     unique-opened
                     router>
                <el-menu-item index="/user" style="text-align: left;" v-if="menus.indexOf('user') !== -1">
                    <template slot="title">
                        <i class="el-icon-user"></i>
                        <span slot="title">用户</span>
                    </template>
                </el-menu-item>


                <el-submenu index="general" style="text-align: left;" v-if="menus.indexOf('data') !== -1">
                    <template slot="title">
                        <i class="el-icon-coin"></i>
                        <span slot="title">数据</span>
                    </template>
                    <el-menu-item index="/data/style01">样式01</el-menu-item>
                    <el-menu-item index="/data/style02">样式02</el-menu-item>
                    <el-menu-item index="/data/style03">样式03</el-menu-item>
                    <el-menu-item index="/data/style04">样式04</el-menu-item>
                    <el-menu-item index="/data/style05">样式05</el-menu-item>
                </el-submenu>

            </el-menu>
        </el-aside>

        <el-container>
            <el-main>
                <router-view></router-view>
            </el-main>
        </el-container>
    </el-container>
</template>

<script>
    // import Import from "@/components/Import";
    // import Analysis from "@/components/Analysis";
    // import Table from "@/components/Table";
    // import TabImage from "@/components/Tab-Image";

    import ajax from "@/util/ajax";

    export default {
        components: {
            // Table, Analysis, Import, TabImage
        },
        data: function () {
            return {
                dataBaseName: 'general',
                tableName: 'weather',
                activeTabName: 'analysis',
                loginFormVisible: false,
                form: {
                    username: '匿名',
                    password: ''
                },
                passwordType: 'password',
                loggedIn: true,
                menus: ['user', 'data']
            };
        },
        computed: {},
        methods: {
            login() {
                ajax.post('/login', {
                    username: this.form.username,
                    password: this.form.password
                }).then((message) => {
                    this.$message.success("登录成功");
                    this.loggedIn = true;
                    this.loginFormVisible = false;

                    this.menus = JSON.parse(message.request.response);
                }).catch((error) => {
                    this.$message.error(error.request.response);
                }).finally();
            },

            logoutHandler() {
                ajax.post('/logout', {}).then(() => {
                    this.$message.success("注销成功");
                    this.loggedIn = false;
                }).catch((error) => {
                    this.$message.error(error.request.response);
                }).finally();
            }
        }

    }
</script>

<style scoped>

</style>