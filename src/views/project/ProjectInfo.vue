<template>
  <page-header-wrapper>
    <a-card :bordered="false" title="详情">
      <div class="table-page-search-wrapper">
        <a-descriptions>
          <a-descriptions-item label="创建时间">
            {{projectInfo.createDate}}
          </a-descriptions-item>
          <a-descriptions-item label="项目名称">
            {{projectInfo.projectName}}
          </a-descriptions-item>
          <a-descriptions-item label="项目类型">
            {{projectInfo.projectType }}
          </a-descriptions-item>
          <a-descriptions-item label="计划交付时间">
            {{projectInfo.completeDate}}
          </a-descriptions-item>
          <a-descriptions-item label="负责人">
            {{projectInfo.staff}}
          </a-descriptions-item>
          <a-descriptions-item label="客户">
            {{projectInfo.customer}}
          </a-descriptions-item>
        </a-descriptions>
      </div>
    </a-card>
    <a-card :bordered="false" title="合同详情">
      <a-row :gutter="24">
        <a-col :md="12">
          <div class="table-operator">
            <span style="margin-right: 20px">项目合同</span>
            <a-button type="primary" icon="plus" @click="memberAdd">新增</a-button>
        </div>
          <div>
            <s-table
              ref="teamTable"
              size="default"
              rowKey="key"
              :columns="contractColumns"
              :data="teamData"
              :alert="true"
            >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
              <span slot="action" slot-scope="text, record">
          <template>
            <a @click="memberEdit(record)">移除</a>
          </template>
        </span>
            </s-table>
          </div>
          <member-form
            ref="memberModal"
            :visible="memberFormVisible"
            :loading="memberFormConfirmLoading"
            :model="memberFormMdl"
            @cancel="memberCancel"
            @ok="memberOk"
          /></a-col>
        <a-col :md="12">
          <div class="table-operator">
            <span style="margin-right: 20px">采购合同</span>
            <a-button type="primary" icon="plus" @click="memberAdd">新增</a-button>
          </div>
          <div>
            <s-table
              ref="teamTable"
              size="default"
              rowKey="key"
              :columns="contractColumns"
              :data="teamData"
              :alert="true"
            >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
              <span slot="action" slot-scope="text, record">
          <template>
            <a @click="memberEdit(record)">移除</a>
          </template>
        </span>
            </s-table>
          </div>
          <member-form
            ref="memberModal"
            :visible="memberFormVisible"
            :loading="memberFormConfirmLoading"
            :model="memberFormMdl"
            @cancel="memberCancel"
            @ok="memberOk"
          />
        </a-col>
      </a-row>
    </a-card>
    <a-card :bordered="false" title="项目进度">
      <div>
        <a-progress :percent="30" />
      </div>
    </a-card>
    <a-card :bordered="false" title="团队成员">
      <div class="table-operator">
        <a-button type="primary" icon="plus" @click="memberAdd">新增</a-button>
      </div>
      <div>
        <s-table
          ref="teamTable"
          size="default"
          rowKey="key"
          :columns="teamColumns"
          :data="teamData"
          :alert="true"
        >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
          <span slot="action" slot-scope="text, record">
          <template>
            <a @click="memberEdit(record)">编辑</a>
            <a-divider type="vertical" />
            <a @click="memberEdit(record)">移除</a>
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
      <member-form
        ref="memberModal"
        :visible="memberFormVisible"
        :loading="memberFormConfirmLoading"
        :model="memberFormMdl"
        @cancel="memberCancel"
        @ok="memberOk"
      />
      <step-by-step-modal ref="modal" @ok="handleOk"/>
    </a-card>
    <a-card :bordered="false" title="采购清单">
      <a-row :gutter="[30, 30]" >
        <a-col :md="12">
          <div class="table-operator">
            <span style="margin-right: 30px;font-size: 18px; font-weight: bold">计划采购清单</span>
            <a-button type="primary" icon="plus" @click="productAdd">新建</a-button>
            <span style="margin-right: 30px;font-size: 18px; font-weight: bold; float: right">总金额: 4999</span>
            <span style="clear: both"></span>
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
              showPagination="auto"
            >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
              <span slot="action" slot-scope="text, record">
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
            @ok="productOk"
          /></a-col>
        <a-col :md="12">
          <div class="table-operator">
            <span style="margin-right: 30px;font-size: 18px; font-weight: bold">项目合同清单</span>
            <a-button type="primary" icon="plus" @click="productAdd">新建</a-button>
            <span style="margin-right: 30px;font-size: 18px; font-weight: bold; float: right">总金额: 4999</span>
            <span style="clear: both"></span>
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
              showPagination="auto"
            >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
              <span slot="action" slot-scope="text, record">
          <template>
            <a @click="handleInfo(record)">合同</a>
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
            @ok="productOk"
          /></a-col>
      </a-row>
      <a-row :gutter="[30, 30]">
        <a-col :md="12">
          <div class="table-operator">
            <span style="margin-right: 30px;font-size: 18px; font-weight: bold">采购清单</span>
            <a-button type="primary" icon="plus" @click="productAdd">新建</a-button>
            <span style="margin-right: 30px;font-size: 18px; font-weight: bold; float: right">总金额: 4999</span>
            <span style="clear: both"></span>
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
              showPagination="auto"
            >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
              <span slot="action" slot-scope="text, record">
          <template>
            <a @click="handleInfo(record)">合同</a>
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
            @ok="productOk"
          /></a-col>
        <a-col :md="12">
          <div class="table-operator">
            <span style="margin-right: 30px;font-size: 18px; font-weight: bold">交付清单</span>
            <a-button type="primary" icon="plus" @click="productAdd">新建</a-button>
            <span style="margin-right: 30px;font-size: 18px; font-weight: bold; float: right">总金额: 4999</span>
            <span style="clear: both"></span>
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
              showPagination="auto"
            >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
              <span slot="action" slot-scope="text, record">
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
            @ok="productOk"
          /></a-col>
      </a-row>
    </a-card>
    <a-card :bordered="false" title="任务管理">
      <div class="table-operator">
        <a-row :gutter="24">
          <a-col :md="8">
            <a-button type="primary" icon="plus" @click="taskAdd">新建</a-button>
          </a-col>
        </a-row>
      </div>
      <div>
        <a-row :gutter="24">
          <a-col :md="10">
            <s-table
              ref="taskTable"
              size="default"
              rowKey="key"
              :columns="taskColumns"
              :data="taskData"
              :alert="true"
              :rowSelection="rowSelection"
              showPagination="auto"
            >
              <span slot="serial" slot-scope="text, record, index">
                {{ index + 1 }}
              </span>
              <span slot="action" slot-scope="text, record">
                <template>
                  <a @click="taskAdd(record)">新建子任务</a>
                  <a-divider type="vertical" />
                  <a @click="taskEdit(record)">编辑</a>
                  <a-divider type="vertical" />
                  <a @click="taskEdit(record)">完成</a>
                  <a-divider type="vertical" />
                  <a @click="taskEdit(record)">删除</a>
                </template>
              </span>
            </s-table>
          </a-col>
          <a-col :md="6">
            <a-descriptions title="任务详情" :column="2">
              <a-descriptions-item label="任务名称">
                {{ staffInfo.entryDate }}
              </a-descriptions-item>
              <a-descriptions-item label="任务目标">
                {{staffInfo.staffName }}
              </a-descriptions-item>
              <a-descriptions-item label="指标">
                {{staffInfo.sex}}
              </a-descriptions-item>
              <a-descriptions-item label="开始日期">
                {{staffInfo.department}}
              </a-descriptions-item>
              <a-descriptions-item label="计划交付日期">
                {{staffInfo.jobs}}
              </a-descriptions-item>
              <a-descriptions-item label="实际完成日期">
                {{staffInfo.contactWay}}
              </a-descriptions-item>
              <a-descriptions-item label="任务所处于节点">
                {{staffInfo.IDCard}}
              </a-descriptions-item>
              <a-descriptions-item label="执行人员">
                {{staffInfo.birthDate}}
              </a-descriptions-item>
              <a-descriptions-item label="任务类型">
                {{staffInfo.emergencyContact}}
              </a-descriptions-item>
              <a-descriptions-item label="状态">
                {{staffInfo.emergencyContactWay}}
              </a-descriptions-item>
              <a-descriptions-item label="完成质量">
                {{staffInfo.spouseName}}
              </a-descriptions-item>
              <a-descriptions-item label="备注">
                {{staffInfo.spouseContactWay}}
              </a-descriptions-item>
            </a-descriptions>
          </a-col>
          <a-col :md="8">
            <s-table
              ref="fileTable"
              size="default"
              rowKey="key"
              :columns="fileColumns"
              :data="teamData"
              :alert="true"
            >
        <span slot="serial" slot-scope="text, record, index">
          {{ index + 1 }}
        </span>
            </s-table>
          </a-col>
        </a-row>
      </div>
      <task-form
        ref="taskModal"
        :visible="taskFormVisible"
        :loading="taskFormConfirmLoading"
        :model="taskFormMdl"
        @cancel="taskCancel"
        @ok="taskOk"
      />
      <div>
        <div style="margin-top: 10px;margin-bottom: 10px">
          <span style="font-weight: bold;font-size: 16px;">任务进度</span>
        </div>
        <div>
          <a-steps :current="current">
            <a-step v-for="item in steps" :key="item.title" :title="item.title" />
          </a-steps>
          <div class="steps-content">
            {{ steps[current].content }}
          </div>
          <div class="steps-action">
            <a-button v-if="current < steps.length - 1" type="primary" @click="next">
              Next
            </a-button>
            <a-button
              v-if="current == steps.length - 1"
              type="primary"
              @click="$message.success('Processing complete!')"
            >
              Done
            </a-button>
            <a-button v-if="current > 0" style="margin-left: 8px" @click="prev">
              Previous
            </a-button>
          </div>
        </div>
      </div>
    </a-card>
  </page-header-wrapper>
</template>

<script>
import { STable, Ellipsis } from '@/components'
import { getRoleList, getServiceList } from '@/api/manage'

import StepByStepModal from './modules/StepByStepModal'
import CreateForm from './modules/CreateForm'
import MemberForm from '@/views/project/modules/MemberForm'
import ProductForm from '@/views/project/modules/ProductForm'
import TaskForm from '@/views/project/modules/TaskForm2'

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
    title: '合同',
    dataIndex: 'contractName'
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

const teamColumns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '姓名',
    dataIndex: 'staffName'
  },
  {
    title: '性别',
    dataIndex: 'sex'
  },
  {
    title: '岗位',
    dataIndex: 'jobs'
  },
  {
    title: '项目职责',
    dataIndex: 'projectDuty'
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

const taskColumns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '任务名称',
    dataIndex: 'taskName'
  },
  {
    title: '状态',
    dataIndex: 'status'
  },
  {
    title: '完成质量',
    dataIndex: 'completeQuality'
  },
  {
    title: '负责人',
    dataIndex: 'staff'
  },
  {
    title: '操作',
    dataIndex: 'action',
    scopedSlots: { customRender: 'action' }
  }
]

const fileColumns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '任务名称',
    dataIndex: 'taskName'
  },
  {
    title: '文件',
    dataIndex: 'file'
  }
]

const contractColumns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '合同名称',
    dataIndex: 'taskName'
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
    MemberForm,
    ProductForm,
    TaskForm
  },
  data () {
    this.productColumns = productColumns
    this.teamColumns = teamColumns
    this.taskColumns = taskColumns
    this.fileColumns = fileColumns
    this.contractColumns = contractColumns
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
      taskFormVisible: false,
      taskFormConfirmLoading: false,
      taskFormMdl: null,
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
                productName: '笔记本电脑',
                brand: '联想',
                options: '',
                unit: '台',
                count: '1',
                price: '4999',
                supplier: '联想',
                type: '预算',
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
                }],
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
        return getServiceList(requestParameters)
          .then(res => {
            return {
              data: [{
                id: 1,
                staffName: '张三',
                sex: '男',
                jobs: '工程师',
                projectDuty: '项目经理'
              }],
              pageNo: 1,
              pageSize: 10,
              totalCount: 10,
              totalPage: 1
            }
          })
      },
      taskData: parameter => {
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getServiceList(requestParameters)
          .then(res => {
            return {
              data: [{
                id: 1,
                taskName: '签订合同',
                status: '进行中',
                completeQuality: '',
                children: [{
                  id: 2,
                  taskName: '协商合同细节',
                  status: '进行中',
                  completeQuality: ''
                }]
              },
              {
                id: 3,
                  taskName: '设备采购',
                status: '进行中',
                completeQuality: '',
                children: [{
                id: 4,
                taskName: '供应商协商',
                status: '进行中',
                completeQuality: ''
              }]
              },
                {
                  id: 5,
                  taskName: '系统研发',
                  status: '进行中',
                  completeQuality: '',
                  children: [{
                    id: 6,
                    taskName: '系统原型设计',
                    status: '进行中',
                    completeQuality: ''
                  }]
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
      projectInfo: {},
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
    this.init()
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
    init () {
      this.projectInfo = this.$route.query.record
    },
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
    taskAdd () {
      this.taskFormMdl = null
      this.taskFormVisible = true
    },
    taskEdit (record) {
      this.taskFormVisible = true
      this.taskFormMdl = { ...record }
    },
    taskOk () {
      const form = this.$refs.taskModal.form
      this.taskFormConfirmLoading = true
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
              this.taskFormVisible = false
              this.taskFormConfirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.taskTable.refresh()

              this.$message.info('修改成功')
            })
          } else {
            // 新增
            new Promise((resolve, reject) => {
              setTimeout(() => {
                resolve()
              }, 1000)
            }).then(res => {
              this.taskFormVisible = false
              this.taskFormConfirmLoading = false
              // 重置表单数据
              form.resetFields()
              // 刷新表格
              this.$refs.taskTable.refresh()

              this.$message.info('新增成功')
            })
          }
        } else {
          this.confirmLoading = false
        }
      })
    },
    taskCancel () {
      this.taskFormVisible = false

      const form = this.$refs.taskModal.form
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
