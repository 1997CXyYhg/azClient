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
            <a-form-item label="公司名称">
              <a-input v-decorator="['companyName']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="地址">
              <a-input v-decorator="['address']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="经营范围">
              <a-input v-decorator="['businessScope']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="联系方式">
              <a-input v-decorator="['contactsWay']" />
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
            <a-form-item label="产品标签">
              <a-select
                mode="multiple"
                style="width: 100%"
                placeholder="Please select"
                v-decorator="['productTags']"
              >
                <a-select-option value="联想">联想</a-select-option>
                <a-select-option value="华为">华为</a-select-option>
                <a-select-option value="笔记本">笔记本</a-select-option>
                <a-select-option value="投影仪">投影仪</a-select-option>
                <a-select-option value="摄像头">摄像头</a-select-option>
              </a-select>
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
const fields = ['id', 'companyName', 'address', 'businessScope', 'contactsWay', 'email', 'productTags']

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
