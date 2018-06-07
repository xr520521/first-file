<style lang="less" module >
.container {}
</style>
<template>
  <Modal v-model="showEditModal" :title="title" :loading="loading" @on-ok="handleSubmit" @on-cancel="handleReset">
    <Form ref="formValidate" :model="formValidate" :rules="ruleValidate" :label-width="120">
      <Form-item label="经销商类型" prop="AgentType">
        <Select  v-model="formValidate.TypeID" >
          <Option v-for="(item,index) in AgentType" :key="item.ID" :value="item.ID">{{item.Name}}</Option>
        </Select>
      </Form-item>
      <Form-item label="经销商名称" prop="Name">
        <Input placeholder="请输入经销商姓名" v-model="formValidate.Name"></Input>
      </Form-item>
      <Form-item label="ID" prop="Code">
        <Input placeholder="请输入编码" v-model="formValidate.Code"></Input>
      </Form-item>
      <Form-item label="所属公司" prop="Company">
        <!-- <Input placeholder="请输入所属公司" v-model="formValidate.Company"></Input> -->
        <Select v-model="formValidate.Company" filterable>
          <Option v-for="item in companyList" :value="item.Name" :key="item.Name">{{ item.Name }}</Option>
        </Select>
      </Form-item>
      <Form-item label="联系人" prop="Contacts">
        <Input placeholder="请输入联系人" v-model="formValidate.Contacts"></Input>
      </Form-item>
      <Form-item label="联系电话" prop="Phone">
        <Input placeholder="请输入联系电话" v-model="formValidate.Phone"></Input>
      </Form-item>
      <Form-item label="电子邮箱" prop="Email">
        <Input placeholder="请输入电子邮箱" v-model="formValidate.Email"></Input>
      </Form-item>
      <Form-item label="负责人" prop="ManagerID">
        <Select v-model="formValidate.ManagerID" filterable>
          <Option v-for="item in staffList" :value="item.ID" :key="item.ID">{{ item.Name }}</Option>
        </Select>
      </Form-item>
      <Form-item label="用户名" prop="UserName">
        <Input placeholder="请输入用户名" v-model="formValidate.UserName"></Input>
      </Form-item>
      <Form-item label="密码" prop="Password">
        <Input type="password" placeholder="请输入密码" v-model="formValidate.Password"></Input>
      </Form-item>
      <Form-item label="描述" prop="Description">
        <Input v-model="formValidate.Description" type="textarea" :autosize="{minRows: 2,maxRows: 5}" placeholder="请输入..."></Input>
      </Form-item>
    </Form>
  </Modal>
</template>
<script>
import { AgentDetail, AgentTypeList } from '@/services/agent'
import * as api from '@/services/dept'
import { listCompanys } from '@/services/company'

import { mapActions } from 'vuex'
export default {
  props: {
    value: {
      default: false
    }
  },
  async created() {
    this.getAgentTypeList()
    const { data } = await api.getStaffList()
    this.staffList = data
    const res3 = await listCompanys()
    this.companyList = res3.data
  },
  data() {
    return {
      companyList: [],
      loading: true,
      AgentType: [],
      formValidate: {
        Name: '',
        TypeID: '',
        Code: '',
        Company: '',
        Contacts: '',
        Phone: '',
        UserName: '',
        ManagerID: '',
        Password: '',
        Email: '',
        Description: ''
      },
      ruleValidate: {
        TypeID: [{ required: true, message: '经销商类型不能为空' }],
        Name: [
          { required: true, message: '经销商名称不能为空', trigger: 'blur' },
          { type: 'string', max: 20, message: '不能大于20个字符', trigger: 'blur' }
        ],
        Code: [{ type: 'string', max: 20, message: '不能大于20个字符', trigger: 'blur' }],
        // Company: [{ type: 'string', max: 20, message: '不能大于20个字符', trigger: 'blur' }],
        Contacts: [{ type: 'string', max: 20, message: '不能大于20个字符', trigger: 'blur' }],
        Phone: [
          { type: 'string', message: '联系电话格式不正确', pattern: /^1[3|4|5|7|8][0-9]{9}$/, trigger: 'blur' }
        ],
        Email: [{ type: 'string', max: 20, message: '不能大于20个字符', trigger: 'blur' }],
        Profession: [{ type: 'string', max: 20, message: '不能大于20个字符', trigger: 'blur' }],
        UserName: [
          { type: 'string', max: 20, message: '不能大于20个字符', trigger: 'blur' },
          { type: 'string', min: 6, message: '不能小于6个字符', trigger: 'blur' }
        ],
        Password: [
          { type: 'string', max: 20, message: '不能大于20个字符', trigger: 'blur' },
          { type: 'string', min: 6, message: '不能小于6个字符', trigger: 'blur' }
        ],
        Description: [{ type: 'string', max: 144, message: '不能大于144个字符', trigger: 'blur' }]
      }
    }
  },
  computed: {
    showEditModal: {
      get() {
        return this.$store.state.agent.showEditModal
      },
      set(val) {
        this.update({ key: 'showEditModal', val })
      }
    },
    itemed() {
      return this.$store.state.agent.itemed
    },
    title() {
      return this.itemed === null ? '新增分销商' : '编辑分销商'
    }
  },
  watch: {
    async itemed() {
      if (this.itemed === null) {
        this.formValidate = {
          Name: '',
          TypeID: '',
          Code: '',
          Company: '',
          Contacts: '',
          Phone: '',
          ManagerID: '',
          UserName: '',
          Password: '',
          Email: '',
          Description: ''
        }
      } else {
        const { data } = await AgentDetail(this.itemed)
        this.formValidate = data
        console.log(data.AgentType)
        this.formValidate.TypeID = data.AgentType.m_Item1
        this.formValidate.UserName = data.User.m_Item2
      }
    }
  },
  methods: {
    ...mapActions('agent', ['saveData', 'update']),
    async handleSubmit() {
      this.loading = true
      this.$refs.formValidate.validate(async (valid) => {
        if (valid) {
          // this.$Message.success('提交成功!')
          // this.showModal = false
          const formValidate = { ...this.formValidate }
          delete formValidate.Overdraft
          delete formValidate.Profit
          delete formValidate.Balance
          formValidate.Account = {
            UserName: formValidate.UserName,
            Password: formValidate.Password
          }
          if (!await this.saveData(formValidate)) {
            this.loading = false
            this.$nextTick(() => {
              this.loading = true
            })
          }

          // const methods = this.itemed === null ? AddGuest : ModifyGuest

          // const { status } = await methods(formValidate)

          // // const { status } = await AddGuest(formValidate)
          // if (status === 200) {
          //   this.$Message.success('操作成功')
          //   this.$emit('update')
          // } else {
          //   this.$Message.error('操作失败')
          // }
          // this.handleReset()
        } else {
          this.loading = false
          this.$nextTick(() => {
            this.loading = true
          })
          this.$Message.error('表单验证失败!')
        }
      })
    },
    async getAgentTypeList() {
      const { data } = await AgentTypeList()
      this.AgentType = data
    },
    handleReset() {
      // this.$refs.formValidate.resetFields()
    }
  },
  mounted() {

  }
}
</script>
