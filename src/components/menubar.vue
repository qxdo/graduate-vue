<template>
  <div>
    <nav style="z-index: 2;width: 20%;height: 1000px;">
      <el-tree style="width: 100%;height: 1000px;z-index: 2;"
               :data="treeData"
               :props="defaultProps"
               accordion
               @node-click="handleNodeClick">

      </el-tree>
    </nav>
    <!--<nav>
      <el-button type="text" @click="dialogVisible = true">点击打开 Dialog</el-button>

      <el-dialog
        title="提示"
        :visible.sync="dialogVisible"
        width="30%"
        :before-close="handleClose">
        <span>这是一段信息</span>
        <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
  </span>
      </el-dialog>
    </nav>-->
  </div>

</template>






<script>
  //导入模态对话框
  export default {
    name: "menubar",      //是否显示loading
    data(){
      let _this = this;


      _this.$axios
        .get('/node/list')
        .then(function (res) {
          console.log(res);
          console.log(res.data);
          console.log(res.data.status == 202);

          if( res.data.status == 202 ){



            console.log(res.data);
            console.log(res);

            _this.data = res.data.data;
          }
        })
      console.log(this.data);
      return {
        dialogVisible: false,
        data : [

        ],
        defaultProps: {
          children: 'children',
          /*label: 'title'*/
        }
      }
    },


    computed:{
      treeData(){
        let cloneData = JSON.parse(JSON.stringify(this.data))    // 对源数据深度克隆
        let tree = cloneData.filter(father=>{              //循环所有项
          let branchArr = cloneData.filter(child=>{
            return father.id == child.pid      //返回每一项的子级数组
          });
          if(branchArr.length>0){
            father.children = branchArr;    //如果存在子级，则给父级添加一个children属性，并赋值
          }
          return father.pid==0;      //返回第一层
        });
        return tree     //返回树形数据
      }
    },
    methods:{
      handleNodeClick(data){
        console.log(data);


        //是叶子节点才会有正常的url
        console.log(data.leaf);
        if(data.leaf  == 1){
          this.$emit("menuSelectd",data.url);
          console.log(data.url);
        }
      },
    },
    mounted(){
    }
  }
</script>

<style scoped>

</style>
