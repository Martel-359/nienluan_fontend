<template>
  <div>
    <a-button type="primary" @click="showModal">Add User<PlusCircleOutlined /></a-button>
    <a-modal v-model:open="visible" title="Title" @ok="handleOk">
      <template #footer>
        <a-button key="back" @click="handleCancel">Return</a-button>
        <a-button key="submit" type="primary" :loading="loading" @click="handleOk">Submit</a-button>
      </template>
      <a-form
        layout="horizontal"
        :model="formState"
        :label-col="labelCol"
        :wrapper-col="wrapperCol"
        name="register"
        ref="formRef"
        class="register-form"
        :rules="rules"
      >
        <a-form-item label="Họ và tên" name="Hoten" ref="Hoten">
          <a-input v-model:value="formState.Hoten">
            <template #prefix>
              <UserOutlined class="site-form-item-icon" />
            </template>
          </a-input>
        </a-form-item>

        <a-form-item label="Email" name="Email" ref="Email">
          <a-input v-model:value="formState.Email">
            <template #prefix>
              <UserOutlined class="site-form-item-icon" />
            </template>
          </a-input>
        </a-form-item>

        <a-form-item label="Mật khẩu" name="Matkhau" ref="Matkhau">
          <a-input-password v-model:value="formState.Matkhau">
            <template #prefix>
              <LockOutlined class="site-form-item-icon" />
            </template>
          </a-input-password>
        </a-form-item>

        <a-form-item label="Địa chỉ" name="Diachi" ref="Diachi">
          <a-input v-model:value="formState.Diachi">
            <template #prefix>
              <UserOutlined class="site-form-item-icon" />
            </template>
          </a-input>
        </a-form-item>

        <a-form-item label="Số điện thoại" name="Sodienthoai" ref="Sodienthoai">
          <a-input v-model:value="formState.Sodienthoai">
            <template #prefix>
              <UserOutlined class="site-form-item-icon" />
            </template>
          </a-input>
        </a-form-item>

        <a-form-item label="Nhóm" name="Groupid" ref="Groupid">
          <a-select v-model:value="formState.Groupid">
            <a-select-option value=""> </a-select-option>
            <a-select-option v-for="group in groupsData" :key="group.Groupid" :value="group.Groupid"
              >{{ group.Tennhom.toUpperCase() }}
            </a-select-option>
          </a-select>
        </a-form-item>
      </a-form>
    </a-modal>
  </div>
</template>
<script>
import { defineComponent, ref, reactive } from 'vue'
import { PlusCircleOutlined } from '@ant-design/icons-vue'
import { createUser, getAllGroup } from '@/sevices/admin.service.js'
export default defineComponent({
  setup(props, { emit }) {
    const loading = ref(false)
    const visible = ref(false)
    const groupsData = ref([])
    // groupsData.value = getAllGroup()
    // console.log('groupsData', groupsData)
    const formRef = ref(null) // Define ref here for the form
    const showModal = async () => {
      visible.value = true
    }

    async function fetchGroups() {
      try {
        const response = await getAllGroup()
        groupsData.value = response.data
        console.log('groupsData', groupsData)
      } catch (error) {
        console.error('Failed to fetch groups:', error)
      }
    }

    const handleOk = async () => {
      if (!formRef.value) return
      loading.value = true

      try {
        // Validate specific fields if necessary
        await formRef.value.validate()
        // After successful validation, make the API call
        await createUser(formState)
        // Reset the form after successful submission
        formRef.value.resetFields()
        // Emit an event to notify the parent component
        emit('userCreated')
      } catch (error) {
        console.log('Validation/API call error', error)
      } finally {
        loading.value = false
        visible.value = false
      }
    }

    const handleCancel = () => {
      visible.value = false
    }

    const formState = reactive({
      Email: '',
      Matkhau: '',
      Hoten: '',
      Diachi: '',
      Sodienthoai: '',
      Groupid: ''
    })

    const rules = {
      Hoten: [
        { required: true, message: 'Vui lòng nhập họ và tên!' },
        { min: 6, message: 'Họ và tên phải lớn hơn 6 ký tự!' }
      ],
      Email: [
        { required: true, message: 'Vui lòng nhập email!' },
        { type: 'email', message: 'Email không hợp lệ!' }
      ],
      Matkhau: [
        { required: true, message: 'Vui lòng nhập mật khẩu!' },
        { min: 8, message: 'Mật khẩu phải lớn hơn 8 ký tự!' },
        { max: 20, message: 'Mật khẩu phải nhỏ hơn 20 ký tự!' },
        {
          type: 'string',
          pattern: /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d]{8,}$/,
          message: 'Mật khẩu phải chứa ít nhất 1 chữ hoa, 1 chữ thường và 1 số!'
        }
      ],
      Diachi: [{ required: true, message: 'Vui lòng nhập địa chỉ!' }],
      Sodienthoai: [
        { required: true, message: 'Vui lòng nhập số điện thoại!' },
        { pattern: /(84|0[3|5|7|8|9])+([0-9]{8})\b/, message: 'Số điện thoại không hợp lệ!' },
        { min: 10, message: 'Số điện thoại phải lớn hơn 10 số!' },
        { max: 11, message: 'Số điện thoại phải nhỏ hơn 11 số!' }
      ],
      Groupid: [{ required: true, message: 'Vui lòng chọn nhóm!' }]
    }
    const labelCol = {
      style: {
        width: '150px'
      }
    }
    const wrapperCol = {
      span: 14
    }
    return {
      loading,
      visible,
      showModal,
      handleOk,
      handleCancel,
      formRef, // make sure to return it
      formState,
      rules,
      groupsData,
      labelCol,
      wrapperCol,
      fetchGroups
    }
  },
  components: {
    PlusCircleOutlined
  },
  created() {
    this.fetchGroups()
  }
})
</script>
