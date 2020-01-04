<template>
    <div>
    <el-button size="small" type="info" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">删除</el-button>

    <el-table :data="employees">
        <el-table-column prop = "id" label="编号"></el-table-column>
        <el-table-column prop = "username" label="用户名"></el-table-column>
        <el-table-column prop = "password" label="密码"></el-table-column>
        <el-table-column prop = "realname" label="姓名"></el-table-column>
        <el-table-column prop = "gender" label="性别"></el-table-column>
        <el-table-column prop = "idCard" label="身份证号"></el-table-column>
        <el-table-column prop = "telephone" label="手机号"></el-table-column>
        <el-table-column label="操作">

            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
            </template>
        </el-table-column>
    </el-table>

    <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%">
  测试：{{form}}
  <el-form label-width="80px" :model="form">
      <el-form-item label="用户名">
      <el-input v-model="form.username"></el-input>
    </el-form-item>

    <el-form-item label="密码">
      <el-input type="password" v-model="form.password"></el-input>
    </el-form-item>

    <el-form-item label="真实姓名">
      <el-input v-model="form.realname"></el-input>
    </el-form-item>

    <el-form-item label="性别">
      <el-radio-group v-model="form.gender">
    <el-radio :label="3">男</el-radio>
    <el-radio :label="6">女</el-radio>
  </el-radio-group>
    </el-form-item>

    <el-form-item label="手机号">
      <el-input v-model="form.telephone"></el-input>
    </el-form-item>

    <el-form-item label="身份证号">
      <el-input v-model="form.idCard"></el-input>
    </el-form-item>


  </el-form>
  <span>这是一段信息</span>
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
            employees:[],
            form:{
             type : "waiter"
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
      let url = "http://localhost:6677/waiter/saveOrUpdate"
      request({
        url,
        method:"post",
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
            let url = "http://localhost:6677/waiter/findAll";
            request.get(url).then((response)=>{
        //箭头函数中的this指向外部函数中的this
        this.employees = response.data;
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
          let url = "http://localhost:6677/waiter/deleteById?id="+id;
          request.get(url).then((response)=>{
            //刷新数据
            this.loadData();
            //提示效果
            this.$message({
              type:'success',
              message:response.message
            });

          })
          this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
        })
        },
        toUpdataHandler(row){
          //在模态框的表单中显示本行的信息
          this.form = row;
          this.visible = true;
        },
        toAddHandler(){
            this.title = "录入员工信息";
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