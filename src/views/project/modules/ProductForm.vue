<template>
  <a-modal
    title="产品采购"
    :width="640"
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
        <a-form-item label="产品名称">
          <a-input v-decorator="['productName']" />
        </a-form-item>
        <a-form-item label="供应商">
          <a-select
            style="width: 100%"
            placeholder="Please select"
            v-decorator="['supplier']"
          >
            <a-select-option value="联想">联想</a-select-option>
            <a-select-option value="华为">华为</a-select-option>
            <a-select-option value="海康">海康</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="产品">
          <a-select
            style="width: 100%"
            placeholder="Please select"
            v-decorator="['product']"
          >
            <a-select-option value="张三">联想-电脑</a-select-option>
            <a-select-option value="李四">华为-电脑</a-select-option>
            <a-select-option value="王五">海康-摄像头</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="单位">
          <a-input v-decorator="['unit']" />
        </a-form-item>
        <a-form-item label="数量">
          <a-input v-decorator="['count']" />
        </a-form-item>
        <a-form-item label="单价">
          <a-input v-decorator="['price']" />
        </a-form-item>
        <a-form-item label="类型">
          <a-input v-decorator="['type']" />
        </a-form-item>
        <a-form-item label="合同">
          <a-select
            style="width: 100%"
            placeholder="Please select"
            v-decorator="['contractId']"
          >
            <a-select-option value="合同1">合同1</a-select-option>
            <a-select-option value="合同2">合同2</a-select-option>
            <a-select-option value="合同3">合同3</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="备注">
          <a-input v-decorator="['note']" />
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
import pick from 'lodash.pick'

// 表单字段
const fields = ['id', 'staffName', 'projectDuty', 'note']

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
