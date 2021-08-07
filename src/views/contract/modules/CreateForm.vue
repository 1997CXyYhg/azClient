<template>
  <a-modal
    title="合同编辑"
    :width="800"
    :visible="visible"
    :confirmLoading="loading"
    @ok="() => { $emit('ok') }"
    @cancel="() => { $emit('cancel') }"
  >
    <a-spin :spinning="loading">
      <a-form :form="form" v-bind="formLayout">
        <!-- 检查是否有 id 并且大于0，大于0是修改。其他是新增，新增不显示主键ID -->
        <a-form-item v-show="model && model.id > 0" label="主键ID">
          <a-input v-decorator="['id', { initialValue: 0 }]" disabled />
        </a-form-item>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="创建时间">
              <a-date-picker v-decorator="['createDate']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="合同名称">
              <a-input v-decorator="['contractName']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="合同类型">
<!--              <a-input v-decorator="['projectType']" />-->
              <a-select v-decorator="['contractType']">
                <a-select-option value="项目合同">
                  多媒体项目
                </a-select-option>
                <a-select-option value="采购合同">
                  研发项目
                </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="甲方">
              <a-input v-decorator="['firstParty']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="甲方负责人">
              <a-input v-decorator="['firstPartyPerson']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="甲方联系方式">
              <a-input v-decorator="['firstPartyContactWay']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="乙方">
              <a-input v-decorator="['secondParty']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="乙方负责人">
              <a-input v-decorator="['secondPartyPerson']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="乙方联系人">
              <a-input v-decorator="['secondPartyContactWay']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="签订日期">
              <a-date-picker v-decorator="['signDate']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="交付日期">
              <a-date-picker v-decorator="['deliveryDate']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="违约责任">
              <a-textarea v-decorator="['breachLiability']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="附件">
              <a-upload
                action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
                :default-file-list="defaultFileList"
              >
                <a-button> <a-icon type="upload" /> Upload </a-button>
              </a-upload>
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
import pick from 'lodash.pick'

// 表单字段
const fields = ['id', 'createDate', 'projectName', 'projectType', 'completeDate', 'customer', 'staff']

export default {
  props: {
    visible: {
      type: Boolean,
      required: true
    },
    loading: {
      type: Boolean,
      default: () => false
    },
    model: {
      type: Object,
      default: () => null
    }
  },
  data () {
    this.formLayout = {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 }
      }
    }
    return {
      form: this.$form.createForm(this),
      defaultFileList: [
        {
          uid: '1',
          name: 'xxx.png',
          status: 'done',
          response: 'Server Error 500', // custom error message to show
          url: 'http://www.baidu.com/xxx.png'
        },
        {
          uid: '2',
          name: 'yyy.png',
          status: 'done',
          url: 'http://www.baidu.com/yyy.png'
        },
        {
          uid: '3',
          name: 'zzz.png',
          status: 'error',
          response: 'Server Error 500', // custom error message to show
          url: 'http://www.baidu.com/zzz.png'
        }
      ]
    }
  },
  created () {
    console.log('custom modal created')

    // 防止表单未注册
    fields.forEach(v => this.form.getFieldDecorator(v))

    // 当 model 发生改变时，为表单设置值
    this.$watch('model', () => {
      this.model && this.form.setFieldsValue(pick(this.model, fields))
    })
  }
}
</script>
