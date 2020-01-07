<template>
    <div>
    <el-button size="small" type="info" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>

    <el-table :data="products">
        <el-table-column prop = "id" label="编号"></el-table-column>
        <el-table-column prop = "name" label="产品名称"></el-table-column>
        <el-table-column prop = "price" label="价格"></el-table-column>
        <el-table-column width="200px" prop = "description" label="描述"></el-table-column>
        <el-table-column prop = "categoryId" label="所属产品"></el-table-column>
        <el-table-column width="600px" prop = "photo" label="照片"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
            </template>
        </el-table-column>
    </el-table>

    <el-dialog
  title="录入产品信息"
  :visible.sync="visible"
  width="60%">
  {{form}}
  <el-form :model="form" label-width="80px">

    <el-form-item label="产品名称">
      <el-input v-model="form.name"></el-input>
    </el-form-item>

    <el-form-item label="价格">
      <el-input v-model="form.price"></el-input>
    </el-form-item>

    <el-form-item label="描述">
      <el-input v-model="form.description"></el-input>
    </el-form-item>
    <el-form-item label="所属栏目">
      <el-select v-model="form.categoryId" placeholder="请选择">
        <el-option v-for="item in options"
          :key="item.id"
          :label="item.name"
          :value="item.id"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="图片">
      <el-upload
        class="upload-demo"
        action="http://134.175.154.93:6677/file/upload"
        :file-list="fileList"
        list-type="picture"
        :on-success="uploadSuccessHandler">
        <el-button size="small" type="primary">点击上传</el-button>
        <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
      </el-upload>
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
import querystring,{ stringify } from 'querystring'
export default {
  
    //methods用于存放网页中需要调用的方法
    methods:{
      loadCategory(){
        let url = "http://localhost:6677/category/findAll";
        request.get(url).then((response)=>{
            this.options = response.data;
        });
        },
      uploadSuccessHandler(response){
        //上传成功
        let photo = "http://134.175.154.93:8888/"+response.data.grougname+"/"+response.data.id;
        console.log(response);
        this.form.photo = photo;
      },
      submitHandler(){
      let url = "http://localhost:6677/product/saveOrUpdate"
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
      loadData(){
        let url = "http://localhost:6677/product/findAll"
      request.get(url).then((response)=>{
        //将查询结果设置到product中,this指向外部函数的this
        this.products = response.data;
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
          let url = "http://localhost:6677/product/deleteById?id="+id;
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
            this.form = row;
          this.visible = true;
        },
        closeModulHandler(){
        this.visible = false;
        },
        toAddHandler(){
        this.visible = true;
        }

    },
    //data（）用于存放要向网页中显示的数据
    data(){
        return{
        fileList:[],
        visible:false,
        products:[],
        options:[],
        form:{}
        }
    },
    created(){
      //this为当前vue实例
      //vue实例创建完毕
      this.loadData();
      this.loadCategory();
      }
    }
</script>
<style scoped>
</style>