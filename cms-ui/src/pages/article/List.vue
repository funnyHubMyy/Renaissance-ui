<template>
    <div>
        <el-button type="primary" size="small" @click="toPublishArticle">发布文章</el-button>
        <el-table :data="tableData" style="width:100%">
            <el-table-column prop="id" label="编号" width="180"></el-table-column>
            <el-table-column prop="title" label="标题" width="230"></el-table-column>
            <el-table-column prop="author.username" label="作者" width="180"></el-table-column>
            <el-table-column prop="category.name" label="所属栏目" width="180"></el-table-column>
            <el-table-column
                fixed="right"
                label="操作"
                align="center"
                width="150">
                <template slot-scope="scope">
                    <el-button type="text" size="small">查看</el-button>
                    <el-button @click="toEditArticle(scope.row)" type="text" size="small">编辑</el-button>
                    <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
    </div>
</template>

<script>
import request from '@/utils/request'
export default {
    //模板中要用到的变量
    data(){
        return{
            tableData:[]
        }
    },
    //生命周期钩子函数
    created(){
        this.reloadData();
    },
    //方法，模板中要用到的方法
    methods:{
        toPublishArticle(){
            this.$router.push({path:'/article/editor'})
        },
        toEditArticle(row){
            this.$router.push({
                path:'/article/editor',
                query: row
            })
        },
        toDelete(id){
            this.$confirm('是否要永久删除此文章？','提示',{
                confirmButtonText:'确定',
                cancelButtonText:'取消',
                type:'warning'
            })
            .then(() => {
                let url = "/article/deleteById"
                request.get(url,{params:{id}})
                .then(response => {
                    this.$message({
                        type:'success',
                        message:response.message
                    });
                    this.reloadData();
                })
            })
        },
        reloadData(){
            request.get("/article/cascadeFindAll")
            .then(response => {
                this.tableData = response.data;
        })
        }
    }
}
</script>

<style scoped>

</style>
