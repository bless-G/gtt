<template>
    <div>
        <!--按钮-->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">删除</el-button>
        <!--按钮结束-->
        <!--表格-->
    <el-table :data="customers">
        <el-table-column type="selection"></el-table-column>
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="realname" label="姓名"></el-table-column>
        <el-table-column prop="gender" label="性别"></el-table-column>
        <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
        <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
        <el-table-column width="120" prop="telephone" label="手机号"></el-table-column>
        <el-table-column fixed="right" label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
            </template>
        </el-table-column>
    </el-table>
    <!--表格结束-->
    <!--分页开始-->
    <el-pagination
        layout="prev, pager, next" 
        :total="50">
    </el-pagination>
    <!--分页结束-->
    <!-- 模态框 -->
    <el-dialog
        title="录入顾客信息"
        :visible.sync="visible"
         width="60%">
         测试{{form}}
         <el-form :model="form" label-width="80px">
             <el-form-item label="用户名">
            <el-input  v-model="form.username"></el-input>
             </el-form-item>
             <el-form-item label="密码">
                 <el-input type="password" v-model="form.password"></el-input>
             </el-form-item>
             <el-form-item label="姓名">
            <el-input  v-model="form.realname"></el-input>
             </el-form-item>
             <el-form-item label="手机号">
            <el-input  v-model="form.telephone"></el-input>
             </el-form-item>
             </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button @click="closeModelHandler" size="small">取 消</el-button>
    <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
  </span>
</el-dialog>
    <!-- 模态框结束 -->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    // 用于存放网页中需要调用的方法
    methods:{
        loadData(){
            let url="http://localhost:6677/customer/findAll"
            request.get(url).then((response)=>{
            //将查询结果设置到customers
            this.customers=response.data;
        })
        },
        submitHandler(){
            let url="http://localhost:6677/customer/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求结束
                //模态框关闭
                this.closeModelHandler();
                //刷新
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toDeleteHandler(id){
            //确认
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url="http://localhost:6677/customer/deleteById?id="+id;
            request.get(url).then((response)=>{
            this.loadData();
            this.$message({
            type: 'success',
            message: response.message
            });
          })
        })
    
        },
        toUpdateHandler(row){
            //在模态框的表单中显示当前行的信息
            this.form=row;
            this.visible=true;
        },
        toAddHandler(){
            this.form={
                type:"customer"
            }
            this.visible=true;
        },
        closeModelHandler(){
            this.visible=false;
            this.loadData();
        }
    },
    //要向网页中显示的数据
    data(){
     return{
        visible:false,
        customers:[],
        form:{
        type:"customers"
        }
     }
    },
    created(){
        //this为当前vue实例对象
        //vue实例创建完毕
        this.loadData()
        
    }
}
</script>

<style scoped>

</style>