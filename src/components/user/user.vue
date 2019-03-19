<template>
  <div id="user-select">

    <el-form   :model="form1">
      <el-form-item label="用户名">
        <el-input v-model="form1.username"  autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="操作符1">
        <el-select v-model="form1.operator1" placeholder="请选择活动区域">
          <el-option label="OR" value="1"></el-option>
          <el-option label="AND" value="2"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="性别">
        <el-select v-model="form1.sex" placeholder="请选择性别">
          <el-option label="男" value="1"></el-option>
          <el-option label="女" value="0"></el-option>
        </el-select>
      </el-form-item>




      <el-form-item label="操作符2">
        <el-select v-model="form1.operator2" placeholder="请选择活动区域">
          <el-option label="OR" value="1"></el-option>
          <el-option label="AND" value="2"></el-option>
        </el-select>
      </el-form-item>



      <el-form-item label="FROM">
        <el-date-picker v-model="form1.from" type="date" placeholder="选择日期" style="width: 100%;"></el-date-picker>
        <!--<el-input  autocomplete="off"></el-input>-->
      </el-form-item>

      <el-form-item label="TO" >
        <el-date-picker v-model="form1.to" type="date" placeholder="选择日期" style="width: 100%;"></el-date-picker>
        <!--<el-input  autocomplete="off"></el-input>-->
      </el-form-item>


      <el-button @click="selectUser()">查询</el-button>

    </el-form>

    <el-button @click="dialogFormVisible = true">新增用户</el-button>





    <el-table
      :data="tableData"
      border
      style="width: 100%">

      <el-table-column prop="id" label="ID" width="100"></el-table-column>
      <el-table-column prop="username" label="用户名" width="120"></el-table-column>
      <el-table-column prop="sex" :formatter="formatterSex" label="性别" width="120"></el-table-column>
      <el-table-column prop="birthday" label="生日" width="120"></el-table-column>
      <el-table-column prop="address" label="地址" width="120"></el-table-column>
      <el-table-column prop="contact" label="联系电话" width="120"></el-table-column>
      <el-table-column prop="levele" :formatter="formatterLevele" label="等级" width="120"></el-table-column>
      <el-table-column prop="backup" label="备注" width="120"></el-table-column>
      <el-table-column prop="modifiedtime" label="修改日期" width="120"></el-table-column>
      <el-table-column prop="deleted" :formatter="formatterDeleted" label="用户状态" width="120"></el-table-column>

      <el-table-column
        fixed="right"
        label="操作"
        width="200">
        <template slot-scope="scope">
          <el-button type="text" @click="handleEdit(scope.row)" size="small">编辑</el-button>
            <!--<el-button @click.native.prevent="deleteRow(scope.$index, tableData)" type="text" size="small">
              移除
            </el-button>-->
          <el-button type="text" @click="handleDelete(scope.$index, scope.row,tableData)" size="small">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-dialog title="填写节点信息" :visible.sync="dialogFormVisible">

      <el-form :rules="rules"  :model="form">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="form.username"  autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="生日" prop="birthday">
          <el-date-picker v-model="form.birthday" type="date" placeholder="选择日期" style="width: 100%;"></el-date-picker>
          <!--<el-input  autocomplete="off"></el-input>-->
        </el-form-item>
        <el-form-item label="地址" prop="address">
          <el-input v-model="form.address" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="联系电话" prop="contact">
          <el-input v-model="form.contact" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="等级" prop="levele">
          <el-select v-model="form.levele" placeholder="请选择活动区域">
            <el-option label="1级" value="1"></el-option>
            <el-option label="2级" value="2"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="备注" prop="backup">


          <el-input v-model="form.backup" autocomplete="off"></el-input>
        </el-form-item>

      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="()=>this.dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="conformDialog('form')">确 定</el-button>
      </div>
    </el-dialog>





    <!--<el-dialog
      title="提示"
      :visible.sync="dialogVisible2"
      width="30%">
      <span>{{tips}}</span>
      <span slot="footer" class="dialog-footer">
          <el-button @click="cancelPost()">取 消</el-button>
          <el-button type="primary" @click="dialogVisible2 = false">确 定</el-button>
        </span>
    </el-dialog>-->


  </div>

</template>

<script>
  import Qs from 'qs'

  export default {
    name: "user",
    data() {
      let _this = this;
      _this.$axios
        .get('/user/list')
        .then(function (res) {
          if (res.data.status === 202) {
            console.log(res.data.data);
            _this.tableData = res.data.data;

          }
        });
      return {

        errors: [],
        tableData: [],
        tips: '',
        form: {
          id: '',
          username: '',
          sex: '',
          birthday: '',
          address: '',
          contact: '',
          levele: '',
          backup: '',
          modifiedtime: '',
          deleted: '',
        },
        form1: {
          username: '',
          operator1: '',
          sex: '',
          operator2: '',
          from: '',
          to: '',
        },
        rules: {
          username: [
            {required: true, message: '请输入用户名', trigger: 'blur'},
            {min: 2, max: 20, message: '长度在 2 到 20 个字符，无特殊标点符号', trigger: 'blur'}
          ],
          birthday: [
            {required: true,message: '请选择生日',trigger:'blur'},
          ],
          address: [
            {required: true, message: '请输入地址', trigger: 'blur'},
            {min: 3, max: 50, message: '长度在 3 到 50 个字符，无特殊标点符号', trigger: 'blur'}
          ],
          contact: [

            {required: true, message: '请输入电话', trigger: 'blur'},
            {min: 8, max: 20, message: '长度在 8 到 20 个字符', trigger: 'blur'}
          ],
          levele: [
            {required: true, message: '请选择等级', trigger: 'blur'}
          ],
        },
        dialogFormVisible: false,
        dialogVisible2: false,
      }
    },
    methods: {
      selectUser(){
        let _this = this;
        _this.$axios
          .post('/user/selectmuticondication',Qs.stringify(_this.form1))
          .then(function(res){
            if(res.data.status === 202){
              _this.tableData = res.data.data;
            }

          })


        //console.log(this.form1);
      },
      conformDialog(formName){
        console.log(formName)
        let _this = this;
        this.$refs[formName].validate((valid) => {
          if (valid) {
            alert('submit!');
          } else {
            console.log('error submit!!');
            return false;
          }
        });

      },
      handleEdit(row) {
        //打
        this.dialogFormVisible = true;

        this.form.id = row.id;
        this.form.username = row.username;
        this.form.address = row.address;
        this.form.birthday = row.birthday;
        this.form.contact = row.contact;
        this.form.levele = row.levele;
        console.log("editDataOK.....");
      },
      handlePostUser() {
        let _this = this;
        //先关闭dialog
        this.dialogFormVisible = false;
        if (this.form.id === '' || this.form.id === undefined) {
          //handlePostCreate
          //Qs.stringify(form)是一个axios提供的将对象序列化工具
          _this.$axios.post('/user/create', Qs.stringify(_this.form))
            .then(function (res) {
              if(res.data.status === 202 || res.data.status === 200 ) {
                console.log("create ok ....");
              }
            })

        } else {

          _this.axios.post('/user/update',Qs.stringify(_this.form))
            .then(function(res){
              if(res.data.status === 202 || res.data.status === 200 ){
                console.log('update ok ....');
              }
            })
          //handlePostUpdate

        }


      },
      handleDelete(index, row,rows){





        this.$axios.get('/user/delete/'+row.id)

          .then(function(res){
            if(res.data.status === 202 || res.data.status === 200 ){
              console.log('deleted:'+row.id);
              rows.splice(index,1);
            }



          })
      },
      formatterSex(row, column) {
        return row.sex === 1 ? '男' : row.sex === 0 ? '女' : '未知';
      },
      formatterLevele(row) {
        return (row.levele + '级会员');
      },
      formatterDeleted(row) {
        switch (row.deleted) {

          case 0:
            return '正常';
            break;
          case 1:
            return '已删除';
            break;
          default:
            return '状态异常';

        }
      }
    }


  }
</script>

<style scoped>

</style>
