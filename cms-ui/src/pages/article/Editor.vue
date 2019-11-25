<template>
    <div>
        <el-button type="primary" size="small" icon="el-icon-arrow-left" @click="back" plain>返回</el-button>
        <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="所属栏目">
            <el-select v-model="form.categoryId" placeholder="请选择活动区域">
                <el-option v-for="c in categories" :key="c.id" :label="c.name" :value="c.id"></el-option>
            </el-select>
        </el-form-item>
        <el-form-item label="标题">
            <el-input v-model="form.title"></el-input>
        </el-form-item>
        <el-form-item label="正文">
            <el-input type="textarea" v-model="form.content"></el-input>
        </el-form-item>
        <el-form-item>
            <el-button type="primary" @click="onSubmit">发布</el-button>
        </el-form-item>
        </el-form>
    </div>
</template>

<script>
import request from '@/utils/request'
import qs from 'querystring'
export default {
    //模板中需要用到的数据
    data(){
        return{
            form:{},
            categories:[]
        }
    },
    //生命周期函数
    created(){
        this.form = this.$route.query;
        this.reloadData()
    },
    //模板中用到的方法
    methods:{
        back(){
            this.$router.go(-1);
        },
        reloadData(){
            let url = "/category/findAll"
            request.get(url)
            .then(response => {
                this.categories = response.data;
            })
        },
        onSubmit(){
            request.request({
                url:"/article/saveOrUpdate",
                method:"post",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:qs.stringify(this.form)
            })
            .then(response => {
                this.$message({
                    message:response.message,
                    type:'success'
                });
                this.back();
            })
        }
    }
}
</script>

<style scoped>

</style>

