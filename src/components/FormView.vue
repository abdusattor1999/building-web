<template>
  <div>
    <h1>Ro'yxatdan o'tish</h1>
    <form @submit.prevent="handleSubmit">
      <div class="form-group">
        <label for="firstName">Ism</label><br />
        <input type="text" id="firstName" v-model="formData.first_name" required />
      </div>
      <div class="form-group">
        <label for="lastName">Familiya</label><br />
        <input type="text" id="lastName" v-model="formData.last_name" required />
      </div>
      <div class="form-group">
        <label for="phone">Telefon</label><br />
        <input type="tel" id="phone" v-model="formData.phone" required />
      </div>
      <div class="form-group">
        <label for="services">Qanday xizmatlar ko'rsata olasiz ?</label><br />
        <select v-model="formData.services" id="services" multiple required>
          <option v-for="service in services" :key="service.id" :value="service.id">
            {{ service.name }}
          </option>
        </select>
      </div>
      <div class="form-group">
        <label for="regions">Qaysi tumanlarda ishlay olasiz ?</label><br />
        <select v-model="formData.regions" id="regions" multiple required>
          <option v-for="region in regions" :key="region.id" :value="region.id">
            {{ region.name }}
          </option>
        </select>
      </div>
      <div class="form-group">
        <label for="passportPhoto">Passport Rasmi</label><br />
        <br />
        <input type="file" id="passportPhoto" @change="setUserPhoto" required />
      </div>
      <button type="submit" class="submit_button">Yuborish</button>
    </form>
    <FormModel
      v-if="model.isOpen"
      :is-model-open="model.isOpen"
      :success-text="model.successText"
      :error-text="model.errorText"
      :close-sign="model.closeSignText"
      :close-model="closeModal"
    />
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, watchEffect } from 'vue'
import { useRoute } from 'vue-router'
import FormModel from '@/components/FormModel.vue'

const SERVER_URL = 'https://d0b99aadfaf63f3d72efafd67e06edf2.serveo.net/api/v1'
const route = useRoute()

const formData = reactive({
  first_name: '',
  last_name: '',
  phone: '+998901234567',
  passport_photo: '',
  services: [],
  regions: [],
  chat_id: ''
})
const services = ref([])
const regions = ref([])

const model = reactive({
  isOpen: false,
  closeSignText: '',
  successText: '',
  errorText: ''
})

const closeModal = () => {
  model.isOpen = false
}
const clearModelTextFields = (modelTextFields) => {
  modelTextFields.forEach((field) => {
    model[field] = ''
  })
}

const setShowModelBool = (show = true, text = '', type = 'success') => {
  model.isOpen = show
  switch (type) {
    case 'success':
      model.successText = text
      clearModelTextFields(['errorText', 'closeSignText'])
      break
    case 'error':
      model.errorText = text
      clearModelTextFields(['successText', 'closeSignText'])
      break
    case 'close':
      model.closeSignText = text
      clearModelTextFields(['errorText', 'successText'])
  }
}

const setUserPhoto = (event) => {
  formData.passport_photo = event.target.files[0]
}

const fetchChoices = async () => {
  try {
    const response = await fetch(`${SERVER_URL}/choices`)
    const res_data = await response.json()
    services.value = res_data.services
    regions.value = res_data.regions
  } catch (error) {
    console.error('Error fetching choices:', error)
    // Handle errors appropriately, e.g., display an error message to the user
  }
}

const handleSubmit = async () => {
  const formDataWithPhoto = new FormData()
  formData.chat_id = route.query.chat_id
  for (const key in formData) {
    if (key === 'services') {
      formDataWithPhoto.append('services', JSON.stringify(formData.services))
    } else if (key === 'regions') {
      formDataWithPhoto.append('regions', JSON.stringify(formData.regions))
    } else {
      formDataWithPhoto.append(key, formData[key])
    }
  }

  try {
    const response = await fetch(`${SERVER_URL}/master`, {
      method: 'POST',
      // headers: { 'Content-Type': 'multipart/form-data' },
      body: formDataWithPhoto
    })
    const registerUserData = await response.json()
    console.log('Res', registerUserData, response.status)
    if (response.status === 404) {
      setShowModelBool(true, registerUserData.error, 'error')
    } else if (response.status === 400) {
      setShowModelBool(true, registerUserData.error, 'close')
    } else {
      setShowModelBool(true, registerUserData.message, 'success')
    }
  } catch (error) {
    console.error('Error submitting form:', error.response)
  }
}
watchEffect(() => {
  console.log('Form Data', formData)
})
// Fetch choices on component mount (improved for efficiency)
onMounted(async () => {
  await fetchChoices()
})
</script>
