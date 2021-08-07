<template>
  <a-modal
    title="员工信息"
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
            <a-form-item label="联系人">
              <a-input v-decorator="['contactName']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="职位">
              <a-input v-decorator="['jobs']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="性别">
              <a-input v-decorator="['sex']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="工作电话">
              <a-input v-decorator="['contactWay']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="私人电话">
              <a-input v-decorator="['privateContactWay']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="私人地址">
              <a-input v-decorator="['address']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="邮箱">
              <a-input v-decorator="['email']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="喜好">
              <a-input v-decorator="['hobbies']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="备注">
              <a-input v-decorator="['note']" />
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
const fields = ['id', 'contactName', 'jobs', 'sex', 'contactWay', 'privateContactWay', 'customerGrade', 'email', 'hobbies', 'note', 'address']

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
