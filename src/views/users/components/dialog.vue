<template>
  <el-dialog
    :model-value="dialogVisible"
    :title="dialogTitle"
    width="50%"
    @close="handleClose"
  >
    <el-form ref="formRef" :model="form" label-width="70px" :rules="rules">
      <!-- 用户名 -->
      <el-form-item :label="dialogForm.username" prop="username">
        <el-input v-model="form.username"></el-input>
      </el-form-item>
      <!-- 密码 -->
      <el-form-item
        :label="dialogForm.password"
        prop="password"
        v-if="dialogTitle === '添加用户' || dialogTitle === 'Add User'"
      >
        <el-input v-model="form.password" type="password"></el-input>
      </el-form-item>
      <!-- 邮箱 -->
      <el-form-item :label="dialogForm.email" prop="email">
        <el-input v-model="form.email"></el-input>
      </el-form-item>
      <!-- 手机 -->
      <el-form-item :label="dialogForm.mobile" prop="mobile">
        <el-input v-model="form.mobile"></el-input>
      </el-form-item>
    </el-form>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="handleClose">{{ dialogForm.cancel }}</el-button>
        <el-button type="primary" @click="handleConfirm">{{
          dialogForm.confirm
        }}</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup>
import { defineEmits, ref, defineProps, watch } from 'vue'
import { addUser, editUser } from '@/api/users'
import { useI18n } from 'vue-i18n'
import { ElMessage } from 'element-plus'
const i18n = useI18n()
const props = defineProps({
  dialogTitle: {
    type: String,
    default: '',
    required: true
  },
  dialogTableValue: {
    type: Object,
    default: () => {}
  }
})

const formRef = ref(null)
const form = ref({
  username: '',
  password: '',
  email: '',
  mobile: ''
})
const dialogForm = ref({
  username: i18n.t('table.username'),
  password: i18n.t('table.password'),
  email: i18n.t('table.email'),
  mobile: i18n.t('table.mobile'),
  adduserTitle: i18n.t('dialog.adduserTitle'),
  confirm: i18n.t('dialog.confirm'),
  cancel: i18n.t('dialog.cancel')
})
const rules = ref({
  username: [
    {
      required: true,
      message: i18n.t('dialog.msgUsername'),
      trigger: 'blur'
    }
  ],
  password: [
    {
      required: true,
      message: i18n.t('dialog.msgPassword'),
      trigger: 'blur'
    }
  ],
  email: [
    {
      required: true,
      message: i18n.t('dialog.msgEmail'),
      trigger: 'blur'
    },
    {
      type: 'email',
      message: i18n.t('dialog.messageemail'),
      trigger: ['blur', 'change']
    }
  ],
  mobile: [
    {
      required: true,
      message: i18n.t('dialog.msgMobile'),
      trigger: 'blur'
    }
  ]
})

watch(
  () => props.dialogTableValue,
  () => {
    console.log(props.dialogTableValue)
    form.value = props.dialogTableValue
  },
  { deep: true, immediate: true }
)

const emits = defineEmits(['update:modelValue', 'initUserList'])

const handleClose = () => {
  emits('update:modelValue', false)
}

const handleConfirm = () => {
  formRef.value.validate(async (valid) => {
    if (valid) {
      props.dialogTitle === '添加用户' || props.dialogTitle === 'Add User'
        ? await addUser(form.value)
        : await editUser(form.value)
      ElMessage({
        message: i18n.t('message.updateSuccess'),
        type: 'success'
      })
      emits('initUserList')
      handleClose()
    } else {
      console.log('error submit!!')
      return false
    }
  })
}
</script>

<style lang="scss" scoped>
::v-deep .el-form-item__label {
  white-space: nowrap !important;
  margin-right: 15px;
}
</style>
