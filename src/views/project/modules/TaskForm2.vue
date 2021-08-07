<template>
  <a-modal
    title="任务编辑"
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
            <a-form-item label="任务名称">
              <a-input v-decorator="['customer']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="任务目标">
              <a-input v-decorator="['staff']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="指标">
              <a-input v-decorator="['customer']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="开始时间">
              <a-date-picker v-decorator="['createDate']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="计划交付时间">
              <a-date-picker v-decorator="['createDate']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="实际完成日期">
              <a-date-picker v-decorator="['createDate']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="任务所处节点">
              <a-input v-decorator="['customer']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="执行人员">
              <a-input v-decorator="['staff']" />
            </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="任务类型">
              <a-input v-decorator="['staff']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="状态">
              <a-input v-decorator="['staff']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="完成质量">
<!--              <a-input v-decorator="['projectType']" />-->
              <a-select v-decorator="['projectType']">
                <a-select-option value="多媒体项目">
                  多媒体项目
                </a-select-option>
                <a-select-option value="研发项目">
                  研发项目
                </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="备注">
              <a-input v-decorator="['projectName']" />
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
