<template>
  <div>
    <vxe-toolbar>
      <template v-slot:buttons>
    <el-button-group style="float: left; margin:10px">
      <!-- <el-button type="primary" icon="el-icon-circle-plus-outline" size="small" @click="$refs.editable.insertAt({name: `New last ${Date.now()}`, flag: true, createDate: Date.now()}, -1)">新增</el-button> -->
      <el-button type="primary" icon="el-icon-circle-plus-outline" size="small" @click.native="dialogFormVisible = true">添加m</el-button>
      <el-button type="info" size="small" @click="$refs.editable.revert()">放弃更改</el-button>
      <el-button type="info" size="small" icon="el-icon-delete" @click="$refs.editable.clear()">清空数据</el-button>
    </el-button-group>
    <el-button-group  style="float: right; margin:10px">
      <el-button type="primary" icon="el-icon-circle-plus-outline" size="small">搜索</el-button>
      <el-button type="primary" icon="el-icon-refresh" size="small">刷新</el-button>
    </el-button-group>
    
    <!-- <el-col> -->
      <el-button-group style="float: left; margin:10px">
        <!-- <template> -->
          <el-select v-model="value" placeholder="请选择workflow" @change="changeValue" clearable>
            <el-option
              v-for="item in workflowdata.workflows"
              :key="item.ID"
              :label="item.Name"
              :value="item.ID">
            </el-option>
          </el-select>
        <!-- </template> -->
      </el-button-group>
    <!-- </el-col> -->
    </template>
    </vxe-toolbar>

    <vxe-table ref="xTable"
      :data.sync="nodedata" border style="width: 100%" stripe :edit-config="{trigger: 'click', mode: 'cell'}"
      @edit-actived="editActivedEvent"
      @edit-closed="editClosedEvent">
      <!-- <el-table-column label="ID" prop="ID" align="center"></el-table-column> -->
      <vxe-table-column title="序号" type="index" show-overflow-tooltip width="50"  align="center">
      </vxe-table-column>
      <vxe-table-column title="NAME" field="Name" :edit-render="{name: 'input'}" align="center">
      </vxe-table-column> 
      <vxe-table-column field="DocType" title="DOCTYPE" :editRender="{type: 'default'}" align="center">
        <template slot="edit" slot-scope="scope">
          <el-select v-model="scope.row.DocType" clearable>
            <el-option
              v-for="item in doctypedata.doctypes"
              :key="item.ID"
              :label="item.Name"
              :value="item.ID">
            </el-option>
          </el-select>
        </template>
        <template slot-scope="scope">{{ getColumnLabel(scope.row.DocType) }}</template>
      </vxe-table-column>
      <vxe-table-column field="DocState" title="DOCSTATE" :editRender="{type: 'default'}" align="center">
        <template slot="edit" slot-scope="scope">
          <el-select v-model="scope.row.DocState" clearable>
            <el-option
              v-for="item in docstatedata.docstates"
              :key="item.ID"
              :label="item.Name"
              :value="item.ID">
            </el-option>
          </el-select>
        </template>
        <template slot-scope="scope">{{ getColumnLabel2(scope.row.DocState) }}</template>
      </vxe-table-column>

      <!-- 因为acid这个无法显示，所以不能在行中添加或修改！！！<vxe-table-column field="AccCtx" title="ACCESSCONTEXT" :editRender="{type: 'default'}" align="center">
        <template slot="edit" slot-scope="scope">
          <el-select v-model="scope.row.AccCtx" clearable>
            <el-option
              v-for="item in accesscontextdata"
              :key="item.ID"
              :label="item.Name"
              :value="item.ID">
            </el-option>
          </el-select>
        </template>
        <template slot-scope="scope">{{ getColumnLabel3(scope.row.AccCtx) }}</template>
      </vxe-table-column> -->

      <vxe-table-column field="NodeType" title="NODETYPE" :editRender="{type: 'default'}" align="center">
        <template slot="edit" slot-scope="scope">
          <el-select v-model="scope.row.NodeType" clearable>
            <el-option
              v-for="item in nodetypedata"
              :key="item.ID"
              :label="item.Name"
              :value="item.Value"><!-- 传值 -->
            </el-option>
          </el-select>
        </template>
        <!-- <template slot-scope="scope">{{ getColumnLabel4(scope.row.NodeType,nodetypedata) }}</template> -->
      </vxe-table-column>
      <vxe-table-column title="操作" align="center">
        <template slot-scope="scope">
          <el-button-group>
            <el-button size="mini" @click="handleSubmit(scope.$index, scope.row)">Save</el-button>
            <el-button size="mini" type="danger" @click="deleteRow(scope.$index, nodedata)">Delete</el-button>
          </el-button-group>
        </template>
      </vxe-table-column>
    </vxe-table>
    <el-pagination background
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage"
      :page-sizes="[10, 50, 100, 200]"
      :page-size="pageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="nodedata.total" style="float: right; margin:10px">
    </el-pagination>

    <el-dialog title="定义work_node" :visible.sync="dialogFormVisible" center>
    <!-- 插入测试 -->
      <el-form :model="ruleForm2" status-icon :rules="rules2" ref="ruleForm2" label-width="100px" class="demo-ruleForm">
        <el-form-item label="名称" prop="nodename">
          <el-input v-model.number="ruleForm2.nodename" auto-complete="off" placeholder="输入名称" style='width: 215px;'></el-input>
          <!-- <el-form-item label="TITLE" prop="docname" class="transparentIcon" style='width: 320px;'> -->
        </el-form-item>
        <el-form-item label="DocType" prop="dtid">
          <el-select v-model.number="ruleForm2.dtid" clearable>
            <el-option
              v-for="item in doctypedata.doctypes"
              :key="item.ID"
              :label="item.Name"
              :value="item.ID">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="DocState" prop="dsid">
          <el-select v-model.number="ruleForm2.dsid" clearable>
            <el-option
              v-for="item in docstatedata.docstates"
              :key="item.ID"
              :label="item.Name"
              :value="item.ID">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="AccCtx" prop="acid">
          <el-select v-model.number="ruleForm2.acid" clearable>
            <el-option
              v-for="item in accesscontextdata.accesscontexts"
              :key="item.ID"
              :label="item.Name"
              :value="item.ID">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="NodeType" prop="nodetype">
          <el-select v-model.number="ruleForm2.nodetype" clearable>
            <el-option
              v-for="item in nodetypedata"
              :key="item.ID"
              :label="item.Name"
              :value="item.Name">
            </el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <!-- 插入测试 -->
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false; resetForm('ruleForm2')">取 消</el-button>
        <el-button type="primary" @click="submitForm('ruleForm2')">确 定</el-button>
      </div>
    </el-dialog>

  </div>
</template>

<script type="text/javascript">
  /* eslint-disable */
  import XEUtils from 'xe-utils'
  const axios = require('axios');
  export default { // 这里需要将模块引出，可在其他地方使用
    name: 'node',
      data() {
        var checkName = (rule, value, callback) => {
          if (value === '') {
            callback(new Error('请输入账号'));
          } else {
            callback();
          }
        };
        var checkNum = (rule, value, callback) => {
          if (!value) {
            return callback(new Error('账号不能为空'));
          }
          setTimeout(() => {
            if (!Number.isInteger(value)) {
              callback(new Error('请输入数字值'));
            } else {
              var myreg=/^[1][3,4,5,7,8][0-9]{9}$/;
              if (!myreg.test(value) ) {
                callback(new Error('请输入正确的手机号码'));
              } else {
                callback();
              }
            }
          }, 1000);
        };
        var validatePass = (rule, value, callback) => {
          if (value === '') {
            callback(new Error('请输入密码'));
          } else {
            callback();
          }
        };
        return {
          total: 50,
          currentPage: 1,
          pageSize: 10,
          loginPower:false,
          checked: true,
          /*插入form方法*/
          /*设定规则指向*/
          ruleForm2: {
            name:'',
            pass: '',
            num: '',
            delivery: false,
          },
          rules2: {
            typename: [
              { validator: checkName, trigger: 'blur' }
            ],
            pass: [
              { validator: validatePass, trigger: 'blur' }
            ],
            num: [
              { validator: checkNum, trigger: 'blur' }
            ]
          },
          /*插入form方法*/
          dialogTableVisible: false,
          dialogFormVisible: false,
          form: {
            name: '',
            type: [],
            resource: '',
            desc: ''
          },
          formLabelWidth: '120px',
          isCollapse: true,
          bannerHeight:200,
          clientHeight:'',
          posts:[],
          numbers:0,
          // dialogFormVisible: false,
          form: {
            name: '',
            region: '',
            date1: '',
            date2: '',
            delivery: false,
            type: [],
            resource: '',
            desc: ''
          },
          formLabelWidth: '120px',
          workflowdata: [
            {
              ID:1,
              Name:'测试数据'
            }
          ],      
          nodedata: [
            {
              ID:1,
              DocType:3,
              DocState:7,
              // AccCtx:4,
              // Wflow:1,
              Name:'node测试数据',
              NodeType:'begin',
              Workflow: 3
            }
          ],
          doctypedata: {
            doctypes: [
              {
                ID: 3,
                Name: "图纸设计"
              }
            ],
            page: 1,
            total: 7
          },
          docstatedata: [
            {
              ID:3,
              Name:'设计中……'
            },
            {
              ID:5,
              Name:'校核中……'
            }
          ],
          accesscontextdata: [
            {
              ID:4,
              Name:'提交设计'
            }
          ],
          nodetypedata: [
            {
              ID:1,
              Name:'begin',
              Value:'begin'
            },
            {
              ID:2,
              Name:'end',
              Value:'end'
            },
            {
              ID:3,
              Name:'linear',
              Value:'linear'
            },
            {
              ID:4,
              Name:'branch',
              Value:'branch'
            },
            {
              ID:5,
              Name:'joinany',
              Value:'joinany'
            },
            {
              ID:6,
              Name:'joinall',
              Value:'joinall'
            },
          ],
          search: '',
          value:'',
          workflowid:1
        };
      },
      mounted:function () {
        this.workflow(this.currentPage);
        this.node(this.currentPage);
        this.doctype(this.currentPage);
        this.docstate(this.currentPage);
        this.accesscontext(this.currentPage);
      },
      methods:{
        submitForm(formName) {
              axios({
                // headers: {
                //   'X-Requested-With': 'XMLHttpRequest',
                //   'Content-Type': 'application/json; charset=UTF-8',
                //   'Access-Control-Allow-Origin': '*'
                // },//设置跨域请求头
                method: "POST",//请求方式
                url: "/flownode",//请求地址
                params:{
                  name:this.ruleForm2.nodename,
                  dtid:this.ruleForm2.dtid,
                  dsid:this.ruleForm2.dsid,
                  acid:this.ruleForm2.acid,
                  nodetype:this.ruleForm2.nodetype,
                },
                // data: {
                //   name:this.ruleForm2.typename,
                // }
              })
              .then((response) => {
                if (response != "err") {
                  // this.$Message.info('用户名或密码错误，请送心')
                  //提交成功做的动作
                  this.$message({
                    type: 'success',
                    message: '提交成功' 
                  });
                  //刷新表格
                  // this.docaction(currentPage);
                  this.dialogFormVisible = false;
                } else {
                  // console.log(response.data)
                  //写入失败！
                  this.$message.error('写入失败！');
                }
              })
              .catch(function (error) {
                console.log(error);
              });
        },

        resetForm(formName) {
          this.$refs[formName].resetFields();
        },

        changeWidthL(key, keyPath) {
          console.log(key, keyPath);
        },

        handleOpen(key, keyPath) {
          console.log(key, keyPath);
        },
        handleClose(key, keyPath) {
          console.log(key, keyPath);
        },
        workflow(currentPage){
          axios({
            method: 'get',
            url: '/flowworkflowlist',//2.get通过params选项
          })
          .then(response => (this.workflowdata = response.data))
          .catch(function (error) {
            console.log(error);
          });
        },
        node(currentPage){
          axios({
            method: 'get',
            url: '/flownodelist',//2.get通过params选项
            params:{
              page:currentPage,
              workflowid:this.workflowid
            }
          })
          .then(response => (this.nodedata = response.data))
          .catch(function (error) {
            console.log(error);
          });
        },
        doctype(currentPage){
          axios({
            method: 'get',
            url: '/flowtypelist',//2.get通过params选项
            // params:{
            //   page:currentPage
            // }
          })
          .then(response => (this.doctypedata = response.data))
          .catch(function (error) {
            console.log(error);
          });
        },
        docstate(currentPage){
          axios({
            method: 'get',
            url: '/flowstatelist',//2.get通过params选项
            // params:{
            //   page:currentPage
            // }
          })
          .then(response => (this.docstatedata = response.data))
          .catch(function (error) {
            console.log(error);
          });
        },
        accesscontext(currentPage){
          axios({
            method: 'get',
            url: '/flowaccesscontextlist',//2.get通过params选项
            // params:{
            //   page:currentPage
            // }
          })
          .then(response => (this.accesscontextdata = response.data))
          .catch(function (error) {
            console.log(error);
          });
        },
        deleteRow(index, rows) {//删除改行
          //先删除数据库

          //再删除前端行
          rows.splice(index, 1);
        },
        addRow(nodedata,event){
          nodedata.push({ ID:'',})
        },
        //用这个
        handleSubmit(index, row) {
          console.log(row);
              axios({
                method: "POST",//请求方式
                url: "/flownode",//请求地址
                params:{
                  name:row.Name,
                  dtid:row.DocType,
                  dsid:row.DocState,
                  acid:row.AccessContext,
                  nodetype:row.NodeType
                },
              })
              .then((response) => {
                if (response != "err") {
                  // this.$Message.info('用户名或密码错误，请送心')
                  //提交成功做的动作
                  this.$message({
                    type: 'success',
                    message: '提交成功' 
                  });
                  //刷新表格
                  this.node(this.currentPage);
                  this.dialogFormVisible = false;
                } else {
                  // console.log(response.data)
                  //写入失败！
                  this.$message.error('写入失败！');
                }
              })
              .catch(function (error) {
                console.log(error);
              });
        },
        sendGetByStr(){
          //1.get通过直接发字符串拼接
          axios.get(`api/v1/wx/getlistarticles?page=1`)
            // .then(function (response) {
            //   console.log(response);
            //   console.log(response.data.info);
            // }
            .then(response => (this.info = response.data.info)
            )
            .catch(function (error) {
              console.log(error);
            });
        },
        //html剔除富文本标签，留下纯文本
        getSimpleText(html){
          var re1 = new RegExp("<.+?>","g");//匹配html标签的正则表达式，"g"是搜索匹配多个符合的内容
          var msg = html.replace(re1,'');//执行替换成空字符
          return '<br>'+msg.substring(0,20);
        },
        even: function (numbers) {
          // console.log(numbers);
          // console.log(numbers % 2 === 0);
          // return numbers.filter(function (number) {
            return numbers % 2 === 0
          // })
        },
        even1: function (numbers) {
          // console.log(numbers);
          // console.log(numbers % 2 === 0);
          // return numbers.filter(function (number) {
            return numbers === 0
          // })
        },
        changeCollapse:function(isCollapse){
          if (isCollapse==true) {
            this.isCollapse=false
          }else{
            this.isCollapse=true
          }
        },
        skip(a){
          this.$router.push(a)
        },
        jump(select){
          console.log(select);
          let routeUrl = this.$router.resolve({
            path: "/onlyoffice",
            // query: {id:96}
          });
          switch (select) {
            case ("onlyoffice"):
              routeUrl = this.$router.resolve({
                path: "/onlyoffice",
                // query: {id:96}
              });
              window.open(routeUrl .href);
              break;
            case ("project"):
              routeUrl = this.$router.resolve({
                path: "/project",
                // query: {id:96}
              });
              window.open(routeUrl .href);
              break;
            case ("design"):
              routeUrl = this.$router.resolve({
                path: "/design",
                // query: {id:96}
              });
              window.open(routeUrl .href);
              break;
            default:
              routeUrl = this.$router.resolve({
                path: "/index",
                // query: {id:96}
              });
              window.open(routeUrl .href);
              break;
          }
          //this.$router.push({path: '/cart?goodsId=12'})
          //this.$router.go(-2)
          //后退两步
          // let routeUrl = this.$router.resolve({
          //   path: "/onlyoffice",
          //   query: {id:96}
          // });
          // window.open(routeUrl .href, '_blank');
          // window.open(routeUrl .href);
        },
        toNext: function(index) {
          sessionStorage.ticketName =this.sortList[index].list;
          this.$router.push('/mine/tiketOrder');
        },
        handleSizeChange: function (size) {
          this.pageSize = size;
        },
        handleCurrentChange: function(currentPage){
          this.currentPage = currentPage;
          this.node(currentPage);
        },
        getColumnLabel (value) {
          // console.log(this.doctypedata.doctypes)
          let selectItem = this.doctypedata.doctypes.find(item => item.ID === value)
          return selectItem ? selectItem.Name : null
        },
        getColumnLabel2 (value) {
          console.log(value)
          let selectItem = this.docstatedata.docstates.find(item => item.ID === value)
          return selectItem ? selectItem.Name : null
        },
        getColumnLabel3 (value) {
          let selectItem = this.accesscontextdata.accesscontext.find(item => item.ID === value)
          return selectItem ? selectItem.Name : null
        },
        getColumnLabel4 (value, list, valueProp = 'Value', labelField = 'Name') {
          let item = XEUtils.find(list, item => item[valueProp] === value)
              return item ? item[labelField] : null
        },
        changeValue(value) {
          // console.log(value);
          this.workflowid = value; 
          this.node(this.currentPage);
          // let obj = {};
          // obj = this.options.find((item)=>{
          //     return item.value === value;
          // });
          // console.log(obj.label);
        },
        editActivedEvent ({ row, column }, event) {
          console.log(`打开 ${column.title} 列编辑`)
        },
        editClosedEvent ({ row, column }, event) {
          console.log(`关闭 ${column.title} 列编辑`)
        },
        insertEvent (row) {
          let record = {
            sex: '1'
          }
          this.$refs.xTable.insertAt(record, row)
            .then(({ row }) => this.$refs.xTable.setActiveCell(row, 'sex'))
        }
      }
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #vue{
    color: green;
    font-size: 28px;
  }
  .con_section{
    position: absolute;
    top: 0px;
    bottom: 0px;
    left:0px;
    width:100%;
  }
  .blueheader {
    height: 60px;
    line-height: 60px;
    background: #67c23a;
    color: #fff;
  }
  .el-menu-item.is-active {
      color: #67c23a;
  }
  .headlogo{
    float: left;
    height: 60px;
    margin: 0 20px;
    width: 300px;
  }
  ul.el-menu {
    background: #e4e8f1;
  }
  .userinfo{
    position: absolute;
    right: 0;
  }
  .el-submenu__title{
    background:#eef1f6;
  }
  .el_main{
    padding:0px;
  }
  .home_main{
    padding:10px;
  }
  .breadcrumb-container .title {
      width: 200px;
      float: left;
      color: #475669;
    font-size: 13px;
    }
  .breadcrumb-inner {
      float: right;
      font-size: 13px;
  }
  .el-breadcrumb__inner, .el-breadcrumb__inner a {
    font-weight: 400;
  }
</style>
