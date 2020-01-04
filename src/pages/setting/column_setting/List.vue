<template>
    <div>
        <el-button type="warning" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        
        <el-table :data="category">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="栏目名称" prop="name"></el-table-column>
            <el-table-column label="序号" prop="num"></el-table-column>
            <el-table-column label="父栏目" prop="parentId"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>&nbsp;&nbsp;
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>

        <!-- 分页 -->
        <br/><br/><center>
        <el-pagination 
            background 
            layout="prev, pager, next" 
            :total="1000">
        </el-pagination></center>
        <!-- 对话框 -->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            ---{{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="栏目名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="序号">
                    <el-input v-model="form.num"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" type="primary" @click="closeHandlerwithYES">确 定</el-button>
                <el-button @click="closeHandlerwithNO" size="small">取 消</el-button>
            </span>
        </el-dialog>
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
        loadData(){
        let url = "http://localhost:6677/category/findAll";
        request.get(url).then((response)=>{
            this.category = response.data;
        });
        },
        toAddHandler(){
            this.visible = true;
            this.title = "添加栏目信息";
        },
        closeHandlerwithYES(){
            let url = "http://localhost:6677/category/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response) => {
                //this.visible = false;
                //刷新
                this.loadData();
                this.closeHandlerwithNO();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        closeHandlerwithNO(){
            this.visible = false;
        },
        toUpdateHandler(row){
            this.form = row;
            this.visible = true;
            this.title = "修改栏目信息";
        },
        toDeleteHandler(){
                this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    let url = "http://localhost:6677/category/deleteById?id="+id
                    request.get(url).then((response)=>{
                        this.loadData();
                            this.$message({
                            type: 'success',
                            message: response.message,
                        });
                    })
                });
        }
    },
    data(){
        return {
            title:"添加栏目信息",
            visible:false,
            category:[],
            form:{
                type:"category",
            }
        };
    },
    created(){
        this.loadData();
    }
}
</script>
<style scoped>
</style>