<template>
    <a class="back-button" href="index.html">
        <ArrowLeft style="width: 30px; height: 30px; margin-left: 15px;margin-top: 15px"/>
    </a>
    <section>
        <div class="code-table">
            <ul class="operation-box">
                <li v-if="operation===0" class="chosen" @click="changeOperation(0)">添加</li>
                <li v-else @click="changeOperation(0)">添加</li>
                <li v-if="operation===1" class="chosen" @click="changeOperation(1)">删除</li>
                <li v-else @click="changeOperation(1)">删除</li>
                <li v-if="operation===2" class="chosen" @click="changeOperation(2)">修改</li>
                <li v-else @click="changeOperation(2)">修改</li>
                <li v-if="operation===3" class="chosen" @click="changeOperation(3)">查找</li>
                <li v-else @click="changeOperation(3)">查找</li>
            </ul>
            <section class="operation-panel">
                <AddDepartment v-if="operation===0"
                               :name="props.name"
                               :id="origin_department_id"
                               :department_map="department_map"
                               @addDepartment="addDepartment"></AddDepartment>
            </section>
        </div>
        <div class="tree-table">
            <el-empty v-if="ifEmpty" description="暂无数据" :image-size="300" style="height: 750px"/>
            <Relationship v-else  :head="props.origin_department_id" :department_map="department_map"></Relationship>
        </div>
    </section>
</template>

<script setup>
/* 引入必要的组件和文件 */
import {onMounted, reactive, ref} from 'vue'
import {ArrowLeft} from '@element-plus/icons-vue'
import AddDepartment from "@/components/AddDepartment.vue";
import Relationship from "@/components/Relationship.vue";
import {ElMessage} from "element-plus";

const props = defineProps(['name', 'origin_department_id'])

/* 定义内部用的变量 */
const ifEmpty = ref(true)
const operation = ref(0)             // 当前对数据进行得是何种操作
const department_map = reactive(new Map([[props.origin_department_id, {// 存储组织机构
    department_id: props.origin_department_id,      // 机构id
    top_department: "",      // 所属机构
    sub_department: [],      // 下属机构
    department_name: props.name,     // 机构名称
    leader: -1,             // 主管id（1名）
    vice_leader: [],        // 副主管id（多名）
    members: []              // 办事人员
}
]]))        // 建立id和机构的映射


const changeOperation = (index) => {
    operation.value = index
}

const addDepartment = (department_name, department_id, top_department_name, top_department_id) => {
    let top_department = department_map.get(top_department_id)
    for(let i = 0;i<top_department.sub_department.length;i++){
        if(department_map.get(top_department.sub_department[i]).department_name===department_name){
            ElMessage.error(department_name + "已经存在！（部门编号："+ department_id +"）")
            return
        }
    }
    top_department.sub_department.push(department_id)
    department_map.set(department_id, {
        department_id: department_id,
        top_department: top_department_id,
        sub_department: [],
        department_name: department_name,
        leader: -1,
        vice_leader: [],
        members: []
    })
    ifEmpty.value=false
}

</script>

<style scoped>
@keyframes fadeIn {
    0% {
        opacity: 0;
    }
    100% {
        opacity: 1;
    }
}

section {
    display: flex;
    justify-content: center;
    width: 90%;
    height: 80%;
    position: absolute;
    top: 13%;
    left: 5%;
    animation: fadeIn 1s forwards;
    animation-fill-mode: both;
    animation-duration: 1s;
}

.back-button {
    width: 60px;
    height: 60px;
    border-radius: 1000px;
    background: rgba(255, 255, 255, 0.6);
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
    margin-top: 5%;
    position: absolute;
    bottom: 1.5%;
    right: 1.5%;
    animation: fadeIn 1s forwards;
    animation-fill-mode: both;
    animation-duration: 1s;
    transition: box-shadow 0.5s ease;
}

.back-button:hover {
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.4);
}

.code-table {
    width: 320px;
    height: 750px;
    border-radius: 10px;
    background: rgba(255, 255, 255, 1);
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
}

.operation-box {
    width: 280px;
    height: 40px;
    display: flex;
    justify-content: space-between;
    margin-left: 20px;
    margin-right: 20px;
    font-family: Msyh;
    letter-spacing: 2px;
}

.operation-box li {
    padding-top: 15px;
    display: inline-block;
    width: 40px;
    border-bottom: 2px solid #B2D7D2;
    text-align: center;
    color: #888888;
    font-size: 14px;
    transition: color 0.5s ease;
    cursor: default;
}

.operation-box li:hover {
    color: #B2D7D2;
}

.operation-box .chosen {
    color: #B2D7D2;
}

.tree-table {
    width: 1120px;
    height: 750px;
    border-radius: 10px;
    background: rgba(255, 255, 255, 0.6);
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
    margin-left: 30px;
    position: relative;
}

.operation-panel {
    margin-left: -25px;
    margin-top: -40px;
    width: 280px;
    height: 680px;
//border: solid 1px black;
}
</style>