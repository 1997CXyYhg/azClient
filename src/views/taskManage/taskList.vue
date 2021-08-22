<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="48">
            <a-col
              :md="6"
              :sm="16">
              <a-form-item label="执行人">
                <a-input
                  v-model="queryParam.staffName"
                  placeholder="" />
              </a-form-item>
            </a-col>
            <a-col
              :md="6"
              :sm="16">
              <a-form-item label="任务等级">
                <a-select
                  v-model="queryParam.department"
                  placeholder="请选择"
                  default-value="0">
                  <a-select-option value="0">研发部</a-select-option>
                  <a-select-option value="1">工程部</a-select-option>
                  <a-select-option value="2">GC部</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col
              :md="6"
              :sm="16">
              <a-form-item label="状态">
                <a-input
                  v-model="queryParam.jobs"
                  placeholder="" />
              </a-form-item>
            </a-col>
            <a-col
              :md="6"
              :sm="16">
              <span
                class="table-page-search-submitButtons"
                :style=" { float: 'right', overflow: 'hidden' }">
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
      <div>
        <a-row :gutter="48">
          <a-col
            :md="24"
            :sm="24">
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
              <div
                slot="expandedRowRender"
                slot-scope="record"
                style="margin: 0">
                <p>计划交付日期: {{ record.planCompleteDate }}&nbsp;&nbsp;&nbsp;&nbsp;实际交付日期: {{ record.completeDate }}</p>
                <p>关联项目: {{ record.projectName }}&nbsp;&nbsp;&nbsp;&nbsp;备注: {{ record.noe }}&nbsp;&nbsp;&nbsp;完成质量:{{ record.completeQuality }}</p>
                <p>相关附件: {{ record.fileNames }}</p>
              </div>
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
          </a-col>
        </a-row>
      </div>
      <!-- <create-form
        ref="createModal"
        :visible="visible"
        :loading="confirmLoading"
        :model="mdl"
        @cancel="handleCancel"
        @ok="handleOk" /> -->
      <a-modal
        v-model="showModal"
        title="更新任务"
        :footer="null"
      >
        <a-form
          :form="form"
          :label-col="{ span: 5 }"
          :wrapper-col="{ span: 12 }"
          @submit="handleSubmit">
          <a-form-item label="附件">
            <a-upload-dragger
              action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
              :file-list="fileList"
              :multiple="true"
              @change="handleChange">
              <div v-if="fileList.length < 5">
                <a-icon type="plus" />
                <div class="ant-upload-text">
                  Upload
                </div>
              </div>
            </a-upload-dragger>
          </a-form-item>
          <a-form-item label="执行人" >
            <a-input v-decorator="['staffName', { rules: [{ required: true, message: '请输入执行人' }] }]" />
          </a-form-item>
          <a-form-item label="预计完成时间">
            <!-- <a-input v-decorator="['planCompleteDate', { rules: [{ required: true, message: '请输入预计完成时间' }] }]" /> -->
            <a-date-picker
              type="date"
              :showToday="false"
              placeholder="选择预计完成日期"
              v-decorator="['planCompleteDate', { rules: [{ required: true, message: '请输入预计完成时间' }] }]"
              style="width: 100%;"
            />
          </a-form-item>
          <a-form-item label="状态">
            <!-- <a-input v-decorator="['status', { rules: [{ required: true, message: '请输入状态' }] }]" /> -->
            <a-select
              v-decorator="[
                'status',
                { rules: [{ required: true, message: '请输入状态' }] },
              ]"
              placeholder="请选择状态"
            >
              <a-select-option value="0">
                合格
              </a-select-option>
              <a-select-option value="1">
                良
              </a-select-option>
              <a-select-option value="2">
                优
              </a-select-option>
            </a-select>
          </a-form-item>
          <a-form-item label="完成质量">
            <a-input v-decorator="['completeQuality', { rules: [{ required: true, message: '请输入完成质量' }] }]" />
          </a-form-item>
          <a-form-item label="等级">
            <a-input v-decorator="['level', { rules: [{ required: true, message: '请输入等级' }] }]" />
          </a-form-item>
          <a-form-item :wrapper-col="{ span: 12, offset: 5 }">
            <a-button
              type="primary"
              html-type="submit">
              提交
            </a-button>
          </a-form-item>
        </a-form>
      </a-modal>
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable } from '@/components'
import { getRoleList, getServiceList } from '@/api/manage'
// import { FormModel } from 'ant-design-vue'
// import StepByStepModal from './modules/StepByStepModal'
// import CreateForm from './modules/CreateForm'

const columns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '任务名称',
    dataIndex: 'taskName'
  },
  {
    title: '任务目标',
    dataIndex: 'taskTarget'
    // scopedSlots: { customRender: 'description' }
  },
  {
    title: '开始日期',
    dataIndex: 'beginDate'
  },
  {
    title: '执行人',
    dataIndex: 'staffName'
  },
  {
    title: '父任务',
    dataIndex: 'parentTask'
  },
  {
    title: '等级',
    dataIndex: 'level'
  },
  {
    title: '状态',
    dataIndex: 'status'
  },
  {
    title: '操作人',
    dataIndex: 'execPerson'
  },
  {
    title: '更新时间',
    dataIndex: 'updateTime'
  },
  {
    title: '操作',
    dataIndex: 'action',
    width: '150px',
    scopedSlots: { customRender: 'action' }
  }
]

const statusMap = {
  0: {
    status: 'default',
    text: '关闭'
  },
  1: {
    status: 'processing',
    text: '运行中'
  },
  2: {
    status: 'success',
    text: '已上线'
  },
  3: {
    status: 'error',
    text: '异常'
  }
}

export default {
  name: 'TableList',
  components: {
    STable
    // FormModel
    // Ellipsis,
    // CreateForm,
    // StepByStepModal
  },
  data () {
    this.columns = columns
    return {
      // create model
      // visible: false,
      form: this.$form.createForm(this, { name: 'coordinated' }),
      fileList: [],
      confirmLoading: false,
      showModal: false,
      updateRecordData: {
        taskName: '任务名称',
        taskTarget: '任务目标',
        beginDate: '开始日期',
        staffName: '执行人',
        parentTask: '父任务',
        level: '等级',
        status: '进行中、审核中、完成',
        execPerson: '执行人',
        updateTime: '更新时间',
        planCompleteDate: '计划交付日期（',
        completeDate: '实际完成日期',
        projectId: 'projectId',
        projectName: 'projectName',
        fileId: 'fileId',
        fileNames: '附件。。',
        fileList: [],
        completeQuality: '完成质量'
      },
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
                taskName: '任务名称',
                taskTarget: '任务目标',
                beginDate: '开始日期',
                staffName: '执行人',
                parentTask: '父任务',
                level: '等级',
                status: '进行中、审核中、完成',
                execPerson: '操作人',
                updateTime: '更新时间',
                planCompleteDate: '计划交付日期（',
                completeDate: '实际完成日期',
                projectId: 'projectId',
                projectName: 'projectName',
                fileId: 'fileId',
                fileNames: '附件。。',
                completeQuality: '完成质量',
                note: 'notenote'
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
      physicalSurveyCurrRowId: null
    }
  },
  filters: {
    statusFilter (type) {
      return statusMap[type].text
    },
    statusTypeFilter (type) {
      return statusMap[type].status
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
    handleSubmit (e) {
      e.preventDefault()
      this.form.validateFields((err, values) => {
        if (!err) {
          console.log('Received values of form: ', values, this.fileList.name)
          this.showModal = false
        }
      })
    },
    handleChange ({ fileList }) {
      this.fileList = fileList
    },
    handleUpdate (updateRecordData) {
      this.showModal = false
      console.log(JSON.stringify(updateRecordData))
    },
    handleAdd () {
      this.updateRecordData = null
      this.visible = true
    },
    handleEdit (record) {
      this.visible = true
      this.showModal = true
      this.updateRecordData = { ...record }
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
            this.staffInfo = record
          }
        }
      }
    }
  }
}
</script>
