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
            <a-form-item label="入职日期">
              <a-input v-decorator="['entryDate']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="姓名">
              <a-input v-decorator="['staffName']" />
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
            <a-form-item label="部门">
              <a-input v-decorator="['department']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="岗位">
              <a-input v-decorator="['jobs']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="联系方式">
              <a-input v-decorator="['contactWay']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="身份证号">
              <a-input v-decorator="['IDCard']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="出生日期">
              <a-input v-decorator="['birthDate']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="紧急联系人">
              <a-input v-decorator="['emergencyContact']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="紧急联系人方式">
              <a-input v-decorator="['emergencyContactWay']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="配偶姓名">
              <a-input v-decorator="['spouseName']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="配偶联系方式">
              <a-input v-decorator="['spouseContactWay']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="父亲姓名">
              <a-input v-decorator="['fatherName']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="父亲联系方式">
              <a-input v-decorator="['fatherNameContactWay']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="父亲姓名">
              <a-input v-decorator="['fatherName']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="父亲联系方式">
              <a-input v-decorator="['fatherNameContactWay']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="母亲姓名">
              <a-input v-decorator="['motherName']" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="母亲联系方式">
              <a-input v-decorator="['motherContactWay']" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :span="12">
            <a-form-item label="兴趣爱好">
              <a-input v-decorator="['hobbies']" />
            </a-form-item>
          </a-col>
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
const fields = ['id', 'entryDate', 'staffName', 'sex', 'department', 'jobs', 'contactWay', 'IDCard', 'birthDate',
'emergencyContact', 'emergencyContactWay', 'spouseName', 'spouseContactWay', 'fatherName', 'fatherNameContactWay',
'fatherNameContactWay', 'motherName', 'motherContactWay', 'hobbies', 'note']

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
