<template>

    <el-select :loading="isLoad" :disabled="isLoad" @change="routeToGroup" filterable clearable
        placeholder="Введите название группы">
        <i slot="prefix" class="el-input__icon el-icon-search"></i>
        <el-option-group v-for="(grouplist, index) in groups" :label="ConvertToName(index)">
            <el-option v-for="group in grouplist" :key="group" :label="group" :value="group">
            </el-option>
        </el-option-group>
    </el-select>
</template>
<style>
body {
    background-color: #ebebeb;
}
</style>
<script>
export default {
    methods: {
        routeToGroup(value) {
            this.$router.push('/group/' + value);
        },
        ConvertToName(number) {
            let result = number;
            this.platforms.forEach(op => {
                console.log('Число: ' + op.num);
                console.log('Значение: ' + number);
                if (Number(number) == op.num) {
                    result = op.title;
                }
            });
            return result;
        },
        getGroups() {
            let appRef = this;
            this.groups = "";
            this.$axios.$request({
                method: 'GET',
                url: 'https://www.ks54.ru/lk/include/api.php?action=grouplist',
                /*headers: {
                    'Authorization': 'Bearer ' + sessionStorage.getItem('token'),
                    'content-type': 'application/json'
                },
                data: this.form */
            }).then(function (response) {
                appRef.isLoad = false;
                appRef.groups = response.grouplist
                //console.log(response);
            }).catch(function (error) {
                appRef.isLoad = false;
                appRef.$message({
                    type: 'error',
                    message: 'Не удалось получить список групп'
                });
            });
        }
    },
    mounted() {
        this.getGroups();
    },

    data() {
        return {
            isLoad: true,
            groups: {},
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
        }
    }
}
</script>
