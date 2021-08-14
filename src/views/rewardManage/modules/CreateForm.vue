<template>
  <a-modal
    title="员工信息"
    :width="800"
    :visible="visible"
    :confirmLoading="loading"
    okText="确认"
    cancelText="取消"
    @ok="() => { $emit('ok') }"
    @cancel="() => { $emit('cancel') }">
    <a-spin :spinning="loading">
      <a-form
        :form="form"
        v-bind="formLayout">
        <!-- 检查是否有 id 并且大于0，大于0是修改。其他是新增，新增不显示主键ID -->
        <a-form-item
          v-show="model && model.id > 0"
          label="主键ID">
          <a-input
            v-decorator="['id', { initialValue: 0 }]"
            disabled />
        </a-form-item>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="员工">
              <a-input v-decorator="['staffName']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="兑现日期">
              <a-date-picker
                v-decorator="['execDate']"
                placeholder="选择日期" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="事迹">
              <a-input v-decorator="['deed']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="类型">
              <a-input v-decorator="['type']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="处理结果">
              <a-input v-decorator="['execResult']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="信息展示有效期">
              <a-range-picker
                v-decorator="['validTime']"
                style="width: 300px" />
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
const fields = ['id', 'staffName', 'createDate', 'deed', 'type', 'execResult', 'execDate', 'validTime', 'status']

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
      form: this.$form.createForm(this)
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
