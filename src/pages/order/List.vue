<template>
    <div>
        <el-button type="warning" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        
        <el-table :data="order.list">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="订单时间" prop="orderTime"></el-table-column>
            <el-table-column label="价格" prop="total"></el-table-column>
            <el-table-column label="状态" prop="status"></el-table-column>
            <el-table-column label="顾客ID" prop="customerId"></el-table-column>
            <el-table-column label="员工ID" prop="waiterId"></el-table-column>
            <el-table-column label="地址ID" prop="addressId"></el-table-column>
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
        layout="prev, pager, next" 
        :total="order.total" 
        @current-change="pageChageHandler">
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
                    <el-select v-model="form.categoryId" placeholder="请选择">
                         <el-option v-for="item in options"
                         :key="item.id"
                         :label="item.name"
                         :value="item.id"></el-option>
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
        loadCategory(){
        let url = "http://localhost:6677/category/findAll";
        request.get(url).then((response)=>{
            this.options = response.data;
        });
        },
        pageChageHandler(page){
            //将当前页改为插件中的当前页
            this.params.page = page-1;
            //加载
            this.loadData();
        },
        loadData(){
        let url = "http://localhost:6677/order/queryPage";
        request({
            url,
            method:"POST",
            headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
            data:querystring.stringify(this.params)
        }).then((response)=>{
            this.order = response.data;
        })
        },
        toAddHandler(){
            this.visible = true;
            this.title = "添加产品信息";
        },
        closeHandlerwithYES(){
            let url = "http://localhost:6677/order/saveOrUpdate"
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
        toDeleteHandler(){
                this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    let url = "http://localhost:6677/order/deleteById?id="+id
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
            order:{},
            options:[],
            form:{
                type:"order",
            },
            params:{
                page:0,
                pageSize:10,
            }
        };
    },
    created(){
        this.loadData();
        this.loadCategory();
    }
}
</script>
<style scoped>
</style>