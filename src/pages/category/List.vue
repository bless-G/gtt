<template>
    <div>
        <!--按钮-->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!--按钮结束-->
        <!--表格-->
    <el-table :data="category">
        <el-table-column type="selection"></el-table-column>
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="name" label="栏目名称"></el-table-column>
        <el-table-column prop="num" label="序号"></el-table-column>
        <el-table-column prop="parentId" label="父栏目"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">批量删除</a>
                <a href="" @click.prevent="toUpdateHandler">修改</a>
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
        :title="title"
        :visible.sync="visible"
         width="60%">
         测试{{form}}
    <el-form :model="form" label-width="80px">
        <el-form-item label="栏目名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="序号">
          <el-input v-model="form.num"></el-input>
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
        //get传参无需考虑编码，只能传字符串
        loadData(){   //重载员工数据
            let url="http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{    //this->vue实例，通过vue实例访问该实例中的数据，methods中的数据
            //将查询结果设置到employees
            this.category=response.data;    //箭头函数中的this指向外部函数中的this
        })
        },
        submitHandler(){
            let url="http://localhost:6677/category/saveOrUpdate";
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
            this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
        })
    
        },
        toUpdateHandler(){
            this.title="修改栏目信息"
            this.visible=true;
        },
        toAddHandler(){
            this.title="添加栏目信息"
            this.visible=true;
        },
        closeModelHandler(){
            this.visible=false;
        }
    },
    //要向网页中显示的数据
     data(){
            return{
              visible:false,
              category:[],
              form:{},
              type:"category"
           
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