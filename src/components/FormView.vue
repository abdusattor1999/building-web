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
          <option v-for="service in services" :key="service.id">{{ service.name }}</option>
        </select>
      </div>
      <div class="form-group">
        <label for="regions">Qaysi tumanlarda ishlay olasiz ?</label><br />
        <select v-model="formData.regions" id="regions" multiple required>
          <option v-for="region in regions" :key="region.id">{{ region.name }}</option>
        </select>
      </div>
      <div class="form-group">
        <label for="passportPhoto">Passport Rasmi</label><br />
        <p v-if="formData.passportPhoto">{{ formData.passportPhoto.name }}</p>
        <br />
        <input type="file" id="passportPhoto" ref="passportPhotoInput" required />
      </div>
      <input type="hidden" v-model="chatId" />
      <button type="submit" class="submit_button">Yuborish</button>
    </form>
    <v-modal v-model="showModal" :content="modalContent">
      <template #header> Server Response </template>
    </v-modal>
  </div>
</template>

<script>
import { ref, watchEffect, onMounted } from 'vue'
// import { VModal } from 'vuetify/lib'

const SERVER_URL = 'http://localhost:7778'
export default {
  setup() {
    const chatId = ref(new URLSearchParams(window.location.search).get('chat_id'))
    const formData = ref({
      first_name: '',
      last_name: '',
      phone: '',
      services: [],
      regions: []
    })
    const services = ref([])
    const regions = ref([])

    const showModal = ref(false)
    const modalContent = ref('')

    const fetchChoices = async () => {
      try {
        const response = await fetch(`${SERVER_URL}/api/v1/choices`)
        console.log(response.data)
        services.value = response.data.services
        regions.value = response.data.regions
      } catch (error) {
        console.error('Error fetching choices:', error)
        // Handle errors appropriately, e.g., display an error message to the user
      }
    }

    const handleSubmit = async (event) => {
      event.preventDefault()
      const formDataWithPhoto = new FormData()
      for (const key in formData.value) {
        formDataWithPhoto.append(key, formData.value[key])
      }
      formDataWithPhoto.append('passportPhoto', event.target.elements.passportPhoto.files[0])

      try {
        const response = await fetch('SERVER_URL/master', formDataWithPhoto, {
          headers: { 'Content-Type': 'multipart/form-data' },
          method: 'POST'
        })
        // Handle successful submission, e.g., display a success message or redirect
        console.log('Form submitted successfully!')

        if (response.status === 200) {
          modalContent.value = response.data.message
          showModal.value = true
          // Handle successful submission (e.g., clear form, redirect)
        } else if (response.status === 400) {
          modalContent.value = response.data.error
          showModal.value = true
        } else if (response.status === 401) {
          modalContent.value = response.data.error
          showModal.value = true
          // Close the web app in Telegram bot (implementation depends on your bot framework)
          console.error('User unauthorized, closing app')
          window.Telegram.WebApp.close()
        } else {
          console.error('Unexpected server response:', response)
          // Handle unexpected responses
          modalContent.value = 'An unexpected error occurred.'
          showModal.value = true
        }
      } catch (error) {
        console.error('Error submitting form:', error)
        // Handle errors appropriately, e.g., display an error message to the user
      }
    }

    // Fetch choices on component mount (improved for efficiency)
    onMounted(fetchChoices)

    // Update chatId if URL changes (improved clarity)
    watchEffect(() => {
      chatId.value = new URLSearchParams(window.location.search).get('chat_id')
    }, [window.location.search])

    return { chatId, formData, services, regions, handleSubmit }
  }
}
</script>
