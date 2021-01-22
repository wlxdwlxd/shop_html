<template>
  <div>
    <h3>品牌信息</h3>

    <el-form :inline="true" :model="formInline" class="demo-form-inline">
      <el-form-item label="名称">
        <el-input v-model="formInline.name" placeholder="名称"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="">查询</el-button>
        <el-button type="success" @click="addFormFlag=true">新增</el-button>
      </el-form-item>
    </el-form>



    <el-table
      :data="tableData"
      style="width: 100%"
      @row-click="getDetails"
    >

      <el-table-column
        prop="id"
        label="序号"
        width="180">
      </el-table-column>

      <el-table-column
        prop="name"
        label="品牌名称"
        width="180">
      </el-table-column>

      <el-table-column
        prop="bandE"
        label="首字母">
      </el-table-column>

      <el-table-column
        prop="imgPath"
        label="LOGO">
        <template slot-scope="scope">
          <img width="50px" :src="scope.row.imgPath"/>
        </template>

      </el-table-column>

      <el-table-column
        prop="bandDesc"
        label="品牌介绍">
      </el-table-column>

      <!--<el-table-column
        prop="ord"
        label="汽车品牌">
      </el-table-column>-->

      <el-table-column
        prop="createDate"
        label="上架时间">
      </el-table-column>

      <el-table-column
        prop="updateDate"
        label="下架时间">
      </el-table-column>


      <el-table-column
        prop="author"
        label="操作人">
      </el-table-column>


      <el-table-column
        fixed="right"
        label="操作"
        width="100">
        <template slot-scope="scope">
          <el-button type="text" size="small"@click="() => updateFormFlag=true">编辑</el-button>
          <el-button type="text" size="small" v-on:click="deleteBrand(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>

    </el-table>

    <el-pagination

      @current-change="handleCurrentChange"
      @size-change="handleSizeChange"
      :page-sizes="sizes"
      :page-size="size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="count">
    </el-pagination>


    <!--  新增的弹框   -->
    <el-dialog title="录入汽车信息" :visible.sync="addFormFlag">

      <el-form :model="addBrandForm"  label-width="80px">

        <el-form-item label="品牌名称" prop="name">
          <el-input v-model="addBrandForm.name" autocomplete="off" ></el-input>
        </el-form-item>


        <el-form-item label="首字母" prop="bandE">
          <el-input v-model="addBrandForm.bandE"></el-input>
        </el-form-item>


        <el-form-item label="LOGO">
          <el-upload
            class="upload-demo"
            action="http://192.168.1.35:8080/uploadFile/uploadFiles"
            :on-success="imgCallBack"
            name="file"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">不超过500kb</div>
          </el-upload>
        </el-form-item>

        <el-form-item label="描述信息" prop="bandDesc">
          <el-input type="textarea" v-model="addBrandForm.bandDesc"></el-input>
        </el-form-item>

        <el-form-item label="暗箱排序" prop="ord">
          <el-input v-model="addBrandForm.ord"></el-input>
        </el-form-item>

        <el-form-item label="是否展示" prop="isDel">
          <el-radio v-model="addBrandForm.isDel" label="0">展示</el-radio>
          <el-radio v-model="addBrandForm.isDel" label="1">不展示</el-radio>
        </el-form-item>


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="addForm">确 定</el-button>
      </div>
    </el-dialog>

    <!--  修改的弹框   -->
    <el-dialog title="修改品牌信息" :visible.sync="updateFormFlag">

      <el-form :model="updateBrandForm" ref="updateBrandForm"  label-width="80px">

        <el-form-item label="id" prop="id">
          <el-input v-model="updateBrandForm.id" autocomplete="off" ></el-input>
        </el-form-item>

        <el-form-item label="品牌名称" prop="name">
          <el-input v-model="updateBrandForm.name" autocomplete="off" ></el-input>
        </el-form-item>


        <el-form-item label="首字母" prop="bandE">
          <el-input v-model="updateBrandForm.bandE"></el-input>
        </el-form-item>


        <el-form-item label="LOGO">
          <el-upload
            class="upload-demo"
            action="http://192.168.1.35:8080/uploadFile/uploadFiles"
            :on-success="imgCallBack"
            name="file"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">不超过500kb</div>
          </el-upload>
        </el-form-item>

        <el-form-item label="描述信息" prop="bandDesc">
          <el-input type="textarea" v-model="updateBrandForm.bandDesc"></el-input>
        </el-form-item>

        <el-form-item label="暗箱排序" prop="ord">
          <el-input v-model="updateBrandForm.ord"></el-input>
        </el-form-item>

        <el-form-item label="是否展示" prop="isDel">
          <el-radio v-model="updateBrandForm.isDel" label="0">展示</el-radio>
          <el-radio v-model="updateBrandForm.isDel" label="1">不展示</el-radio>
        </el-form-item>



      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="updateFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="updateBrand">确 定</el-button>
      </div>
    </el-dialog>






  </div>
</template>

<script>
  import  ajax from 'axios'
  import qs from 'qs'

    export default {
        name: "Brand",
        data(){
          return{
            formInline:{
              name:'',
            },
            tableData: [],
            addFormFlag:false,
            addBrandForm:{
              name:"",
              bandE:"",
              imgPath:"",
              bandDesc:"",
              ord:"",
              isDel:"0",
            },
            updateFormFlag:false,
            updateBrandForm:{
              name:"",
              bandE:"",
              imgPath:"",
              bandDesc:"",
              ord:"",
              isDel:"0",
            },
            count:0,
            sizes:[2,3,5,10],
            size:10,
            start:1
          }
        },methods:{
        getDetails:function(row){
          var athis = this;
          athis.updateBrandForm.id=row.id;
          athis.updateBrandForm.name=row.name;
          athis.updateBrandForm.bandE=row.bandE;
          athis.updateBrandForm.bandDesc=row.bandDesc;
          athis.updateBrandForm.ord=row.ord;
          athis.updateBrandForm.isDel=row.isDel;
          athis.updateBrandForm.imgPath=row.imgPath;
        },
        deleteBrand:function(id){
          var athis=this;
          ajax.get("http://localhost:8080/api/brand/deleteBrand?id="+id).then(function (res) {
            history.go(0);
          }).catch(function () {
          })
        },
      imgCallBack:function(response, file, fileList){ //图片上传的回调函数
                                                       // 赋值
        this.addBrandForm.imgPath=response.data;
      },
      queryData:function () {
          var athis=this;
          ajax.get("http://localhost:8080/api/brand/queryBrandByPage?start="+athis.start+"&size="+athis.size).then(function (res) {
            athis.tableData=res.data.data.data;
            athis.brandData=res.data.data.data;
            athis.count=res.data.data.count;
          }).catch(function () {
          })
        },
        selectVo:function () {
          var athis=this;
          ajax.get("http://localhost:8080/api/brand/queryBrandByPage",qs.stringify(this.formInline)).then(function (res) {
            athis.tableData=res.data.data.data;
          }).catch(function () {
          })
        },
        addForm:function () {
          var athis=this;
          ajax.post("http://localhost:8080/api/brand/addBrand",qs.stringify(this.addBrandForm)).then(function () {
            athis.addFormFlag = false;
           history.go(0);
          }).catch(function () {

          })
        },
        updateBrand:function(rs){
            console.log("ssss"+rs);
            var a =this;
            ajax.post("http://localhost:8080/api/brand/updateBrand",qs.stringify(this.updateBrandForm)).then(res=>{
              this.updateFormFlag=false;
              a.queryData(1);
            }).catch(err=>console.log(err))
        },
        handleCurrentChange:function(start){ //跳转页面
          console.log(start);
          this.start=start;
          this.queryData(start);
        },
        handleSizeChange:function(size){ //跳转页面
          this.size=size;
          this.queryData(1);
        }

      },created:function () {
        //请求数据
        this.queryData(1,8);
        //查询品牌数据
      }
    }
</script>

<style scoped>

</style>
