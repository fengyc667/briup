<template>
    <div>
        <!-- 按钮 -->
        <el-button type="warning" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="success" size="small">批量删除</el-button>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="products">
            <el-table-column type="selection" fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="price"  label="价格"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column width="200" prop="categoryId" label="所属产品"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <!-- {{slot.row}} -->
                    <!-- <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>-->
                    <i class="el-icon-edit" @click.prevent="toUpdateHandler(slot,row)"></i> 
                    <!-- <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a> -->
                    <i class="el-icon-delete" @click.prevent="toDeleteHandler(slot,row.id)"></i> 
                    <a href="" @click.prevent="">详情</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50">
        </el-pagination>
        <!-- /分页 -->
        <!-- 模块 -->
        <el-dialog :title="title" :visible.sync="visible" width="60%" >
            <el-form label-width="80px">
                <!-- <el-form-item label="产品编号">
                    <el-input v-model="form.id"/>
                </el-form-item> -->
                <el-form-item label="产品名称">
                    <el-input v-model="form.name"/>
                </el-form-item>
                <el-form-item label="价格">
                    <el-input v-model="form.price"/>
                </el-form-item>
                <el-form-item label="产品描述">
                    <el-input v-model="form.description"/>
                </el-form-item>
                <el-form-item label="所属产品">
                    <el-input v-model="form.categoryId"/>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submintHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!-- /模块 -->
    </div>
</template>

<script>
import request from '@/utils/request'//第三方自定义库
import querystring from 'querystring'//系统库
export default {
   methods:{
        submintHandler(){
            let url="http://localhost:6677/product/saveOrUpdate";
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据，将this.form转换为查询字符串发送给后台
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
        loadData(){
            let url="http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
            //将查询结果设置到response中,箭头函数中的this指向外部函数的this
                this.products=response.data;
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
            message: '删除成功!'//测试id是否拿到
          });
        })
        },
        toUpdateHandler(row){
            this.title="修改产品信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.title="录入产品信息";
            this.visible=true;
        }
    },
    data(){
        return{
            title:"录入产品信息",
            visible:false,
            products:[],
            form:{
                type:"category"
            }
        }
    },
    created(){
        //vue创建完毕所需执行操作,在页面加载出来的时候加载数据
      this.loadData();
    }
}
</script>

<style scoped>

</style>