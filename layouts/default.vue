<template>

    <el-card class="box-card" style="max-width: 1800px; margin: auto; margin-top: 20px;  margin-bottom: 20px">
        <!-- <div slot="header" class="clearfix">
                    <span>Личный кабинет</span>
                    <el-button type="text" style="float: right; padding: 3px 0">Выйти</el-button>
                </div> -->
        <div class="text item">
            <el-row :gutter="20">
                <el-col :xs="24" :sm="7" :md="6" :lg="5" :xl="4">
                    <el-menu>
                        <el-menu-item-group title="Образовательные площадки">
                            <nuxt-link v-for="platform in platforms" :to="'/platform/' + platform.num">
                                <el-menu-item :index="platform.num">
                                    <i class="el-icon-office-building"></i>
                                    <span>{{ platform.title }}</span>
                                </el-menu-item>
                            </nuxt-link>
                        </el-menu-item-group>
                    </el-menu>
                    <!-- <MenuTabs :tabs="tabs"></MenuTabs> -->
                </el-col>
                <el-col :xs="24" :sm="17" :md="18" :lg="19" :xl="20">
                    <GroupSearch style="width: 100%"></GroupSearch>
                    <Nuxt />
                </el-col>
            </el-row>

        </div>
    </el-card>
</template>
<style>
body {
    background-color: #ebebeb;
}
</style>
<script>
import GroupSearch from "../components/GroupSearch.vue";
export default {
    data() {
        return {
            platforms: [
                {
                    title: "Таганская - 1",
                    num: 1
                },
                {
                    title: "Коломенская - 2",
                    num: 2
                },
                {
                    title: "Семёновская - 5",
                    num: 4
                },
                {
                    title: "Рязанская - 6",
                    num: 6
                },
                {
                    title: "Римская - 7",
                    num: 7
                },
                {
                    title: "Авиамоторная - 8",
                    num: 9
                }
            ]
        };
    },
    components: { GroupSearch }
}
</script>
