<template>
    <div>
        <el-row :gutter="20">
            <el-col :span="24" style="padding-left: 10px; padding-right: 10px; padding-top: 20px;">
                <h1 style="font-size: 30px">Группа {{ this.$route.params.name }}</h1>
            </el-col>
        </el-row>
        <el-row>
            <el-col :span="24">
                <h2 style="padding: 20px 0px;"><i class="el-icon-warning-outline"></i> Замены</h2>
                <el-table width="180" :data="replaces" border style="margin: auto">
                    <el-table-column width="40" prop="pair" label="№">
                    </el-table-column>
                    <el-table-column prop="orig" label="Урок">
                        <template slot-scope="scope">
                            <div v-if="scope.row != null && scope.row.orig.disc" style="font-size: 12px;">
                                <div>{{ scope.row.orig.disc | cut }} ({{ scope.row.orig.teacher }})</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div>{{ scope.row.orig.aud }}</div>
                                </div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column prop="replace" label="Замена">
                        <template slot-scope="scope">
                            <div v-if="scope.row != null && scope.row.replace.disc" style="font-size: 12px;">
                                <div>{{ scope.row.replace.disc | cut }} ({{ scope.row.replace.teacher }})</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div>{{ scope.row.replace.aud }}</div>
                                </div>
                            </div>
                        </template>
                    </el-table-column>
                </el-table>
            </el-col>
        </el-row>
        <el-row>
            <el-col :span="24" style="padding: 20px 0"><i class="el-icon-date"></i> Числитель <template
                    v-if="week == 'odd'">(текущий)</template> </el-col>
            <el-col :span="24">
                <el-table :row-class-name="paintNowPair" :cell-class-name="paintNowDay" v-loading="isLoad"
                    :data="timetable | convertToOdd" border style="width: 100%">
                    <el-table-column prop="pair" width="40px" label="№">

                    </el-table-column>
                    <el-table-column prop="Monday" label="Пн">

                        <template slot-scope="scope">
                            <div v-if="scope.row.Monday != null && scope.row.Monday.disc" style="font-size: 15px;">
                                <div>{{ scope.row.Monday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Monday.teacher }}</div>
                                    <div>{{ scope.row.Monday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column prop="Tuesday" label="Вт">
                        <template slot-scope="scope">
                            <div v-if="scope.row.Tuesday != null && scope.row.Tuesday.disc" style="font-size: 15px;">
                                <div>{{ scope.row.Tuesday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Tuesday.teacher }}</div>
                                    <div>{{ scope.row.Tuesday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column prop="Wednesday" label="Ср">
                        <template slot-scope="scope">
                            <div v-if="scope.row.Wednesday != null && scope.row.Wednesday.disc"
                                style="font-size: 15px;">
                                <div>{{ scope.row.Wednesday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Wednesday.teacher }}</div>
                                    <div>{{ scope.row.Wednesday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column prop="Thursday" label="Чт">
                        <template slot-scope="scope">
                            <div v-if="scope.row.Thursday != null && scope.row.Thursday.disc" style="font-size: 15px;">
                                <div>{{ scope.row.Thursday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Thursday.teacher }}</div>
                                    <div>{{ scope.row.Thursday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column prop="Friday" label="Пт">
                        <template slot-scope="scope">
                            <div v-if="scope.row.Friday != null && scope.row.Friday.disc" style="font-size: 15px;">
                                <div>{{ scope.row.Friday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Friday.teacher }}</div>
                                    <div>{{ scope.row.Friday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                </el-table>
            </el-col>
        </el-row>
        <el-row>
            <el-col :span="24" style="padding: 20px 0"><i class="el-icon-date"></i> Знаменатель <template
                    v-if="week == 'even'">(текущий)</template> </el-col>
            <el-col :span="24">
                <el-table :row-class-name="paintNowPair" :cell-class-name="paintNowDay" v-loading="isLoad"
                    :data="timetable | convertToEven" border style="width: 100%">
                    <el-table-column prop="pair" width="40px" label="№">

                    </el-table-column>
                    <el-table-column prop="Monday" label="Пн">

                        <template slot-scope="scope">
                            <div v-if="scope.row.Monday != null && scope.row.Monday.disc" style="font-size: 15px;">
                                <div>{{ scope.row.Monday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Monday.teacher }}</div>
                                    <div>{{ scope.row.Monday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column prop="Tuesday" label="Вт">
                        <template slot-scope="scope">
                            <div v-if="scope.row.Tuesday != null && scope.row.Tuesday.disc" style="font-size: 15px;">
                                <div>{{ scope.row.Tuesday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Tuesday.teacher }}</div>
                                    <div>{{ scope.row.Tuesday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column prop="Wednesday" label="Ср">
                        <template slot-scope="scope">
                            <div v-if="scope.row.Wednesday != null && scope.row.Wednesday.disc"
                                style="font-size: 15px;">
                                <div>{{ scope.row.Wednesday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Wednesday.teacher }}</div>
                                    <div>{{ scope.row.Wednesday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column prop="Thursday" label="Чт">
                        <template slot-scope="scope">
                            <div v-if="scope.row.Thursday != null && scope.row.Thursday.disc" style="font-size: 15px;">
                                <div>{{ scope.row.Thursday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Thursday.teacher }}</div>
                                    <div>{{ scope.row.Thursday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                    <el-table-column prop="Friday" label="Пт">
                        <template slot-scope="scope">
                            <div v-if="scope.row.Friday != null && scope.row.Friday.disc" style="font-size: 15px;">
                                <div>{{ scope.row.Friday.disc | cut }}</div>
                                <div style="display: flex; justify-content: space-between">
                                    <div> {{ scope.row.Friday.teacher }}</div>
                                    <div>{{ scope.row.Friday.aud }}</div>
                                </div>
                            </div>
                            <div v-else style="display: flex; justify-content: center">
                                <div>-</div>
                            </div>
                        </template>
                    </el-table-column>
                </el-table>
            </el-col>
        </el-row>
    </div>
</template>

<style scoped>
#groups button {
    margin: 0 !important;
}
</style>

<script>
export default {
    /* validate({ params }) {
        return /^\d+$/.test(params.name);
    }, */
    mounted() {
        this.getTimetable(this.$route.params.name);
    },
    methods: {
        paintNowPair({ row, rowIndex }) {
            if (rowIndex === 1) {
                return 'gray-row';
            }
            return '';
        },
        paintNowDay({ row, column, rowIndex, columnIndex }) {
            if (columnIndex === 2 && rowIndex === 1) {
                return 'success-row';
            }
            return '';
        },

        /* tableRowClassName({ row, rowIndex }) {
            if (rowIndex === 1) {
                return 'warning-row';
            } else if (rowIndex === 3) {
                return 'success-row';
            }
            return '';
        }, */
        ConvertDayData(data, week) {
            //console.log("test4321");
            //return {};
            //console.log(data);
            result = [];
            for (let pairnum = 0; pairnum < 8; pairnum++) {
                if (data[pairnum] == null) {
                    result[pairnum] = [];
                } else {
                    if (result[pairnum][week] != null) {
                        result[pairnum] = result[pairnum][week];
                    } else if (result[pairnum]['all'] != null) {
                        result[pairnum] = result[pairnum]['all'];
                    } else {
                        result[pairnum] = [];
                    }
                }
            }
            return result;
        },
        getTimetable(group_name) {
            let appRef = this;
            this.groups = "";
            this.$axios.$request({
                method: 'GET',
                url: 'https://www.ks54.ru/lk/include/api.php?action=timetable&group=' + group_name,
            }).then(function (response) {
                appRef.isLoad = false;
                appRef.week = response.week;
                appRef.timetable = response;
            }).catch(function (error) {
                appRef.isLoad = false;
                appRef.$message({
                    type: 'error',
                    message: 'Не удалось получить расписание группы ' + group_name
                });
                appRef.$router.push('/');
            });
        }
    },
    filters: {
        cut: function (stroke) {
            if (stroke.length > 30) {
                return stroke.slice(0, 30) + '...';
            } else {
                return stroke;
            }

        },
        convertToOdd: function (data) {
            let week = 'odd';
            console.log("hello world!");
            let result = {};
            for (var key of Object.keys(data)) {
                if (key != 'code' && key != 'week') {
                    result[key] = {};
                    for (let pairnum = 1; pairnum <= 8; pairnum++) {
                        if (data[key].pair[pairnum] == null) {
                            result[key][pairnum] = {};
                        } else {
                            if (data[key].pair[pairnum][week] != null) {
                                result[key][pairnum] = data[key].pair[pairnum][week];
                            } else if (data[key].pair[pairnum]['all'] != null) {
                                //console.log(result[key]);
                                result[key][pairnum] = data[key].pair[pairnum]['all'];
                            } else {
                                result[key][pairnum] = {};
                            }
                        }
                    }
                }
            }

            let result_to_table = [];
            //console.log(result);

            for (let pairnum = 1; pairnum <= 8; pairnum++) {
                let pairinfo = {};
                pairinfo['pair'] = pairnum;
                for (var key of Object.keys(result)) {
                    pairinfo[key] = result[key][pairnum];
                }
                result_to_table.push(pairinfo);
            }
            console.log(result_to_table);
            return result_to_table;
        },
        convertToEven: function (data) {
            let week = 'even';
            console.log("hello world!");
            let result = {};
            for (var key of Object.keys(data)) {
                if (key != 'code' && key != 'week') {
                    result[key] = {};
                    for (let pairnum = 1; pairnum <= 8; pairnum++) {
                        if (data[key].pair[pairnum] == null) {
                            result[key][pairnum] = {};
                        } else {
                            if (data[key].pair[pairnum][week] != null) {
                                result[key][pairnum] = data[key].pair[pairnum][week];
                            } else if (data[key].pair[pairnum]['all'] != null) {
                                //console.log(result[key]);
                                result[key][pairnum] = data[key].pair[pairnum]['all'];
                            } else {
                                result[key][pairnum] = {};
                            }
                        }
                    }
                }
            }

            let result_to_table = [];
            //console.log(result);

            for (let pairnum = 1; pairnum <= 8; pairnum++) {
                let pairinfo = {};
                pairinfo['pair'] = pairnum;
                for (var key of Object.keys(result)) {
                    pairinfo[key] = result[key][pairnum];
                }
                result_to_table.push(pairinfo);
            }
            console.log(result_to_table);
            return result_to_table;
        },
    },
    data() {
        return {
            week: "",
            isLoad: true,
            timetable: {},
            replaces: [{
                "group": "3-ОИБТС9-8",
                "pair": 3,
                "orig": {
                    "disc": "МДК 03.01",
                    "teacher": "Сидоров А.Б",
                    "aud": ""
                },
                "replace": {
                    "disc": "МДК 03.01",
                    "teacher": "Сидоров А.Б",
                    "aud": ""
                }
            }]
        }
    }
};
</script>


