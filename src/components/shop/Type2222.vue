<template>
  <div>
    <h1>商品分类管理</h1>
    <el-tree
      :props="props"
      :load="loadNode"
      lazy
      show-checkbox>
    </el-tree>
  </div>
</template>

<script>
    export default {
        name: "Type2222",
        data(){
          return{
            props: {
              label: 'name',
              children: 'zones',
              isLeaf: 'leaf',
            },
            data:[],//tree 的数据
            ajaxData:[],// 远程请求的数据
            jsonStr:"", //递归拼接处理
            defaultProps:{}
          }
        },methods:{
        chandleData:function(){
          for (let i = 0; i <this.ajaxData.length; i++) {
              if (this.ajaxData[i].pid==0){
                this.diguiNode(this.ajaxData[i]);
                break;
              }
          }
         /* console.log(this.jsonStr);
          //将字符串 转为json对象
          this.data.push(JSON.parse(this.jsonStr));*/
        },
        diguiNode:function(node){

        },
        loadNode(node, resolve) {
          if (node.level === 0) {
            return resolve([{ name: "qwe" }]);
          }
          if (node.level > 1) return resolve([]);

          setTimeout(() => {
            const data = [{
              name: 'leaf',
              leaf: true
            }, {
              name: 'zone'
            }];

            resolve(data);
          }, 500);
        }
      },created:function () {
        //远程请求数据
        this.$ajax.get("http://192.168.1.35:8080/api/type/getData").then(res=>{
          this.ajaxData=res.data.data;  // 把请求的数据  赋给全局
          this.chandleData();
          console.log(this.ajaxData);
        }).catch(err=>console.log(err));
      }
    }
</script>

<style scoped>

</style>
