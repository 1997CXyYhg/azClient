<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col :md="5" :sm="16">
              <a-form-item label="收件人">
                <a-input v-model="queryParam.brand" placeholder=""/>
              </a-form-item>
            </a-col>
            <a-col :md="5" :sm="16">
              <a-form-item label="发件人">
                <a-input v-model="queryParam.productName" placeholder=""/>
              </a-form-item>
            </a-col>
            <a-col :md="!advanced && 8 || 24" :sm="24">
              <span class="table-page-search-submitButtons" :style="advanced && { float: 'right', overflow: 'hidden' } || {} ">
                <a-button type="primary" @click="$refs.table.refresh(true)">查询</a-button>
                <a-button style="margin-left: 8px" @click="() => this.queryParam = {}">重置</a-button>
              </span>
            </a-col>
          </a-row>
        </a-form>
      </div>

      <div class="table-operator">
        <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
        <a-dropdown v-action:edit v-if="selectedRowKeys.length > 0">
          <a-menu slot="overlay">
            <a-menu-item key="1"><a-icon type="delete" />删除</a-menu-item>
            <!-- lock | unlock -->
            <a-menu-item key="2"><a-icon type="lock" />锁定</a-menu-item>
          </a-menu>
          <a-button style="margin-left: 8px">
            批量操作 <a-icon type="down" />
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
          >
      <span slot="serial" slot-scope="text, record, index">
        {{ index + 1 }}
      </span>
            <span slot="action" slot-scope="text, record">
        <template>
          <a @click="handleEdit(record)">配置</a>
        </template>
      </span>
          </s-table>
      </div>
      <create-form
        ref="createModal"
        :visible="visible"
        :loading="confirmLoading"
        :model="mdl"
        @cancel="handleCancel"
        @ok="handleOk"
      />
      <step-by-step-modal ref="modal" @ok="handleOk"/>
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getRoleList, getServiceList } from '@/api/manage'

import StepByStepModal from './modules/StepByStepModal'
import CreateForm from './modules/CreateForm'

const columns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '发件日期',
    dataIndex: 'senderDate'
  },
  {
    title: '收件人',
    dataIndex: 'receiver'
  },
  {
    title: '收件人联系方式',
    dataIndex: 'receiverContactWay'
  },
  {
    title: '收件地址',
    dataIndex: 'receiverAddress'
  },
  {
    title: '签收人',
    dataIndex: 'signer'
  },
  {
    title: '签收日期',
    dataIndex: 'receiveDate'
  },
  {
    title: '快递公司',
    dataIndex: 'expressCompany'
  },
  {
    title: '快递单号',
    dataIndex: 'trackingNumber'
  },
  {
    title: '快递内容',
    dataIndex: 'trackingContent'
  },
  {
    title: '发件人',
    dataIndex: 'sender'
  },
  {
    title: '发件人联系方式',
    dataIndex: 'senderContactWay'
  },
  {
    title: '附件',
    dataIndex: 'file'
  },
  {
    title: '备注（note）',
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
    StepByStepModal
  },
  data () {
    this.columns = columns
    return {
      // create model
      visible: false,
      confirmLoading: false,
      mdl: null,
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getServiceList(requestParameters)
          .then(res => {
            return {
              data: [{
                id: 1,
                senderDate: '2021-7-27',
                receiver: '张三',
                receiverContactWay: '19967323015',
                receiverAddress: '杭州滨江',
                signer: '李四',
                receiveDate: '2021-7-31',
                expressCompany: '顺丰',
                trackingNumber: 'SF21231313213134',
                trackingContent: '合同',
                sender: '王五',
                senderContactWay: '1770526804',
                file: '',
                note: ''
              }],
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
      physicalSurveyCurrRowId: null
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
    }
  }
}
</script>
