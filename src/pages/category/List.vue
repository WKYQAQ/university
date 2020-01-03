<template>
<!--按钮-->
<div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
<!--/按钮-->
<!--表格-->
<el-table :data="category"><!--为是啥用：data？答：详情element，表格-->
    <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
    <el-table-column fixed="left" prop="name" label="栏目名"></el-table-column>
    <el-table-column fixed="left" prop="num" label="号码"></el-table-column>
    <el-table-column width="120" prop="parentId" label="父编号"></el-table-column>
    <el-table-column width="200" prop="icon" label="图标">
        <template   slot-scope="scope">            
                    <img :src="scope.row.icon"  min-width="70" height="70" />
                 </template>   
    </el-table-column>
    <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
            <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
            <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>   
    </el-table-column>
</el-table>
<!--/表格-->
<!--分页-->
<el-pagination
    layout="prev, pager, next"
    :total="1000">
  </el-pagination>
<!--/分页-->
<!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="30%">
    {{form}}
  <el-form :model="form" label-width="80px">
        <el-form-item label="栏目名">
            <el-input type="name" v-model="form.name"/>
        </el-form-item>
        <el-form-item label="编号">
            <el-input v-model="form.id"/>
        </el-form-item>
        <el-form-item label="号码">
            <el-input v-model="form.num"/>
        </el-form-item>
        <el-form-item label="父编号">
            <el-input v-model="form.parentId"/>
        </el-form-item>
        <el-form-item label="图标">
            <el-input  v-model="form.icon"/>
        </el-form-item>    
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
<!--/模态框-->
</div>  
</template>
<script>
import querystring from 'querystring'
import request from "@/utils/request"
export default {
    methods:{
        upLoad() {
      // 触发上传图片按钮
      this.$refs.avatarInput.dispatchEvent(new MouseEvent("click"));
    },
        submitHandler(){
            let url="http://localhost:6677/category/saveOrUpdate"
            //前端后台发送请求，完成数据的保存操作
            request({
                url,
                method:"POST",
                //告诉给后台我的请求中放的是查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据,将this.form转换未查询字符串发送给后台
                data:querystring.stringify(this.form)}).then((response)=>{
            //请求结束
            this.closeModalHandler();
            this.loaddata();
            this.$message({
              type:"success",
              message:response.message
            })   
                })
        },
        loaddata(){
            let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                this.category=response.data;
            })
        },
        toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!' +id
          });
        })
        },
        toUpdateHandler(row){
            this.title="修改栏目信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },  
        toAddHandler(){
            this.title="录入栏目信息";
            this.visible=true;
        }
    },
    data(){
        return{
            visible:false,
            category:[],
            form:{
                type:"category"
            },
            title:"添加栏目"

        }
    },
    created(){
        this.loaddata();
    }
}
</script>
<style scoped>

</style>