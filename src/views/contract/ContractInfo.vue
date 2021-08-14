<template>
  <page-header-wrapper>
    <a-card
      :bordered="false"
      title="详情">
      <div class="table-page-search-wrapper">
        <a-descriptions>
          <a-descriptions-item label="创建时间">
            {{ contractInfo.createDate }}
          </a-descriptions-item>
          <a-descriptions-item label="合同名称">
            {{ contractInfo.contractName }}
          </a-descriptions-item>
          <a-descriptions-item label="合同类型">
            {{ contractInfo.contractType }}
          </a-descriptions-item>
          <a-descriptions-item label="甲方">
            {{ contractInfo.firstParty }}
          </a-descriptions-item>
          <a-descriptions-item label="甲方负责人">
            {{ contractInfo.firstPartyPerson }}
          </a-descriptions-item>
          <a-descriptions-item label="甲方联系方式">
            {{ contractInfo.firstPartyContactWay }}
          </a-descriptions-item>
          <a-descriptions-item label="乙方">
            {{ contractInfo.secondParty }}
          </a-descriptions-item>
          <a-descriptions-item label="乙方负责人">
            {{ contractInfo.secondPartyPerson }}
          </a-descriptions-item>
          <a-descriptions-item label="乙方联系人">
            {{ contractInfo.secondPartyContactWay }}
          </a-descriptions-item>
          <a-descriptions-item label="签订日期">
            {{ contractInfo.signDate }}
          </a-descriptions-item>
          <a-descriptions-item label="交付日期">
            {{ contractInfo.deliveryDate }}
          </a-descriptions-item>
          <a-descriptions-item label="合同总金额">
            200000
          </a-descriptions-item>
          <a-descriptions-item label="违约责任">
            {{ contractInfo.breachLiability }}
          </a-descriptions-item>
        </a-descriptions>
        <a-button
          type="primary"
          @click="handleEdit">编辑</a-button>
        <create-form
          ref="createModal"
          :visible="visible"
          :loading="confirmLoading"
          :model="mdl"
          @cancel="handleCancel"
          @ok="handleOk" />
      </div>
    </a-card>
    <a-card
      :bordered="false"
      title="附件">
      <div class="table-operator">
        <a-row :gutter="24">
          <a-col :md="8">
            <a-button
              type="primary"
              icon="plus"
              @click="incomeExpensesAdd">新建</a-button>
          </a-col>
        </a-row>
      </div>
      <div>
        <s-table
          ref="incomeExpensesTable"
          size="default"
          rowKey="key"
          :columns="fileColumns"
          :data="incomeExpensesData"
          :alert="true"
          :rowSelection="rowSelection"
          showPagination="auto">
          <span
            slot="serial"
            slot-scope="text, record, index">
            {{ index + 1 }}
          </span>
          <span
            slot="action"
            slot-scope="text, record">
            <template>
              <a
                @click="incomeExpensesEdit(record)"
                v-if="!record.parentId">下载</a>
              <a-divider type="vertical" />
              <a
                @click="incomeExpensesEdit(record)"
                v-if="!record.parentId">删除</a>
            </template>
          </span>
        </s-table>
      </div>
      <income-expenses-form
        ref="incomeExpensesModal"
        :visible="incomeExpensesFormVisible"
        :loading="incomeExpensesFormConfirmLoading"
        :model="incomeExpensesFormMdl"
        @cancel="incomeExpensesCancel"
        @ok="incomeExpensesOk" />
      <step-by-step-modal
        ref="modal"
        @ok="handleOk" />
    </a-card>
    <a-card
      :bordered="false"
      title="采购清单"
      v-show="contractInfo.contractType === '采购合同'">
      <div class="table-operator">
        <a-row :gutter="24">
          <a-col :md="8">
            <a-button
              type="primary"
              icon="plus"
              @click="productAdd">新建</a-button>
          </a-col>
          <a-col :md="8">
          </a-col>
          <a-col :md="8">
            <span style="margin-right: 30px;font-size: 18px; font-weight: bold; float: right">总金额: 4999</span>
          </a-col>
        </a-row>
      </div>
      <div>
        <s-table
          ref="productTable"
          size="default"
          rowKey="key"
          :columns="productColumns"
          :data="loadData"
          :alert="true"
          :rowSelection="rowSelection"
          showPagination="auto">
          <span
            slot="serial"
            slot-scope="text, record, index">
            {{ index + 1 }}
          </span>
          <span
            slot="action"
            slot-scope="text, record">
            <template>
              <a @click="productEdit(record)">配置</a>
              <a-divider type="vertical" />
              <a @click="handleEdit(record)">移除</a>
            </template>
          </span>
        </s-table>
      </div>
      <product-form
        ref="productModal"
        :visible="productFormVisible"
        :loading="productFormConfirmLoading"
        :model="productFormMdl"
        @cancel="productCancel"
        @ok="productOk" />
      <step-by-step-modal
        ref="modal"
        @ok="handleOk" />
    </a-card>
    <a-card
      :bordered="false"
      title="收支列表">
      <div class="table-operator">
        <a-row :gutter="24">
          <a-col :md="8">
            <a-button
              type="primary"
              icon="plus"
              @click="incomeExpensesAdd">新建</a-button>
          </a-col>
        </a-row>
      </div>
      <div>
        <s-table
          ref="incomeExpensesTable"
          size="default"
          rowKey="key"
          :columns="incomeExpensesColumns"
          :data="incomeExpensesData"
          :alert="true"
          :rowSelection="rowSelection"
          showPagination="auto">
          <span
            slot="serial"
            slot-scope="text, record, index">
            {{ index + 1 }}
          </span>
          <span
            slot="action"
            slot-scope="text, record">
            <template>
              <a
                @click="incomeExpensesEdit(record)"
                v-if="!record.parentId">编辑</a>
              <a-divider type="vertical" />
              <a
                @click="incomeExpensesEdit(record)"
                v-if="!record.parentId">删除</a>
            </template>
          </span>
        </s-table>
      </div>
      <income-expenses-form
        ref="incomeExpensesModal"
        :visible="incomeExpensesFormVisible"
        :loading="incomeExpensesFormConfirmLoading"
        :model="incomeExpensesFormMdl"
        @cancel="incomeExpensesCancel"
        @ok="incomeExpensesOk" />
      <step-by-step-modal
        ref="modal"
        @ok="handleOk" />
    </a-card>
  </page-header-wrapper>
</template>

<script>
import { STable, Ellipsis } from '@/components'
import { getRoleList, getServiceList } from '@/api/manage'

import StepByStepModal from './modules/StepByStepModal'
import CreateForm from './modules/CreateForm'
import MemberForm from '@/views/contract/modules/MemberForm'
import ProductForm from '@/views/contract/modules/ProductForm'
import IncomeExpensesForm from '@/views/contract/modules/IncomeExpensesForm'

const productColumns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '名称',
    dataIndex: 'productName'
  },
  {
    title: '品牌',
    dataIndex: 'brand'
  },
  {
    title: '参数',
    dataIndex: 'options'
  },
  {
    title: '单位',
    dataIndex: 'unit'
  },
  {
    title: '数量',
    dataIndex: 'count'
  },
  {
    title: '单价',
    dataIndex: 'price'
  },
  {
    title: '供应商',
    dataIndex: 'supplier'
  },
  {
    title: '类型',
    dataIndex: 'type'
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

const fileColumns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '文件名称',
    dataIndex: 'staffName'
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

const incomeExpensesColumns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '支付阶段',
    dataIndex: 'payMoment'
  },
  {
    title: '金额',
    dataIndex: 'amount'
  },
  {
    title: '经办人',
    dataIndex: 'handler'
  },
  {
    title: '类型',
    dataIndex: 'type'
  },
  {
    title: '开户行',
    dataIndex: 'bankName'
  },
  {
    title: '开户名',
    dataIndex: 'bandAccountName'
  },
  {
    title: '银行账号',
    dataIndex: 'bankAccount'
  },
  {
    title: '备注',
    dataIndex: 'note'
  },
  {
    title: '附件',
    dataIndex: 'file'
  },
  {
    title: '操作',
    dataIndex: 'action',
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
    MemberForm,
    ProductForm,
    IncomeExpensesForm
  },
  data () {
    this.productColumns = productColumns
    this.fileColumns = fileColumns
    this.incomeExpensesColumns = incomeExpensesColumns
    return {
      // create model
      visible: false,
      confirmLoading: false,
      mdl: null,
      // 团队
      memberFormVisible: false,
      memberFormConfirmLoading: false,
      memberFormMdl: null,
      // 采购清单
      productFormVisible: false,
      productFormConfirmLoading: false,
      productFormMdl: null,
      // 任务管理
      incomeExpensesFormVisible: false,
      incomeExpensesFormConfirmLoading: false,
      incomeExpensesFormMdl: null,
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
                productName: '笔记本电脑',
                brand: '联想',
                options: '',
                unit: '台',
                count: '1',
                price: '4999',
                supplier: '联想',
                type: '实际',
                note: ''
              },
              {
                id: 1,
                productName: '笔记本电脑',
                brand: '联想',
                options: '',
                unit: '台',
                count: '1',
                price: '4500',
                supplier: '联想',
                type: '实际',
                contractName: '合同1',
                note: ''
              }
            ],
            pageNo: 1,
            pageSize: 10,
            totalCount: 10,
            totalPage: 1
          }
        })
      },
      teamData: parameter => {
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getServiceList(requestParameters).then(res => {
          return {
            data: [
              {
                id: 1,
                staffName: '张三',
                sex: '男',
                jobs: '工程师',
                projectDuty: '项目经理'
              }
            ],
            pageNo: 1,
            pageSize: 10,
            totalCount: 10,
            totalPage: 1
          }
        })
      },
      incomeExpensesData: parameter => {
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getServiceList(requestParameters).then(res => {
          return {
            data: [
              {
                id: 1,
                payMoment: '预付款',
                amount: '50000',
                handler: '',
                type: '计划',
                bankName: '',
                bandAccountName: '',
                bankAccount: '',
                note: '',
                file: [],
                children: [
                  {
                    id: 2,
                    payMoment: '预付款',
                    amount: '30000',
                    handler: '张三',
                    type: '实际',
                    bankName: '中国银行',
                    bandAccountName: '张三',
                    bankAccount: '112315690080802342343',
                    note: '',
                    file: [],
                    parentId: 1
                  },
                  {
                    id: 3,
                    payMoment: '预付款',
                    amount: '20000',
                    handler: '张三',
                    type: '实际',
                    bankName: '中国银行',
                    bandAccountName: '张三',
                    bankAccount: '112315690080802342343',
                    note: '',
                    file: [],
                    parentId: 1
                  }
                ]
              },
              {
                id: 4,
                payMoment: '设备款',
                amount: '50000',
                handler: '',
                type: '计划',
                bankName: '',
                bandAccountName: '',
                bankAccount: '',
                note: '',
                file: [],
                children: [
                  {
                    id: 5,
                    payMoment: '设备款',
                    amount: '30000',
                    handler: '张三',
                    type: '实际',
                    bankName: '中国银行',
                    bandAccountName: '张三',
                    bankAccount: '112315690080802342343',
                    note: '',
                    file: [],
                    parentId: 4
                  },
                  {
                    id: 6,
                    payMoment: '设备款',
                    amount: '20000',
                    handler: '张三',
                    type: '实际',
                    bankName: '中国银行',
                    bandAccountName: '张三',
                    bankAccount: '112315690080802342343',
                    note: '',
                    file: [],
                    parentId: 4
                  }
                ]
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
      contractInfo: {
        createDate: '2021-01-01',
        contractName: 'xxx合同',
        contractType: '采购合同',
        firstParty: '西安xxx有限责任公司',
        firstPartyPerson: 'xxx',
        firstPartyContactWay: '112312441',
        secondParty: '杭州xxx有限责任公司',
        secondPartyPerson: 'xxx',
        secondPartyContactWay: '1102931123',
        signDate: '2021-01-01',
        deliveryDate: '2021-07-01',
        breachLiability:
          '违约责任 是指在当事人不履行 合同债务 时，所应承担的 民事责任 。. 想要明确违约责任，首先要知道违约责任的归责原则是什么，因此，明确违约责任的归责原则具有十分重要的意义，下面， 律图 为您介绍合同违约责任的归责原则。. 一、 过错责任. 过错责任，是指由于当事人主观上的故意或者过失而引起的违约责任。. 在发生违约事实的情况下，只有当事人有过错，才能承担违约责任，否则，将不承担违约责任。. 过错责任原则 包含下列两个方面的内容：. ①违约责任由有过错的当事人承担。. 一方合同当事人有过错的，由该方自己承担;双方都有过错的，由双方分别承担。. 例如，在来料 加工合同 中，定作人提供的材料质量不合要求，要承担违约责任。.\n' +
          '\n',
        file: []
      },
      current: 0,
      steps: [
        {
          title: '签订合同',
          content: '由xxx签订合同，跟进项目需求。'
        },
        {
          title: '设备采购',
          content: '由xxx同事拟定采购计划，和相关供应商询价，确认供应商。'
        },
        {
          title: '系统开发',
          content: '由xxx同事作为项目经理，负责xxx系统的研发。'
        }
      ]
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
    onSelectChange (selectedRowKeys, selectedRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectedRows = selectedRows
    },
    handleInfo () {},
    next () {
      this.current++
    },
    prev () {
      this.current--
    },
    // 团队成员
    memberAdd () {
      this.memberFormMdl = null
      this.memberFormVisible = true
    },
    memberEdit (record) {
      this.memberFormVisible = true
      this.memberFormMdl = { ...record }
    },
    memberOk () {
      const form = this.$refs.memberModal.form
      this.memberFormConfirmLoading = true
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
              this.memberFormVisible = false
              this.memberFormConfirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.teamTable.refresh()

              this.$message.info('修改成功')
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.memberFormVisible = false
              this.memberFormConfirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.teamTable.refresh()

              this.$message.info('新增成功')
            })
          }
        } else {
          this.confirmLoading = false
        }
      })
    },
    memberCancel () {
      this.memberFormVisible = false

      const form = this.$refs.memberModal.form
      form.resetFields() // 清理表单数据（可不做）
    },
    // 采购清单
    productAdd () {
      this.productFormMdl = null
      this.productFormVisible = true
    },
    productEdit (record) {
      this.productFormVisible = true
      this.productFormMdl = { ...record }
    },
    productOk () {
      const form = this.$refs.productModal.form
      this.productFormConfirmLoading = true
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
              this.productFormVisible = false
              this.productFormConfirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.productTable.refresh()

              this.$message.info('修改成功')
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.productFormVisible = false
              this.productFormConfirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.productTable.refresh()

              this.$message.info('新增成功')
            })
          }
        } else {
          this.confirmLoading = false
        }
      })
    },
    productCancel () {
      this.productFormVisible = false

      const form = this.$refs.productModal.form
      form.resetFields() // 清理表单数据（可不做）
    },
    // 任务管理
    incomeExpensesAdd () {
      this.incomeExpensesFormMdl = null
      this.incomeExpensesFormVisible = true
    },
    incomeExpensesEdit (record) {
      this.incomeExpensesFormVisible = true
      this.incomeExpensesFormMdl = { ...record }
    },
    incomeExpensesOk () {
      const form = this.$refs.incomeExpensesModal.form
      this.incomeExpensesFormConfirmLoading = true
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
              this.incomeExpensesFormVisible = false
              this.incomeExpensesFormConfirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.incomeExpensesTable.refresh()

              this.$message.info('修改成功')
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.incomeExpensesFormVisible = false
              this.incomeExpensesFormConfirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.incomeExpensesTable.refresh()

              this.$message.info('新增成功')
            })
          }
        } else {
          this.confirmLoading = false
        }
      })
    },
    incomeExpensesCancel () {
      this.incomeExpensesFormVisible = false

      const form = this.$refs.incomeExpensesModal.form
      form.resetFields() // 清理表单数据（可不做）
    }
  }
}
</script>
<style scoped>
.steps-content {
  margin-top: 16px;
  border: 1px dashed #e9e9e9;
  border-radius: 6px;
  background-color: #fafafa;
  min-height: 200px;
  text-align: center;
  padding-top: 80px;
}

.steps-action {
  margin-top: 24px;
}
</style>
