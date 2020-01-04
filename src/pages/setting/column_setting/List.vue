<template>
    <div>
        <el-button type="warning" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        
        <el-table :data="product">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="产品名称" prop="name"></el-table-column>
            <el-table-column label="价格" prop="price"></el-table-column>
            <el-table-column label="描述" prop="description"></el-table-column>
            <el-table-column label="所属产品" prop="categoryId"></el-table-column>
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
                <el-form-item label="名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="价格">
                    <el-input v-model="form.price"></el-input>
                </el-form-item>
                <el-form-item label="所属栏目">
                     <el-select v-model="form.status" placeholder="请选择">
                    </el-select>
                </el-form-item>
                <el-form-item label="介绍">
                    <el-input type="textarea" v-model="form.description"></el-input>
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
        let url = "http://localhost:6677/product/findAll";
        request.get(url).then((response)=>{
            this.product = response.data;
        });
        },
        toAddHandler(){
            this.visible = true;
            this.title = "添加产品信息";
        },
        closeHandlerwithYES(){
            let url = "http://localhost:6677/product/saveOrUpdate"
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
            this.title = "修改产品信息";
        },
        toDeleteHandler(id){
                this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    let url = "http://localhost:6677/category/deleteById?id="+id;
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
            product:[],
            form:{
                type:"product",
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