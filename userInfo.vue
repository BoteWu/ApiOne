<template>
  <div>
    <div>
      <Row type="flex" style="margin-top: -15px;">
        <i-col span="4">
          <Input v-model="iUserCode" placeholder="账号" clearable style="width: 200px" />
        </i-col>
        <i-col span="4">
          <Input v-model="iUserName" placeholder="姓名" clearable style="width: 200px" />
        </i-col>
        <i-col span="4">
          <Input v-model="iMobile" placeholder="手机号" clearable style="width: 200px" />
        </i-col>
        <i-col span="4">
          <Input v-model="iBuild" placeholder="创建人" clearable style="width: 200px" />
        </i-col>
        <i-col span="4">
          <Select v-model="iRode" clearable style="width:200px" placeholder="角色">
            <Option v-for="item in cityList" :value="item.value" :key="item.value">{{ item.label }}</Option>
          </Select>
        </i-col>
        <i-col span="4">
          <Select v-model="iStatus" clearable style="width:200px" placeholder="允许登录">
            <Option v-for="item in cityList1" :value="item.value" :key="item.value">{{ item.label }}</Option>
          </Select>
        </i-col>
      </Row>
      <Row type="flex" style="margin-top: 5px; margin-bottom: 5px;">
        <i-col span="4">
          <Select v-model="iCompany" clearable style="width:200px" placeholder="公司">
            <Option v-for="item in cityList1" :value="item.value" :key="item.value">{{ item.label }}</Option>
          </Select>
        </i-col>
        <i-col span="4">
          <Button type="primary" icon="ios-search">查询</Button>&nbsp;&nbsp;&nbsp;&nbsp;
          <!-- <Button type="error" icon="md-add" shape="circle" @click="add_user">新增</Button> -->
        </i-col>
        <i-col span="4"></i-col>
        <i-col span="4"></i-col>
        <i-col span="4"></i-col>
        <i-col span="4" style="float:right">
          <i-button type="primary" icon="ios-add" @click="add_user">添加</i-button>&nbsp;&nbsp;&nbsp;&nbsp;
          <!-- <i-button type="warning" icon="ios-download-outline">导出</i-button> -->
          <Button icon="md-download"
                  :loading="exportLoading"
                  type="warning"
                  @click="exportExcel">
            导出文件
          </Button>
        </i-col>
      </Row>
    </div>
    <div>
      <Table border :columns="columns7" :data="usersList"></Table>
    </div>
    <div style="float: right; margin-top: 10px;">
      <Page :total="total" :page-size="num" show-elevator @on-change="handleChange" />
    </div>
    <div>{{$t('test_message')}}</div>
    <Modal title="编辑用户"
           v-model="modal10"
           class-name="vertical-center-modal"
           @on-ok="ok"
           slot="header"
           width="600px">
      <!-- <div slot="header" style="background:#307ECC; color: #fff;">
        <span>编辑用户</span>
      </div>-->
      <div class="table-sty">
        <span class="title">账号:</span>
        <Input class="box" type="text" disabled v-model="userCode" />
      </div>
      <div class="table-sty">
        <span class="title">姓名:</span>
        <Input class="box" type="text" v-model="userName" />
      </div>
      <div class="table-sty">
        <span class="title">性别:</span>
        <i-select v-model="sex" class="box">
          <i-option v-for="item in sexList" :value="item.code" :key="item.name">{{ item.name }}</i-option>
        </i-select>
      </div>
      <div class="table-sty">
        <span class="title">公司:</span>
        <i-select v-model="company" class="box">
          <i-option v-for="item in companyCode" :value="item.code" :key="item.name">{{ item.name }}</i-option>
        </i-select>
      </div>
      <div class="table-sty">
        <span class="title">角色:</span>
        <i-select v-model="roleName" class="box">
          <i-option v-for="item in roleNameCode" :value="item.code" :key="item.name">{{ item.name }}</i-option>
        </i-select>
      </div>
      <div class="table-sty">
        <span class="title">允许登录:</span>
        <i-select v-model="userStatus" class="box">
          <i-option v-for="item in userStatusData"
                    :value="item.code"
                    :key="item.code">{{ item.value }}</i-option>
        </i-select>
      </div>
      <div class="table-sty">
        <span class="title">手机号:</span>
        <Input class="box" type="text" v-model="mobile" />
      </div>
      <div class="table-sty">
        <span class="title">邮箱:</span>
        <Input class="box" type="text" v-model="Email" />
      </div>
      <div class="table-sty">
        <span class="title">身份证:</span>
        <Input class="box" type="text" v-model="IDCardNO" />
      </div>
      <div class="table-sty">
        <span class="title">国家:</span>
        <i-select v-model="country" class="box">
          <i-option value="China">中国</i-option>
        </i-select>
      </div>
      <div class="table-sty">
        <span class="title">省份:</span>
        <i-select v-model="province" class="box" @on-change="changePro()">
          <i-option v-for="(item,index) in proData"
                    :value="item.code+index"
                    :key="index">{{item.name}}</i-option>
        </i-select>
      </div>
      <div class="table-sty">
        <span class="title">城市:</span>
        <i-select v-model="city" class="box" @on-change="changeCity()">
          <i-option v-for="(item,index) in cityData"
                    :value="item.code+index"
                    :key="index">{{item.name}}</i-option>
        </i-select>
      </div>
      <div class="table-sty">
        <span class="title">区域:</span>
        <i-select v-model="area" class="box">
          <i-option v-for="item in areaData" :value="item.code" :key="item.code">{{item.name}}</i-option>
        </i-select>
      </div>
    </Modal>
    <Modal v-model="modalDel" width="360" class-name="vertical-center-modal">
      <p slot="header" style="color:#fff;text-align:center">
        <Icon type="ios-information-circle"></Icon>
        <span>系统提示</span>
      </p>
      <div style="text-align:center">
        <p style="font-size:13px">
          <Icon type="ios-information-circle" size="20" color="red"></Icon>
          注：您确定要重置该项账户的密码吗？
        </p>
      </div>
      <div slot="footer">
        <Button type="primary" size="large" long :loading="modal_loading" @click="reset">重置</Button>
      </div>
    </Modal>
    <Modal v-model="modalStatus" width="360" class-name="vertical-center-modal">
      <p slot="header" style="color:#fff;text-align:center">
        <Icon type="ios-information-circle"></Icon>
        <span>系统提示</span>
      </p>
      <div style="text-align:center">
        <p style="font-size:13px">
          <Icon type="ios-information-circle" size="20" color="red"></Icon>
          {{statusTextList[statusTextListIndex]}}
        </p>
      </div>
      <div slot="footer">
        <Button type="primary" size="large" long :loading="modal_loading" @click="userStatusControl">确定</Button>
      </div>
    </Modal>
  </div>
</template>

<script>
  import { getUserList } from "@/api/user";
  import excel from "@/libs/excel";
  const proData = require("../../assets/data/area.json");
  export default {
    name: "userManage",
    // computed: {
    //   content () {
    //     return this.$t('content')
    //   }
    // },
    data() {
      return {
        // 下拉框数据
        model8: "",
        cityList: [
          {
            value: "New York",
            label: "New York"
          },
          {
            value: "London",
            label: "London"
          },
          {
            value: "Sydney",
            label: "Sydney"
          },
          {
            value: "Ottawa",
            label: "Ottawa"
          },
          {
            value: "Paris",
            label: "Paris"
          },
          {
            value: "Canberra",
            label: "Canberra"
          }
        ],
        cityList1: [
          {
            value: "New York",
            label: "New York"
          },
          {
            value: "London",
            label: "London"
          },
          {
            value: 1,
            label: "Sydney"
          },
          {
            value: "Ottawa",
            label: "Ottawa"
          },
          {
            value: "Paris",
            label: "Paris"
          },
          {
            value: "Canberra",
            label: "Canberra"
          }
        ],
        columns7: [
          // table列名
          {
            title: "序号",
            type: "index",
            width: 60,
            align: "center"
          },
          {
            title: "账号",
            key: "userCode"
          },
          {
            title: "姓名",
            key: "userName"
            // render: (h, params) => {
            //   return h("div", [
            //     h("Icon", {
            //       props: {
            //         type: "person"
            //       }
            //     }),
            //     h("strong", params.row.name)
            //   ]);
            // }
          },
          { title: "手机号", key: "mobile" },
          { title: "区域", key: "areaShow" },
          { title: "性别", key: "sex", width: 70, align: "center" },
          {
            title: "允许登录",
            // key:"userStatus",
            width: 100,
            align: "center",
            render: (h, params) => {
              let color = "";
              let lock = "";
              let lock_icon_text = "";
              if (params.row.userStatus == "正常") {
                color = "#82AF6F";
                lock = "md-headset";
                lock_icon_text = "启用";
              } else {
                color = "#F89406";
                lock = "ios-lock-outline";
                lock_icon_text = "禁止";
              }
              return h("div", [
                h(
                  "span",
                  {
                    style: {
                      // display: "block",
                      color: "#fff",
                      padding: "4px 8px",
                      background: color
                    }
                  },
                  params.row.userStatus
                )
              ]);
            }
          },
          { title: "公司", key: "companyName" },
          { title: "角色", key: "roleName" },
          { title: "创建时间", key: "updateTime" },
          { title: "创建人", key: "updateBy" },
          {
            title: "操作",
            // key: "sex",
            width: 330,
            ellipsis: false,
            tooltip: true,
            align: "center",
            render: (h, params) => {
              let lock = "";
              let text = "";
              let isShow = "";
              if (params.row.userStatus == "正常") {
                lock = "ios-key";
                text = "启用";
              } else {
                lock = "ios-unlock";
                text = "禁止";
              }
              if (params.row.mobile) {
                isShow = "inline";
              } else {
                isShow = "none";
              }
              return h("div", [
                // 编辑
                h(
                  "Icon", //<Icon type="md-create" />
                  {
                    props: {
                      type: "md-create",
                      size: 12
                    },
                    style: {
                      color: "#69AA46!important"
                    },
                    on: {
                      click: () => {
                        this.Edit(params.index);
                      }
                    }
                  }
                  // "view"
                  // params.row.sex
                ),
                h(
                  "Span", //<Icon type="md-create" />
                  {
                    style: {
                      color: "#69AA46!important",
                      marginRight: "8px"
                    },
                    on: {
                      click: () => {
                        this.Edit(params.index);
                      }
                    }
                  },
                  "编辑"
                ),
                // 短信管理
                h(
                  "Icon", //<Icon type="md-create" />
                  {
                    props: {
                      type: "md-mail",
                      size: 12
                    },
                    style: {
                      color: "#69AA46!important",
                      display: isShow
                    },
                    on: {
                      click: () => {
                        this.show(params.index);
                      }
                    }
                  }
                  // "view"
                  // params.row.sex
                ),
                h(
                  "Span", //<Icon type="md-create" />
                  {
                    style: {
                      color: "#69AA46!important",
                      marginRight: "8px",
                      display: isShow
                    },
                    on: {
                      click: () => {
                        this.show(params.index);
                      }
                    }
                  },
                  "短信管理"
                ),
                // 重置密码
                h(
                  "Icon",
                  {
                    props: {
                      type: "md-refresh"
                    },
                    style: {
                      color: "#69AA46!important"
                    },
                    on: {
                      click: () => {
                        this.resetPsw(params.index);
                      }
                    }
                  }
                ),
                h(
                  "Span",
                  {
                    style: {
                      color: "#69AA46!important",
                      marginRight: "8px"
                      // background: "#69AA46!important"
                    },
                    on: {
                      click: () => {
                        this.resetPsw(params.index);
                      }
                    }
                  },
                  "重置密码"
                ),
                // 启用or禁止
                h("Icon", {
                  props: {
                    type: lock,
                    size: 12
                  },
                  style: {
                    color: "#ff0000"
                  },
                  on: {
                    click: () => {
                      this.userOpener(params.index);
                    }
                  }
                }),
                h(
                  "Span",
                  {
                    style: {
                      color: "#ff0000",
                      marginRight: "8px"
                    },
                    on: {
                      click: () => {
                        this.userOpener(params.index);
                      }
                    }
                  },
                  text
                ),
                // 权限分配
                // 重置密码
                h(
                  "Icon",
                  {
                    props: {
                      type: "ios-build"
                    },
                    style: {
                      color: "#69AA46!important"
                    },
                    on: {
                      click: () => {
                        this.remove(params.index);
                      }
                    }
                  },
                  "编辑"
                ),
                h(
                  "Span",
                  {
                    style: {
                      color: "#69AA46!important",
                      marginRight: "8px"
                      // background: "#69AA46!important"
                    },
                    on: {
                      click: () => {
                        this.remove(params.index);
                      }
                    }
                  },
                  "权限分配"
                )
              ]);
            }
          }
        ],
        // table 数据
        usersList: [],
        iUserCode: "",
        iUserName: "",
        iMobile: "",
        iBuild: "",
        iRode: "",
        iStatus: "New York",
        iCompany: 1,
        exportLoading: false,
        // 分页器
        total: 100,
        num: 20,
        // 点击编辑弹出层
        modal10: false,
        sexList: [
          { code: 1, name: "男" },
          { code: 0, name: "女" }
        ],
        userCode: "",
        userName: "",
        sex: "",
        company: "",
        companyCode: [
          { code: 0, name: "深圳祁飞科技" },
          { code: 1, name: "马大哈有限公司" },
          { code: 2, name: "美丽成长有限公司" }
        ],
        roleName: 0,
        roleNameCode: [
          { code: 0, name: "国务院总统" },
          { code: 1, name: "俄罗斯高级特务" }
        ],
        userStatus: 0,
        userStatusData: [
          { code: 1, value: "是" },
          { code: 0, value: "否" }
        ],
        mobile: "",
        Email: "",
        IDCardNO: "",
        country: "China",
        province: "",
        city: "",
        area: "",
        cityData: {},
        areaData: {},
        proData: {},
        // 重置密码弹层
        modalDel: false,
        modal_loading: false,
        // 开启or禁用账户
        modalStatus: false,
        statusTextList: [
          '注：您确定要禁用该账户吗？',
          '注：您确定要启用该账户吗？'
        ],
        statusTextListIndex: ''
      };
    },
    methods: {
      handleChange() {
        //切换页码
      },
      // 点击table按钮
      show(index) {
        this.$Modal.info({
          title: "User Info",
          content: `Name：${this.usersList[index].userName}<br>Sex：${this.usersList[index].sex}<br>角色:${this.usersList[index].roleName}`
        });
      },
      remove(index) {
        this.usersList.splice(index, 1);
      },
      // 编辑
      Edit(index) {
        let userInfo = this.usersList[index];

        this.modal10 = true;
        this.userCode = userInfo.userCode;
        this.userName = userInfo.userName;
        if (userInfo.sex == "男") {
          this.sex = 1;
        } else {
          this.sex = 0;
        }
        this.roleName = userInfo.roleName;
        if (userInfo.userStatus == "正常") {
          this.userStatus = 1;
        } else {
          this.userStatus = 0;
        }
        this.Email = userInfo.Email;
        this.IDCardNO = userInfo.IDCardNO;
      },
      // 弹出层按钮
      ok() {
        this.$Message.info("Clicked ok");
      },
      // 新增用户
      add_user() {
        this.modal10 = true;
      },
      // 分页查询user信息
      onLoadUserList() {
        getUserList({
          currentCompanyCode: "qf",
          pageIndex: 1,
          pageSize: 14
        })
          .then(res => {
            console.log(res.data.datas);
            // 处理sex，userStatus,updateTime
            for (let i = 0; i < res.data.datas.length; i++) {
              res.data.datas[i].updateTime = res.data.datas[
                i
              ].updateTime.substring(0, 10);
              if (res.data.datas[i].sex == 1) {
                res.data.datas[i].sex = "男";
              } else {
                res.data.datas[i].sex = "女";
              }
              if (res.data.datas[i].userStatus == 1) {
                res.data.datas[i].userStatus = "正常";
              } else {
                res.data.datas[i].userStatus = "禁止";
              }
            }
            this.usersList = res.data.datas;
          })
          .catch(err => {
            reject(err);
          });
      },
      // 改变省option
      changePro() {
        this.city = null;
        this.cityData = null;
        this.area = null;
        this.areaData = null;
        let i = this.province.substring(6);
        i = parseInt(i);
        console.log(i, this.proData);
        this.cityData = this.proData[i].city;
      },
      // 改变城市option
      changeCity() {
        this.area = null;
        this.areaData = null;
        let i = this.city.substring(6);
        i = parseInt(i);
        console.log(i, this.cityData);
        this.areaData = this.cityData[i].area;
      },
      // 导出文件
      exportExcel() {
        if (this.usersList.length) {
          this.exportLoading = true;
          const params = {
            title: [
              "账号",
              "姓名",
              "手机号",
              "邮箱",
              "性别",
              "区域",
              "允许登录",
              "公司",
              "创建时间",
              "创建人"
            ],
            key: [
              "userCode",
              "userName",
              "mobile",
              "email",
              "sex",
              "region",
              "userStatus",
              "companyName",
              "updateTime",
              "updateBy"
            ],
            data: this.usersList,
            autoWidth: true,
            filename: "用户信息"
          };
          excel.export_array_to_excel(params);
          this.exportLoading = false;
        } else {
          this.$Message.info("表格数据不能为空！");
        }
      },
      // 确认密码重置
      reset() {
        this.modal_loading = true;
        setTimeout(() => {
          this.modal_loading = false;
          this.modalDel = false;
          this.$Message.success("密码重置成功！");
        }, 2000);
      },
      // 密码重置
      resetPsw(index) {
        this.modalDel = true
      },
      // 开启or禁止用户
      userOpener(index) {
        this.modalStatus = true
        if (this.usersList[index].userStatus == "正常") {
          this.statusTextListIndex = 1
        } else {
          this.statusTextListIndex = 0
        }
      },
      // 确认开启or禁止用户
      userStatusControl() {
        this.modal_loading = true;
        setTimeout(() => {
          this.modal_loading = false;
          this.modalStatus = false;
          this.$Message.success("操作成功！");
        }, 2000);
      }
    },
    mounted: function () {
      this.onLoadUserList();
      this.proData = proData;
      // console.log(this.$store.state.token,"这里打印是否有效");
    }
  };
</script>
<style lang="less">

  .fillter-mar {
    margin-right: 20px;
  }

  .ivu-modal-header-inner {
    color: #fff;
  }

  .ivu-modal-header {
    background: #307ecc;
  }

  .ivu-icon-ios-close:before {
    color: #fff;
  }

  .ivu-modal-footer {
    background: #eff3f8;
  }

  /*// .ivu-btn-text {
    // background: #87b87f !important;
    // color: #fff;
    //
  }*/

  .vertical-center-modal {
    /* 弹出层 */
    display: flex;
    align-items: center;
    justify-content: center;
    .ivu-modal

  {
    top: 0;
  }

  }

  .table-sty {
    display: -webkit-box; /* Chrome 4+, Safari 3.1, iOS Safari 3.2+ */
    display: -moz-box; /* Firefox 17- */
    display: -webkit-flex; /* Chrome 21+, Safari 6.1+, iOS Safari 7+, Opera 15/16 */
    display: -moz-flex; /* Firefox 18+ */
    display: -ms-flexbox; /* IE 10 */
    display: flex; /* Chrome 29+, Firefox 22+, IE 11+, Opera 12.1/17/18, Android 4.4+ */
    // -webkit-box-pack: justify;
    // -ms-flex-pack: justify;
    // -webkit-justify-content: space-between;
    // justify-content: space-between;
    -webkit-box-align: center;
    -ms-flex-align: center;
    -webkit-align-items: center;
    align-items: center;
    margin-bottom: 10px;
    .title

  {
    font-size: 12px;
    font-weight: 400;
    width: 50px;
    // 如若弹出层列无法对其，此处值曾大即可 padding: 9px 5px 9px 0;
  }

  .box {
    height: 30px;
    padding: 2px 5px;
    width: 500px;
  }
  }
</style>



<!--//获取用户列表
export const getUserList = ({ currentCompanyCode, pageIndex, pageSize }) => {
  const data = {
    currentCompanyCode,
    pageIndex,
    pageSize
  }
  return axios.request({
    url: '/api/app/user/ByPage',
    method: 'post',
    data
    // contentType: 'application/json; charset=UTF-8'
  })
}-->
