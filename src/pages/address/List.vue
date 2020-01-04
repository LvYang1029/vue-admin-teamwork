<template>
    <div>
    <el-button size="small" type="success" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">删除</el-button>

    <el-table :data="address">
        <el-table-column prop = "id" label="编号"></el-table-column>
        <el-table-column prop = "province" label="省"></el-table-column>
        <el-table-column prop = "city" label="市"></el-table-column>
        <el-table-column prop = "area" label="区/县"></el-table-column>
        <el-table-column prop = "address" label="街道"></el-table-column>
        <el-table-column prop = "telephone" label="电话"></el-table-column>
        <el-table-column prop = "customerId" label="顾客ID"></el-table-column>
        <el-table-column label="操作">

            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
            </template>
        </el-table-column>
    </el-table>

    <el-dialog
  title="添加地址信息信息"
  :visible.sync="visible"
  width="60%">
  测试：{{form}}
  <el-form label-width="80px" :model="form">
      <el-form-item label="省">
      <el-input v-model="form.province"></el-input>
    </el-form-item>

    <el-form-item label="市">
      <el-input v-model="form.city"></el-input>
    </el-form-item>

    <el-form-item label="区/县">
      <el-input v-model="form.area"></el-input>
    </el-form-item>
    <el-form-item label="街道">
      <el-input v-model="form.address"></el-input>
    </el-form-item>
    <el-form-item label="手机号">
      <el-input v-model="form.telephone"></el-input>
    </el-form-item>
    <el-form-item label="顾客ID">
      <el-input v-model="form.customerId"></el-input>
    </el-form-item>

  </el-form>

  <span slot="footer" class="dialog-footer">
    <el-button @click="closeModulHandler" size="small">取 消</el-button>
    <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
  </span>
</el-dialog>

    
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring, { stringify } from 'querystring'
export default {
    data(){
        return{
            visible:false,
            address:[],
            form:{
             type : "address"
       }
        }
    },
        created(){
      //this为当前vue实例
      //vue实例创建完毕
      this.loadData();
      },
    methods:{
        submitHandler(){
      let url = "http://localhost:6677/address/saveOrUpdate"
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        //模态框关闭，
        this.closeModulHandler();
        this.loadData();
        //提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })
  },
        //重载员工数据
        loadData(){
            // this 指向vue实例，通过vue来访问该实例中的数据
            let url = "http://localhost:6677/address/findAll";
            request.get(url).then((response)=>{
        //箭头函数中的this指向外部函数中的this
        this.address = response.data;
      })
        },
        
        toDeleteHandler(id){
         //先确认
         this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口，完成删除操作
          let url = "http://localhost:6677/address/deleteById?id="+id;
          request.get(url).then((response)=>{
            //刷新数据
            this.loadData();
            //提示效果
            this.$message({
              type:'success',
              message:response.message
            });

          })
        })
        },
        toUpdataHandler(row){
          //在模态框的表单中显示本行的信息
          this.form = row;
          this.visible = true;
        },
        toAddHandler(){
            this.title = "录入订单信息";
            this.visible = true;
        },
        closeModulHandler(){
            this.visible = false;
        },
    }
    
}
</script>
<style scoped>

</style>