<template>
  <page-header-wrapper>
    <a-card :bordered="false" title="权限配置">
      <a-row :gutter="24">
        <a-col :md="12">
          <a-card title="角色">
            <div class="table-operator">
              <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
            </div>
            <a-list item-layout="horizontal" :data-source="data">
              <a-list-item slot="renderItem" slot-scope="item">
                <a-list-item-meta>
                  <a slot="title" href="https://www.antdv.com/">{{ item.title }}</a>
                </a-list-item-meta>
              </a-list-item>
            </a-list>
          </a-card>
        </a-col>
        <a-col :md="12">
          <a-card title="权限">
            <div class="table-operator">
              <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
            </div>
            <a-tree
              v-model="checkedKeys"
              checkable
              :expanded-keys="expandedKeys"
              :auto-expand-parent="autoExpandParent"
              :selected-keys="selectedKeys"
              :tree-data="treeData"
            />
          </a-card>
        </a-col>
      </a-row>

    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getRoleList, getServiceList } from '@/api/manage'
import StepByStepModal from './modules/StepByStepModal'
import CreateForm from './modules/CreateForm'
import STree from '@/components/Tree/Tree'

const roleColumns = [
  {
    title: '#',
    scopedSlots: { customRender: 'serial' }
  },
  {
    title: '名称',
    dataIndex: 'roleName'
  },
  {
    title: '权限',
    dataIndex: 'permission'
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

const treeData = [
  {
    title: '员工管理',
    key: '0-0',
    children: [
      {
        title: '员工信息',
        key: '0-0-0',
        children: [
          { title: '新增', key: '0-0-0-0' },
          { title: '删除', key: '0-0-0-1' }
        ]
      }
    ]
  },
  {
    title: '客户管理',
    key: '0-0',
    children: [
      {
        title: '客户信息',
        key: '0-0-0',
        children: [
          { title: '新增', key: '0-0-0-0' },
          { title: '删除', key: '0-0-0-1' }
        ]
      }
    ]
  }
]

const data = [
  {
    title: '工程师'
  },
  {
    title: '会计'
  },
  {
    title: '总经理'
  },
  {
    title: '项目经理'
  }
]
export default {
  name: 'TableList',
  components: {
    STable,
    Ellipsis,
    CreateForm,
    StepByStepModal,
    STree
  },
  data () {
    this.roleColumns = roleColumns
    this.treeData = treeData
    this.data = data
    return {
      expandedKeys: ['0-0-0', '0-0-1'],
      autoExpandParent: true,
      checkedKeys: ['0-0-0'],
      selectedKeys: [],
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
                createDate: '2020-10-01',
                roleName: '张三',
                permission: '男',
                department: '研发部',
                jobs: '工程师',
                IDCard: '330382199701100337',
                birthDate: '1997-01-01',
                departureDate: '',
                emergencyContact: '李四',
                emergencyContactPhone: '19967323015',
                contactWay: '17705266804',
                spouseName: '无',
                spouseContactWay: '',
                fatherName: '',
                fatherNameContactWay: '',
                motherName: '',
                motherContactWay: '',
                hobbies: '',
                note: ''
              },
                {
                  id: 2,
                  entryDate: '2020-10-01',
                  staffName: '李四',
                  sex: '男',
                  department: '研发部',
                  jobs: '工程师',
                  IDCard: '330382199701100337',
                  birthDate: '1997-01-01',
                  departureDate: '',
                  emergencyContact: '李四',
                  emergencyContactPhone: '19967323015',
                  contactWay: '17705266804',
                  spouseName: '无',
                  spouseContactWay: '',
                  fatherName: '',
                  fatherNameContactWay: '',
                  motherName: '',
                  motherContactWay: '',
                  hobbies: '',
                  note: ''
                },
                {
                  id: 3,
                  entryDate: '2020-10-01',
                  staffName: '王五',
                  sex: '男',
                  department: '研发部',
                  jobs: '工程师',
                  IDCard: '330382199701100337',
                  birthDate: '1997-01-01',
                  departureDate: '',
                  emergencyContact: '李四',
                  emergencyContactPhone: '19967323015',
                  contactWay: '17705266804',
                  spouseName: '无',
                  spouseContactWay: '',
                  fatherName: '',
                  fatherNameContactWay: '',
                  motherName: '',
                  motherContactWay: '',
                  hobbies: '',
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
      physicalSurveyCurrRowId: null,
      treeData
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
          click: (event) => {
            this.physicalSurveyCurrRowId = record.id
            this.staffInfo = record
          }
        }
      }
    }
  }
}
</script>
