<template>
  <div>
    <!-- 按钮 -->
    <el-button type="primary" size="small" @click="toAdd">新增</el-button>
    <el-button type="danger" size="small" @click="batchDelete">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    {{ids}}
    <el-table :data="categories" @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="name" label="名称"></el-table-column>
      <el-table-column prop="description" label="描述"></el-table-column>
      <el-table-column prop="no" label="序号"></el-table-column>
      <el-table-column prop="parentId" label="父栏目"></el-table-column>
      <el-table-column
        fixed="right"
        label="操作"
        align="center"
        width="100">
        <template slot-scope="scope">
          <el-button @click="toEdit(scope.row)" type="text" size="small">编辑</el-button>
          <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格 -->
    <!-- 模态框 -->
    <el-dialog :title="title" :visible="visible">
      {{form}}
      <el-form :model="form">
        <el-form-item label="栏目名称" label-width="80px">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="栏目介绍" label-width="80px">
          <el-input type="textarea" v-model="form.description" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="父栏目" label-width="80px">
          <el-select clearable v-model="form.parentId" placeholder="请选择父栏目">
            <el-option v-for="c in categories" :key="c.id" :label="c.name" :value="c.id"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="visible = false" size="small">取 消</el-button>
        <el-button type="primary" @click="submitForm" size="small">确 定</el-button>
      </div>
    </el-dialog>
    <!-- /模态框 -->
  </div>
</template>

<script>
import request from '@/utils/request'
import qs from 'querystring'
export default {
    //模板中用到的数据
    data(){
        return{
            visible:false,
            form:{},
            title:"新增栏目",
            categories:[] ,
            ids:[]
        }
    },
    //生命周期钩子函数
    created(){
        this.reloadData();
    },
    //模板中用到的方法
    methods:{
        handleSelectionChange(val){
            this.ids = val.map(item => item.id);
        },
        reloadData(){
            let url = "/category/findAll"
            request.get(url)
            .then(response => {
                this.categories = response.data;
            })
        },
        toAdd(){
            this.form = {};
            this.visible = true
        },
        toEdit(val){
            this.form = val;
            this.visible = true;
        },
        batchDelete(){
            this.$confirm('此操作将永久删除选定栏目，是否继续？','提示',{
                confirmButtonText:'确定',
                cancelButtonText:'取消',
                type:'warning'
            })
            .then(() => {
                let url="/category/batchDelete";
                request.request({
                    url,
                    method:"post",
                    headers:{
                        "Content-Type":"application/x-www-form-urlencoded"
                    },
                    data:qs.stringify({ids:this.ids})
                })
                .then(response => {
                    this.$message({
                        type:'success',
                        message:response.message
                    });
                    this.reloadData();
                })
            })
        },
        toDelete(id){
            this.$confirm('此操做将永久删除该栏目,是否继续？','提示',{
                confirmButtonText:'确定',
                cancelButtonText:'取消',
                type:'warning'
            })
            .then(() => {
                let url="/category/deleteById"
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
        submitForm(){
            request.request({
                url:'/category/saveOrUpdate',
                method:'post',
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded",
                },
                data:qs.stringify(this.form)  
            })
            .then(response => {
                this.$message({
                    type:'success',
                    message:response.message
                });
                this.visible = false;
                this.reloadData();
            })
        }
    }
}
</script>

<style lang="stylus" scoped>

</style>