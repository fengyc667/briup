<template>
    <div>
        <!--按钮-->
        <el-button type="info" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!--/按钮-->
        <!--表格-->
        <el-table :data="customers">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <!-- slot获取当前行信息 -->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!--/表格结束-->
        <!--/分页开始-->
        <!-- <el-pagination layout="prev, pager, next" :total="50">
        </el-pagination> -->
        <!--/分页结束-->
        <!--/对话框开始-->
        <el-dialog :title="title" :visible.sync="visible" width="60%" >
            {{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="=密码">
                    <el-input type="password" v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="=真实姓名">
                    <el-input  v-model="form.realname"></el-input>
                </el-form-item>
                <el-form-item label="=手机号">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
            </el-form>
            <!-- <span>这是一段信息</span> -->
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!--/对话框结束-->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
             let url="http://localhost:6677/customer/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到response中,this指向外部函数的this
            this.customers=response.data;
        })

        },
        submitHandler(){
            //this.form对象 --字符串--> 后台
            //通过request与后台交互，并且要携带参数
            let url="http://localhost:6677/customer/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //模态框关闭
                this.closeModalHandler();
                this.loadData();
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
            //调用后台接口，完成删除操作
            let url="http://localhost:6677/customer/deleteById"
            request.get(url+"?id="+id).then((response)=>{
                //刷新数据
                this.loadData();
                //提示结果
                this.$message({
                type: 'success',
                message: '删除成功!'+id//测试id是否拿到
            });
            })
        })
        },
        toUpdateHandler(){
            //模态框表单中显示当前行信息
            this.title="修改顾客信息";
            this.visible=true;
            this.form=row;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            //将form变为初始值
            this.form = {
                type:"customer"
            }
            this.visible=true;
        }
    },

     //data是用于存放网页中显示的数据
    data(){
        return{
            title:"录入顾客信息",
            visible:false,
            customers:[],
            form:{
                type:"customer"
            }
        }
    },
    created(){
        //vue创建完毕所需执行操作
      this.loadData();
    }
}
</script>

<style scoped>

</style>