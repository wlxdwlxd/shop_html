<template>
  <div>
    <h1>商品分类管理</h1>

    <el-button type="primary" icon="el-icon-edit" v-on:click="goUpdate"></el-button>
    <el-button type="primary" icon="el-icon-plus" v-on:click="goAdd"></el-button>

    <el-tree
      :data="data"
      :props="defaultProps"
      accordion
      @node-click="handleNodeClick"
    >
    </el-tree>

    <!--  新增的弹框   -->
    <el-dialog title="录入分类信息" :visible.sync="addFormFlag">
      <el-form :model="addTypeForm" ref="addTypeForm"   label-width="80px">
        <el-form-item label="pid" prop="pid">
          <el-input v-model="addTypeForm.pid" autocomplete="off" disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="名称" prop="name">
          <el-input v-model="addTypeForm.name" autocomplete="off" ></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="addFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="addForm">确 定</el-button>
      </div>
    </el-dialog>


    <!--  修改的弹框   -->
    <el-dialog title="修改分类信息" :visible.sync="updFormFlag">
      <el-form :model="updTypeForm" ref="updFormFlag"   label-width="80px">
        <el-form-item label="id" prop="id">
          <el-input v-model="updTypeForm.id" autocomplete="off" disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="名称" prop="name">
          <el-input v-model="updTypeForm.name" autocomplete="off" ></el-input>
        </el-form-item>
        <el-form-item label="pid" prop="pid">
          <el-input v-model="updTypeForm.pid" autocomplete="off" ></el-input>
        </el-form-item>
        <el-form-item label="是否展示" prop="isDel">
          <el-radio v-model="updTypeForm.isDel" label="0">展示</el-radio>
          <el-radio v-model="updTypeForm.isDel" label="1">不展示</el-radio>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="updFormFlag = false">取 消</el-button>
        <el-button type="primary" @click="updForm">确 定</el-button>
      </div>
    </el-dialog>

  </div>
</template>

<script>
  export default {
    name: "Type",
    data(){
      return{

        addFormFlag:false,
        addTypeForm:{
          name:"",
          pid:""
        },
        updFormFlag:false,
        updTypeForm:{
          name:"",
          pid:"",
          id:"",
          isDel:"0"
        },

        data:[],//tree 的数据
        ajaxData:[],// 远程请求的数据
        jsonStr:"", //递归拼接处理
        defaultProps:{}
      }
    }
    ,methods:{
      chandleData:function(){ //ajaxData  处理成 data   //转换数据
        //找到顶层节点的数据
        for (let i = 0; i <this.ajaxData.length ; i++) {
          if(this.ajaxData[i].pid==0){
            this.diguiNode(this.ajaxData[i]);
            break;
          }
        }
        //console.log(this.jsonStr);
        //将字符串 转为json对象
        this.data.push(JSON.parse(this.jsonStr));

      },  //  id  name  pid        id  name children []
      diguiNode:function (node) {
        // 判断是否为父节点
        var bf=this.isParent(node);
        if(bf==true){
          //{"id":1,"label":"分类",}
          //{"id":1,"label":"分类","children":[]}
          this.jsonStr+='{"id":'+node.id+',"label":"'+node.name+'","children":[';
          //拼接子节点
          //查找此节点的子节点
          let count=0
          for (let i = 0; i <this.ajaxData.length ; i++) {
            //判断是否为当前节点的子节点
            if(node.id==this.ajaxData[i].pid){
              count++;
              this.diguiNode(this.ajaxData[i]);
              this.jsonStr+=",";
            }
          }
          //处理多余的,号
          if(count!=0){
            this.jsonStr=this.jsonStr.substr(0,this.jsonStr.length-1);
          }



          //拼完整
          this.jsonStr+=']}';
        }else{
          this.jsonStr+='{"id":'+node.id+',"label":"'+node.name+'"}';
        }

      }
      ,isParent:function(node){ // 判断是否为父节点  pid 有没有指向当前id

        for (let i = 0; i <this.ajaxData.length ; i++) {
          if(node.id==this.ajaxData[i].pid){
            return true;
          }
        }
        return false;
      },
      goAdd:function(){
        this.addFormFlag=true;
      },
      handleNodeClick(data) {
        //alert(JSON.stringify(data));
        this.updTypeForm.id=data.id;
        this.updTypeForm.name=data.label;
        this.updTypeForm.pid=data.id;

        this.addTypeForm.pid=data.id;
      },
      addForm:function () {
        var athis=this;
        this.$ajax.post("http://192.168.1.35:8080/api/type/add",this.$qs.stringify(this.addTypeForm)).then(function () {
          athis.addFormFlag = false;
          //athis.queryData(1,4);
        }).catch(function () {

        })
      },
      goUpdate:function () {
        this.updFormFlag=true;
      },
      updForm:function () {
        var athis=this;
        this.$ajax.post("http://192.168.1.35:8080/api/type/update",this.$qs.stringify(this.updTypeForm)).then(function () {
          athis.updFormFlag = false;
          //athis.queryData(1,4);
        }).catch(function () {

        })
      }


    }
    ,created:function(){
      //远程请求数据
      this.$ajax.get("http://192.168.1.35:8080/api/type/getData").then(res=>{
        this.ajaxData=res.data.data;  // 把请求的数据  赋给全局
        this.chandleData();
      }).catch(err=>console.log(err));
    }
  }
</script>

<style scoped>

</style>
