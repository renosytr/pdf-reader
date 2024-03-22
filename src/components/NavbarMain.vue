<script setup>
import { ref } from 'vue'
defineProps({
  activeMenu: String,
})
const emit = defineEmits([
  'loadPDF',
  'clearCanvas'
])
const dataPDF = ref(null)
const filePDF = ref([])

// handle file upload
const uploadFile = (e) => {
  const dataFiles = e.target.files
  const pdf = dataFiles[0]

  if(!pdf.type.match("application/pdf")){
    return
  }else{
    dataPDF.value = pdf;
    const reader = new FileReader();
    reader.onload = (e) => filePDF.value.push(e.target.result)
    reader.readAsDataURL(pdf)
    setTimeout(() => {
      emit('loadPDF', {
        data: dataPDF.value,
        file: filePDF.value
      })
    }, 1500);
  }
}

// handle clear canvas
const onClearCanvas = () => {
  dataPDF.value = null
  filePDF.value = []
  emit('clearCanvas')
}
</script>
<template>
  <nav class="bg-gray-900 fixed w-full z-20 top-0 start-0 border-b border-gray-200 border-gray-600 h-20">
    <div class="max-w-screen-xl flex flex-wrap items-center justify-between mx-auto p-4">
    <a href="https://flowbite.com/" class="flex items-center space-x-3 rtl:space-x-reverse">
        <img src="https://flowbite.com/docs/images/logo.svg" class="h-8" alt="Flowbite Logo">
        <span class="self-center text-2xl font-semibold whitespace-nowrap dark:text-white">PDF Reader</span>
    </a>
    <div v-if="!dataPDF" class="flex md:order-2 space-x-3 md:space-x-0 rtl:space-x-reverse">
      <label for="file-upload" class="cursor-pointer text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 text-center">
          <div class="mt-1">Upload PDF</div>
      </label>
      <input id="file-upload" class="hidden" type="file" @change=uploadFile($event)>
    </div>
    <div v-else class="flex md:order-2 space-x-3 md:space-x-0 rtl:space-x-reverse">
      <button type="button" class="cursor-pointer text-white bg-red-600 hover:bg-red-700 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 text-center" @click="onClearCanvas()">
          Clear Canvas
      </button>
    </div>
    <div class="items-center justify-between hidden w-full md:flex md:w-auto md:order-1" id="navbar-sticky">
      <ul class="flex flex-col p-4 md:p-0 mt-4 font-medium md:space-x-8 rtl:space-x-reverse md:flex-row md:mt-0">
        <li>
          <RouterLink to="/" class="block py-2 px-3 rounded md:bg-transparent md:p-0" :class="activeMenu === 'home' ? 'text-blue-700' : 'text-white'">Home</RouterLink>
        </li>
        <li>
          <RouterLink to="/about" class="block py-2 px-3 rounded hover:bg-gray-100 md:hover:bg-transparent md:p-0" :class="activeMenu === 'about' ? 'text-blue-700' : 'text-white'">About</RouterLink>
        </li>
      </ul>
    </div>
    </div>
  </nav>
</template>