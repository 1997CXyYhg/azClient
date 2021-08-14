<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col
              :md="5"
              :sm="16">
              <a-form-item label="公司名称">
                <a-input
                  v-model="queryParam.staffName"
                  placeholder="" />
              </a-form-item>
            </a-col>
            <a-col
              :md="5"
              :sm="16">
              <a-form-item label="经营范围">
                <a-input
                  v-model="queryParam.jobs"
                  placeholder="" />
              </a-form-item>
            </a-col>
            <a-col
              :md="5"
              :sm="16">
              <a-form-item label="产品标签">
                <a-input
                  v-model="queryParam.jobs"
                  placeholder="" />
              </a-form-item>
            </a-col>
            <a-col
              :md="!advanced && 8 || 24"
              :sm="24">
              <span
                class="table-page-search-submitButtons"
                :style="advanced && { float: 'right', overflow: 'hidden' } || {} ">
                <a-button
                  type="primary"
                  @click="$refs.table.refresh(true)">查询</a-button>
                <a-button
                  style="margin-left: 8px"
                  @click="() => this.queryParam = {}">重置</a-button>
              </span>
            </a-col>
          </a-row>
        </a-form>
      </div>

      <div class="table-operator">
        <a-button
          type="primary"
          icon="plus"
          @click="handleAdd">新建</a-button>
        <a-dropdown
          v-action:edit
          v-if="selectedRowKeys.length > 0">
          <a-menu slot="overlay">
            <a-menu-item key="1">
              <a-icon type="delete" />删除</a-menu-item>
            <!-- lock | unlock -->
            <a-menu-item key="2">
              <a-icon type="lock" />锁定</a-menu-item>
          </a-menu>
          <a-button style="margin-left: 8px">
            批量操作
            <a-icon type="down" />
          </a-button>
        </a-dropdown>
      </div>
      <div>
        <s-table
          ref="table"
          size="default"
          rowKey="key"
          :columns="columns"
          :data="loadData"
          :alert="true"
          :rowSelection="rowSelection"
          showPagination="auto"
          :customRow="tableCustomRow">
          <span
            slot="serial"
            slot-scope="text, record, index">
            {{ index + 1 }}
          </span>
          <span
            slot="status"
            slot-scope="text">
            <a-badge
              :status="text | statusTypeFilter"
              :text="text | statusFilter" />
          </span>
          <span
            slot="description"
            slot-scope="text">
            <ellipsis
              :length="4"
              tooltip>{{ text }}</ellipsis>
          </span>

          <span
            slot="action"
            slot-scope="text, record">
            <template>
              <a @click="handleEdit(record)">配置</a>
            </template>
          </span>
        </s-table>
      </div>
      <div style="margin-top: 30px">
        <div class="table-operator">
          <span style="font-size: 20px;font-weight: bold;padding: 10px;padding-right: 30px">联系人</span>
          <a-button
            type="primary"
            icon="plus"
            @click="handleAdd2">新建</a-button>
        </div>
        <a-table
          ref="contactTable"
          size="default"
          rowKey="key"
          :columns="contactColumns"
          :dataSource="contacts"
          :alert="true">
          <span
            slot="serial"
            slot-scope="text, record, index">
            {{ index + 1 }}
          </span>
          <span
            slot="action"
            slot-scope="text, record">
            <template>
              <a @click="handleEdit2(record)">配置</a>
            </template>
          </span>
        </a-table>
      </div>
      <create-form
        ref="createModal"
        :visible="visible"
        :loading="confirmLoading"
        :model="mdl"
        @cancel="handleCancel"
        @ok="handleOk" />
      <contact-form
        ref="contactForm"
        :visible="visible2"
        :loading="confirmLoading2"
        :model="mdl2"
        @cancel="handleCancel2"
        @ok="handleOk2" />
      <step-by-step-modal
        ref="modal"
        @ok="handleOk" />
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getRoleList, getServiceList } from '@/api/manage'

import StepByStepModal from './modules/StepByStepModal'
import CreateForm from './modules/CreateForm'
import ContactForm from '@/views/customer/modules/ContactForm'

const columns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '公司名称',
    dataIndex: 'companyName'
  },
  {
    title: '地址',
    dataIndex: 'address'
  },
  {
    title: '经营范围',
    dataIndex: 'businessScope'
  },
  {
    title: '产品标签',
    dataIndex: 'productTags'
  },
  {
    title: '联系方式',
    dataIndex: 'contactsWay'
  },
  {
    title: '邮箱',
    dataIndex: 'email'
  },
  {
    title: '备注',
    dataIndex: 'note'
  },
  {
    title: '操作',
    dataIndex: 'action',
    width: '150px',
    scopedSlots: { customRender: 'action' }
  }
]

const contactColumns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '联系人',
    dataIndex: 'contactName'
  },
  {
    title: '职位',
    dataIndex: 'jobs'
  },
  {
    title: '性别',
    dataIndex: 'sex'
  },
  {
    title: '工作电话',
    dataIndex: 'contactWay'
  },
  {
    title: '私人电话',
    dataIndex: 'privateContactWay'
  },
  {
    title: '私人地址',
    dataIndex: 'address'
  },
  {
    title: '邮箱',
    dataIndex: 'email'
  },
  {
    title: '喜好',
    dataIndex: 'hobbies'
  },
  {
    title: '备注',
    dataIndex: 'note'
  },
  {
    title: '操作',
    dataIndex: 'action',
    width: '150px',
    scopedSlots: { customRender: 'action' }
  }
]

export default {
  name: 'TableList',
  components: {
    STable,
    Ellipsis,
    CreateForm,
    StepByStepModal,
    ContactForm
  },
  data () {
    this.columns = columns
    this.contactColumns = contactColumns
    return {
      // create model
      visible: false,
      visible2: false,
      confirmLoading: false,
      confirmLoading2: false,
      mdl: null,
      mdl2: null,
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getServiceList(requestParameters).then(res => {
          return {
            data: [
              {
                id: 1,
                companyName: '杭州奥展科技有限公司',
                address: '天目山路',
                businessScope: '设计',
                contactsWay: '1992201131',
                email: '8889132@163.com',
                productTags: '联想、华为、投影仪',
                note: '',
                contacts: [
                  {
                    id: 1,
                    contactName: '张三',
                    sex: '男',
                    contactWay: '19967323015',
                    jobs: '商务经理',
                    address: '上海',
                    email: '99897979@163.com',
                    privateContactWay: '1998811233',
                    hobbies: '运动',
                    note: '',
                    parentId: ''
                  },
                  {
                    id: 2,
                    contactName: '李四',
                    sex: '男',
                    contactWay: '19967323015',
                    jobs: '商务经理',
                    address: '上海',
                    email: '99897979@163.com',
                    privateContactWay: '1998811233',
                    hobbies: '运动',
                    note: '',
                    parentId: ''
                  }
                ]
              },
              {
                id: 2,
                companyName: '杭州奈斯效果科技有限公司',
                address: '滨江',
                businessScope: '设计',
                contactsWay: '1992201131',
                email: '8889132@163.com',
                productTags: '笔记本、幕布',
                note: '',
                contracts: []
              }
            ],
            pageNo: 1,
            pageSize: 10,
            totalCount: 10,
            totalPage: 1
          }
        })
      },
      staffInfo: {},
      selectedRowKeys: [],
      selectedRows: [],
      physicalSurveyCurrRowId: null,
      contacts: [{}]
    }
  },
  created () {
    getRoleList({ t: new Date() })
  },
  computed: {
    rowSelection () {
      return {
        selectedRowKeys: this.selectedRowKeys,
        onChange: this.onSelectChange
      }
    }
  },
  methods: {
    handleAdd () {
      this.mdl = null
      this.visible = true
    },
    handleEdit (record) {
      this.visible = true
      this.mdl = { ...record }
    },
    handleOk () {
      const form = this.$refs.createModal.form
      this.confirmLoading = true
      form.validateFields((errors, values) => {
        if (!errors) {
          console.log('values', values)
          if (values.id > 0) {
            // 修改 e.g.
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.visible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.table.refresh()

              this.$message.info('修改成功')
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.visible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.table.refresh()

              this.$message.info('新增成功')
            })
          }
        } else {
          this.confirmLoading = false
        }
      })
    },
    handleCancel () {
      this.visible = false

      const form = this.$refs.createModal.form
      form.resetFields() // 清理表单数据（可不做）
    },
    handleSub (record) {
      if (record.status !== 0) {
        this.$message.info(`${record.no} 订阅成功`)
      } else {
        this.$message.error(`${record.no} 订阅失败，规则已关闭`)
      }
    },
    onSelectChange (selectedRowKeys, selectedRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectedRows = selectedRows
    },
    toggleAdvanced () {
      this.advanced = !this.advanced
    },
    resetSearchForm () {
      this.queryParam = {
        date: moment(new Date())
      }
    },
    tableCustomRow (record, index) {
      return {
        style: {
          'background-color': record.id === this.physicalSurveyCurrRowId ? '#1890FF' : 'white'
        },
        on: {
          click: event => {
            this.physicalSurveyCurrRowId = record.id
            this.contacts = record.contacts
          }
        }
      }
    },
    handleAdd2 () {
      this.mdl2 = null
      this.visible2 = true
    },
    handleOk2 () {
      const form = this.$refs.contactForm.form
      this.confirmLoading2 = true
      form.validateFields((errors, values) => {
        if (!errors) {
          console.log('values', values)
          if (values.id > 0) {
            // 修改 e.g.
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.visible2 = false
              this.confirmLoading2 = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.table.refresh()

              this.$message.info('修改成功')
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.visible = false
              this.confirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.contactTable.refresh()

              this.$message.info('新增成功')
            })
          }
        } else {
          this.confirmLoading = false
        }
      })
    },
    handleEdit2 (record) {
      this.visible2 = true
      this.mdl2 = { ...record }
    },
    handleCancel2 () {
      this.visible2 = false

      const form = this.$refs.contactForm.form
      form.resetFields() // 清理表单数据（可不做）
    }
  }
}
</script>
