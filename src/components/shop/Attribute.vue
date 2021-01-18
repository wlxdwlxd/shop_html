<template>
  <div>
    <h1>属性</h1>

    <el-button type="success" @click="addFormFlag=true">新增</el-button>

    <el-table
      :data="tableData"
      style="width: 100%"
      @row-click="getDetails"
    >

      <el-table-column
        prop="id"
        label="序号"
        width="70">
      </el-table-column>


      <el-table-column
        prop="nameCH"
        label="中文名称">
      </el-table-column>

      <el-table-column
        prop="name"
        label="英文名称"
        width="180">
      </el-table-column>

      <el-table-column
        prop="typeId"
        label="分类">
      </el-table-column>


      <el-table-column
        prop="type"
        label="属性的类型">
      </el-table-column>

      <!-- <el-table-item label="特殊资源">
         <el-radio-group  size="medium">
           <el-radio border label="线上品牌商赞助"></el-radio>
           <el-radio border label="线下场地免费"></el-radio>
         </el-radio-group>
       </el-table-item>-->

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
        width="200">
        <template slot-scope="scope">
          <el-button type="text" size="small" v-if="scope.row.type!=3" v-on:click= "selectValueFlagDiv(scope.row)">维护分类</el-button>
          <el-button type="text" size="small" @click="() => updateFormFlag=true">编辑</el-button>
          <el-button type="text" size="small" v-on:click="deleteSku(scope.row.id)">删除</el-button>
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

    <!--  维护分类的弹框   -->
    <el-dialog title="录入属性信息" :visible.sync="selectValueFlag">
      <el-button type="success" icon="el-icon-plus" circle @click="showValueFrom"></el-button>
      <el-table
        :data="tableDataValue"
        style="width: 100%"
      >

        <el-table-column
          prop="id"
          label="序号"
          width="70">
        </el-table-column>

        <el-table-column
          prop="nameCH"
          label="中文名称">
        </el-table-column>

        <el-table-column
          prop="name"
          label="英文名称"
          width="180">
        </el-table-column>

        <el-table-column
          fixed="right"
          label="操作"
          width="200">
          <template slot-scope="scope">

          </template>
        </el-table-column>

      </el-table>


      <el-form :model="valueform" label-width="80px" v-if="ShowValueFormTable">
        <el-form-item label="中文名称">
          <el-input v-model="valueform.nameCH"></el-input>
        </el-form-item>
        <el-form-item label="英文名称">
          <el-input v-model="valueform.name"></el-input>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="addValue">添加</el-button>
          <el-button @click="ShowValueFormTable=false">取消</el-button>
        </el-form-item>
      </el-form>

      <!--<div slot="footer" class="dialog-footer">
        <el-button @click="selectValueFlag = false">取 消</el-button>
        <el-button type="primary">确 定</el-button>
      </div>-->
    </el-dialog>




    <!--  新增的弹框   -->
    <el-dialog title="录入属性信息" :visible.sync="addFormFlag">

      <el-form :model="addAttributeForm"  label-width="80px">

        <el-form-item label="属性名称" prop="name">
          <el-input v-model="addAttributeForm.name" autocomplete="off" ></el-input>
        </el-form-item>


        <el-form-item label="中文名称" prop="nameCH">
          <el-input v-model="addAttributeForm.nameCH"></el-input>
        </el-form-item>

        <el-form-item label="TypeId" prop="typeId">
          <el-input v-model="addAttributeForm.typeId"></el-input>
        </el-form-item>

        <el-form-item label="是否SKU" prop="isSKU">
          <el-radio v-model="addAttributeForm.isSKU" label="0">否</el-radio>
          <el-radio v-model="addAttributeForm.isSKU" label="1">是</el-radio>
        </el-form-item>

        <el-form-item label="是否删除" prop="isDel">
          <el-radio v-model="addAttributeForm.isDel" label="0">不删除</el-radio>
          <el-radio v-model="addAttributeForm.isDel" label="1">删除</el-radio>
        </el-form-item>

        <!--属性的类型    0 下拉框     1 单选框      2  复选框   3  输入框-->
        <el-form-item label="属性的类型" prop="type">
          <el-radio v-model="addAttributeForm.type" label="0">下拉框</el-radio>
          <el-radio v-model="addAttributeForm.type" label="1">单选框</el-radio>
          <el-radio v-model="addAttributeForm.type" label="2">复选框</el-radio>
          <el-radio v-model="addAttributeForm.type" label="3">输入框</el-radio>
        </el-form-item>
<!--
        <el-form-item label="操作人" prop="author">
          <el-input v-model="addAttributeForm.author"></el-input>
        </el-form-item>-->


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="addForm">确 定</el-button>
      </div>
    </el-dialog>


    <!--  修改的弹框   -->
    <el-dialog title="修改属性信息" :visible.sync="updateFormFlag">

      <el-form :model="updateAttributeForm"  label-width="100px">

        <el-form-item label="属性名称" prop="name">
          <el-input v-model="updateAttributeForm.name" autocomplete="off" ></el-input>
        </el-form-item>


        <el-form-item label="中文名称" prop="nameCH">
          <el-input v-model="updateAttributeForm.nameCH"></el-input>
        </el-form-item>

        <el-form-item label="分类" prop="typeId">
          <el-input v-model="updateAttributeForm.typeId"></el-input>
        </el-form-item>

        <el-form-item label="是否SKU" prop="isSKU">
          <el-radio v-model="updateAttributeForm.isSKU" :label="0">否</el-radio>
          <el-radio v-model="updateAttributeForm.isSKU" :label="1">是</el-radio>
        </el-form-item>

        <!--   <el-form-item label="是否删除" prop="isDel">
             <el-radio v-model="updateAttributeForm.isDel" :label="0">不删除</el-radio>
             <el-radio v-model="updateAttributeForm.isDel" :label="1">删除</el-radio>
           </el-form-item>-->

        <!--属性的类型    0 下拉框     1 单选框      2  复选框   3  输入框-->
        <el-form-item label="属性的类型" prop="type">
          <!--:label  表示获取选项的value-->
          <el-radio v-model="updateAttributeForm.type" :label="0">下拉框</el-radio>
          <el-radio v-model="updateAttributeForm.type" :label="1">单选框</el-radio>
          <el-radio v-model="updateAttributeForm.type" :label="2">复选框</el-radio>
          <el-radio v-model="updateAttributeForm.type" :label="3">输入框</el-radio>
        </el-form-item>

        <!-- <el-form-item label="操作人" prop="author">
           <el-input v-model="updateAttributeForm.author"></el-input>
         </el-form-item>-->


      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="updateFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="updateForm">确 定</el-button>
      </div>
    </el-dialog>


  </div>
</template>

<script>
  export default {
    name: "Attribute",
    data(){
      return{
        tableDataValue:[],
        valueform:{
          name:"",
          attrId:"",
          nameCH:""
        },
        tableData:[],
        sizes:[2,3,5,10],
        size:10,
        start:1,
        count:0,
        /*新增*/
        addFormFlag:false,
        addAttributeForm:{
          name:"",
          nameCH:"",
          typeId:"",
          type:"",
          isSKU:"",
          isDel:"0",
        },
        updateFormFlag:false,
        updateAttributeForm:{
          name:"",
          nameCH:"",
          typeId:"",
          type:[],
          isSKU:"",
          isDel:"",
          author:""
        },
        selectValueFlag:false,
        ShowValueFormTable:false
      }
    },
    methods:{
      queryData:function () {
        var athis=this;
        this.$ajax.get("http://localhost:8080/api/attribute/queryAttByPage?start="+this.start+"&size="+this.size).then(function (res) {
          athis.tableData=res.data.data.data;
          athis.count=res.data.data.count;
        }).catch(function () {

          alert("查询失败")
        })
      },deleteSku:function (id) {
        this.$ajax.get("http://localhost:8080/api/attribute/deleteAttribute?id="+id).then(function () {
          alert("删除成功");
          history.go(0);
        }).catch(function () {

        })
      },
      addForm:function () {
        var athis=this;
        this.$ajax.post("http://localhost:8080/api/attribute/addAttribute",this.$qs.stringify(this.addAttributeForm)).then(function () {
          athis.addFormFlag = false;
         history.go(0);
        }).catch(function () {

        })
      }, getDetails(row){
        var athis = this;
        athis.updateAttributeForm.id=row.id;
        athis.updateAttributeForm.name=row.name;
        athis.updateAttributeForm.nameCH=row.nameCH;
        athis.updateAttributeForm.typeId=row.typeId;
        athis.updateAttributeForm.type=row.type;
        athis.updateAttributeForm.isSKU=row.isSKU;
        athis.updateAttributeForm.isDel=row.isDel;
        athis.updateAttributeForm.author=row.author;
        athis.querySkuValue(athis.updateAttributeForm.id);
        console.log("属性修改"+JSON.stringify(row))//此时就能拿到整行的信息
      },
      querySkuValue:function(skuId){
        var athis=this;
        this.$ajax.get("http://localhost:8080/api/skuValue/querySkuValue?skuId="+skuId).then(function (res) {
          console.log(res);
          athis.tableDataValue=res.data.data;
        }).catch(function () {
          this.$message.success("失败");
        })
      },
      updateForm:function (rs) {
        var a =this;
        this.$ajax.post("http://localhost:8080/api/attribute/updateAttributeById",this.$qs.stringify(this.updateAttributeForm)).then(res=>{
          this.updateFormFlag=false;
          a.queryData(1);

        }).catch(err=>console.log(err))

      },
      addValue:function(){
        //发起请求 添加数据
        this.$ajax.post("http://localhost:8080/api/skuValue/addSkuValue",this.$qs.stringify(this.valueform)).then(res=>{
          this.$message.success("新增成功");
          //关闭form表单
          this.ShowValueFormTable=false;
          //刷新table
        })
      },
      showValueFrom:function () {
        this.ShowValueFormTable=true
      },
      selectValueFlagDiv:function (row) {
        this.selectValueFlag=true;
        this.valueform.attrId=row.id;

      }
      ,handleCurrentChange:function(start){ //跳转页面
        console.log(start);
        this.start=start;
        this.queryData(start);
      },handleSizeChange:function(size){ //跳转页面
        this.size=size;
        this.queryData(1);
      }

    },created:function () {
      //请求数据
      this.queryData(1,2);
      //查询品牌数据
    }
  }
</script>

<style scoped>

</style>
