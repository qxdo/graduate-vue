<template>
    <div id="node-add" style="width: 10px; height:20px;">
        <nav style="z-index: 2;width: 100%;height: 300px;">
            <el-button @click="createNode()">新增一级节点</el-button>
            <el-tree

                    :data="treeData"
                    show-checkbox
                    node-key="id"
                    :expand-on-click-node="true">
          <span class="custom-tree-node" slot-scope="{ node, data }">
              <span>{{ node.label }}</span>
            <span>
              <el-button
                      type="text"
                      size="mini"
                      @click="() => append(data)">
                +
              </el-button>
              <el-button
                      type="text"
                      size="mini"
                      @click="() => remove(node, data)">
                -
              </el-button>
            </span>
          </span>
            </el-tree>
        </nav>


        <el-dialog title="填写节点信息" :visible.sync="dialogFormVisible">

            <el-form :rules="rules" ref="form" :model="form">
                <el-form-item label="节点名称" :label-width="formLabelWidth" prop="label" >
                    <el-input v-model="form.label" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="节点URL" :label-width="formLabelWidth" prop="url">
                    <el-input v-model="form.url" autocomplete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="()=>closeDialog()">取 消</el-button>
                <el-button type="primary" @click="() =>conformDialog('form')">确 定</el-button>
            </div>
        </el-dialog>

        <el-dialog
                title="提示"
                :visible.sync="dialogVisible"
                width="30%">
            <span>{{tips}}</span>
            <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
        </span>
        </el-dialog>

        <!--<el-dialog
                title="确定操作？"
                :visible.sync="dialogVisible2"
                width="30%">
            <span>确认删除？</span>
            <span slot="footer" class="dialog-footer">
          <el-button @click="">取 消</el-button>
          <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
        </span>
        </el-dialog>-->
    </div>
</template>

<script>

    /**
     * //console.log("nodeData:"+data);
     //传值
     //console.log("data.id="+data.id+data.url+data.rank);

     /*const newChild = { id: id++, label: 'testtest', children: [] };
     //dialog is true ->
     if (!data.children) {
            this.$set(data, 'children', []);
            data.children.push(newChild);
          }
     */
    import Qs from 'qs'
    export default {
        name: "add",

        data() {
            let _this = this;
            _this.$axios
                .get('/node/list')
                .then(function (res) {
                    console.log(res);
                    console.log(res.data);
                    console.log(res.data.status === 202);

                    if (res.data.status === 202) {
                        _this.data = res.data.data;
                    }

                })
            //axios end
            //console.log(this.data);
            return {
                tips: '',
                nodeData: {},
                errors: [],
                dialogFormVisible: false,
                dialogVisible: false,
                dialogVisible2: false,

                form: {
                    label: '',
                    url: '',
                    leaf: '',
                    id: '',
                },
                rules: {
                    label: [
                        {required: true, message: '请输入节点标签', trigger: 'blur'},
                        {min: 2, max: 20, message: '长度在 2 到 20 个字符，无特殊标点符号', trigger: 'blur'}
                    ],
                    url: [
                      {required: true, message: '请输入URL', trigger: 'blur'},
                      {min: 2, max: 20, message: '长度在 2 到 20 个字符', trigger: 'blur'}
                    ],
                },
                formLabelWidth: '120px',
                data: [],
                defaultProps: {
                    children: 'children',
                    label: 'title'
                },
            }
        },
        computed: {
            treeData() {
                let cloneData = JSON.parse(JSON.stringify(this.data))    // 对源数据深度克隆
                let tree = cloneData.filter(father => {              //循环所有项
                    let branchArr = cloneData.filter(child => {
                        return father.id === child.pid      //返回每一项的子级数组
                    });
                    if (branchArr.length > 0) {
                        father.children = branchArr;    //如果存在子级，则给父级添加一个children属性，并赋值
                    }
                    return father.pid === 0;      //返回第一层
                });
                return tree     //返回树形数据
            }
        },

        methods: {
            createNode() {
                this.dialogFormVisible = true;
            },
            append(data) {
                this.nodeData = data;

                this.dialogFormVisible = true;//打开dialog


            },
            remove(node, data) {
                if (data.pid != 0) {
                    alert("确认删除?");
                    this.$axios
                        .get('/node/delete/' + data.id + '/' + data.pid)
                        .then(function (res) {
                            if (res.data.status === 200 || res.data.status === 202 ) {

                              this.tips = '删除成功';

                              this.dialogVisible = true;

                            }

                        })
                }

                const parent = node.parent;

                const children = parent.data.children || parent.data;
                const index = children.findIndex(d => d.id === data.id);
                children.splice(index, 1);
                console.log("node.id" + data.id + "pid:" + data.pid);


                if (data.pid == 0) {

                    this.tips = '一级节点，无法删除';
                    this.dialogVisible = true;
                }
            },
            closeDialog() {
                this.dialogFormVisible = false;
                return 0;
            },


            conformDialog(formName) {


                let _this = this;
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        alert('submit!');
                    } else {
                        console.log('error submit!!');
                        return false;

                    }
                });
                const postData = {
                    pid: _this.nodeData.id,
                    label: _this.form.label,
                    leaf: 1,
                    id: _this.form.id,

                    url: _this.form.url,
                    rank: _this.nodeData.rank + 1,
                };
                let respData = {};
                this.$axios
                    .post('/node/save', Qs.stringify(postData))
                    .then(function (res) {
                        respData = res.data.data;
                        console.log(respData);
                        if (res.data.status === 202) {
                            _this.dialogFormVisible = false;

                            const newChild = {id: respData.id, label: respData.label, children: []};
                            if (!_this.nodeData.children) {
                                _this.$set(_this.nodeData, 'children', []);
                            }
                            _this.nodeData.children.push(newChild);


                        }
                    })
                console.log(respData);

                const newChild = {id: id++, label: postData, children: []};

                //dialog is true ->
                if (!postData.children) {
                    this.$set(postData, 'children', []);
                }
                postData.children.push(newChild);
            },
        }
    }
</script>

<style scoped>

</style>
