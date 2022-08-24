<template>
    <div v-loading="isLoad">
        <el-row :gutter="20">
            <el-col :span="24" style="padding-top: 20px;">
                <h1 style="font-size: 30px">{{ ConvertToName(this.$route.params.id) }}</h1>
            </el-col>

        </el-row>
        <el-row style="padding-top: 20px">
            <el-col :xs="24" :lg="12" style="padding: 10px">
                <h2 :span="24" style="padding: 20px 0"><i class="el-icon-date"></i> Расписание</h2>
                <el-table :row-class-name="paintNowPair" :data="timetable" border style="width: 100%; margin: auto">
                    <el-table-column width="40" prop="pair" label="№">
                    </el-table-column>
                    <el-table-column label="Первая половина">
                        <template slot-scope="scope">
                            {{ scope.row.first_start }} - {{ scope.row.first_end }}
                        </template>
                    </el-table-column>
                    <el-table-column label="Вторая половина">
                        <template slot-scope="scope">
                            {{ scope.row.second_start }} - {{ scope.row.second_end }}
                        </template>
                    </el-table-column>
                </el-table>
            </el-col>
            <el-col :xs="24" :lg="12" style="padding: 10px">
                <h2 :span="24" style="padding: 20px 0"><i class="el-icon-warning-outline"></i> Замены</h2>
                <el-table :data="replaces" :row-class-name="paintNowReplace" border style="width: 100%; margin: auto">
                    <el-table-column width="110" prop="group" label="Группа">
                        <template slot-scope="scope">
                            <nuxt-link :to="'/group/' + scope.row.group">{{ scope.row.group }}</nuxt-link>
                        </template>
                    </el-table-column>
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
        <el-row style="padding-top: 20px">
            <h2 :span="24" style="padding: 20px 0"><i class="el-icon-user"></i> Учебные группы</h2>
            <el-col :span="24" style="display: flex; gap: 20px; flex-direction: column">
                <el-card v-for="cource_num in cources_count" shadow="hover" :header="cource_num + ' курс'"
                    v-if="groups.length > 0 && CheckCourceExist(cource_num)">
                    <div id="groups" style="display: flex; justify-content: left; gap: 20px; flex-wrap: wrap;">
                        <nuxt-link v-if="group.substr(0, 1) == cource_num" v-for="group in groups"
                            :to="'/group/' + group">
                            <el-button :type="hasGroupReplace(group) ? 'danger' : 'primary'" plain><i
                                    class="el-icon-user"></i> {{ group }}
                            </el-button>
                        </nuxt-link>
                    </div>
                </el-card>
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
    validate({ params }) {
        return /^\d+$/.test(params.id);
    },
    mounted() {
        this.getGroups(this.$route.params.id);
    },
    filters: {
        cut: function (stroke) {
            if (stroke.length > 30) {
                return stroke.slice(0, 30) + '...';
            } else {
                return stroke;
            }

        },
    },
    methods: {
        hasGroupReplace(group_name) {
            let result = false;
            this.replaces.forEach(replace => {
                if (replace.group == group_name) {
                    result = true;
                }
            });
            return result;
        },
        paintNowReplace({ row, rowIndex }) {
            //console.log(row);
            if (row.pair === 2) {
                return 'success-row';
            }
            return '';
        },
        paintNowPair({ row, rowIndex }) {
            if (rowIndex === 1) {
                return 'success-row';
            }
            return '';
        },
        /* printXml() {
             var xml = '<?xml version="1.0" encoding="UTF-8"?><Root><Classes>';
             var tritem = document.getElementById("result").getElementsByTagName("tr");
             for (i = 0; i < tritem.length; i++) {
                 var celldata = tritem[i];
                 if (celldata.cells.length > 0) {
                     xml += "<Class name='" + celldata.cells[0].textContent + "'>\n";
                     for (var m = 1; m < celldata.cells.length; ++m) {
                         xml += "\t<data>" + celldata.cells[m].textContent + "</data>\n";
                     }
                     xml += "</Class >\n";
                 }
             }
             xml += '</Classes></Root>';
             window.alert(xml);
             //here you can rewrite the xmlstring to a new document
             //or use the hide control to store the xml text, call the text in code behind.
             //also, you can call ajax to excuet codebehind and sava the xml file                
             // window.open('data:text/xml,' + xml);
         },
         write_to_excel() {
 
             str = "";
 
             var mytable = document.getElementsByTagName("table")[0];
             var rowCount = mytable.rows.length;
             var colCount = mytable.getElementsByTagName("tr")[0].getElementsByTagName("td").length;
 
             var ExcelApp = new ActiveXObject("Excel.Application");
             var ExcelSheet = new ActiveXObject("Excel.Sheet");
             ExcelSheet.Application.Visible = true;
 
             for (var i = 0; i < rowCount; i++) {
                 for (var j = 0; j < colCount; j++) {
                     str = mytable.getElementsByTagName("tr")[i].getElementsByTagName("td")[j].innerHTML;
                     ExcelSheet.ActiveSheet.Cells(i + 1, j + 1).Value = str;
                 }
             }
         }, */

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
        CheckCourceExist(cource_num) {
            let result = false;
            this.groups.forEach(group => {
                //console.log(group.substr(0, 1));
                if (group.substr(0, 1) == cource_num) {
                    console.log("OK");
                    result = true;
                    //break;
                }
            });
            return result;
        },
        getGroups(op_num) {
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
                if (response.grouplist[op_num]) {
                    appRef.groups = response.grouplist[op_num];
                } else {
                    appRef.$message({
                        type: 'error',
                        message: 'Расписание площадки под номером ' + op_num + ' не найдено'
                    });
                }
                console.log(response);
            }).catch(function (error) {
                appRef.isLoad = false;
                appRef.$message({
                    type: 'error',
                    message: 'Не удалось получить список групп'
                });
            });
        }
    },
    data() {
        return {
            replaces: [
                {
                    "group": "3-ОИБТС9-8",
                    "pair": 1,
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
                },
                {
                    "group": "1-ИСП11-21",
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
                },
                {
                    "group": "2-ИКСС11-5",
                    "pair": 2,
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
                }


            ],
            groups: [],
            isLoad: true,
            cources_count: 4,
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
            ],
            timetable: [
                {
                    pair: 1,
                    first_start: "12:50",
                    first_end: "12:50",
                    second_start: "12:50",
                    second_end: "12:50",
                },
                {
                    pair: 2,
                    first_start: "12:50",
                    first_end: "12:50",
                    second_start: "12:50",
                    second_end: "12:50",
                },
                {
                    pair: 3,
                    first_start: "12:50",
                    first_end: "12:50",
                    second_start: "12:50",
                    second_end: "12:50",
                },
                {
                    pair: 4,
                    first_start: "12:50",
                    first_end: "12:50",
                    second_start: "12:50",
                    second_end: "12:50",
                }
            ]
        }
    }
};
</script>