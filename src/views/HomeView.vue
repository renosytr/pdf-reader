<script setup>
import { ref } from 'vue'
import * as pdfjsLib from "pdfjs-dist/build/pdf.mjs"
import * as pdfjsWorker from "pdfjs-dist/build/pdf.worker.min.mjs"
import NavbarMain from '@/components/NavbarMain.vue'

// set reactive variable for active menu in navbar
const activeMenu = ref('home')

// convert file size into human readable number
const convertSize = (rawSize) => {
  let convertGb = rawSize/1.0e+9
  let convertMb = rawSize/1.0e+6
  let convertKb = rawSize/1.0e+3
  if(convertKb > 0 && convertMb < 1){
    return convertKb.toFixed(2) + ' KB'
  }else if(convertKb > 0 && convertMb > 1){
    return convertMb.toFixed(2) + 'MB';
  } else {
    return convertGb.toFixed(2) + 'GB';
  }
}

// render uploaded PDF file in the canvas with id 'pdf-viewer'
const uploadedPDF = ref(null)
const handleFilePDF = async (filePDF) => {
  uploadedPDF.value = filePDF
  const canvasContainer = document.getElementById('pdf-viewer');
  if(canvasContainer){
    // run pdfjs-dist worker 
    pdfjsLib.GlobalWorkerOptions.workerSrc = pdfjsWorker
    const loadingFile = pdfjsLib.getDocument(uploadedPDF.value.file[0]);

    // get the PDF object once the pdfjs-dist worker has finished loading all the file
    loadingFile.promise.then((pdf)=> {
      for(let i = 1; i <= pdf.numPages; i++){

        // get each page through for loop 
        pdf.getPage(i).then((page) => {
          // set default page scaling
          const currentWidth = page.getViewport({scale: 1}).width
          const allowedWidth = 1000
          const scale = allowedWidth / currentWidth
          const viewportPage = page.getViewport({scale: scale})

          // create wrapper for the canvas
          const wrapper = document.createElement("div")
          wrapper.className = `canvas-wrapper-${i} relative flex border-2`

          const canvas = document.createElement('canvas');
          canvas.className = `canvas-window-${i} relative`
          canvas.height = viewportPage.height
          canvas.width = viewportPage.width

          const context = canvas.getContext('2d')
          const renderContext = {
            canvasContext: context,
            viewport: viewportPage
          };

          // add the canvas to the wrapper
          wrapper.appendChild(canvas)
          canvasContainer.appendChild(wrapper);

          // render the page
          page.render(renderContext)
        })
      }
    })
  }
}

// clear canvas and its reactive variable which holds the PDF file data
const handleClearCanvas = () => {
  uploadedPDF.value = null
  const canvasContainer = document.getElementById('pdf-viewer');
  if(canvasContainer){
    canvasContainer.innerHTML = '';
  }
}
</script>

<template>
  <NavbarMain :active-menu="activeMenu" @loadPDF="handleFilePDF($event)" @clearCanvas="handleClearCanvas()"></NavbarMain>
  <div class="flex mt-20 h-[91.25vh]">
    <aside id="default-sidebar" class="w-64 h-full transition-transform -translate-x-full sm:translate-x-0" aria-label="Sidebar">
      <div class="h-full px-3 py-4 overflow-y-auto bg-gray-50">
        <div class="mb-4">
          <label for="file-name" class="block mb-1 text-sm font-medium text-gray-900">File Name</label>
          <div v-if="uploadedPDF" id="file-name" class="text-sm">{{ uploadedPDF.data.name }}</div>
          <div v-else id="file-name" class="text-sm">-</div>
        </div>
        <div class="mb-4">
          <label for="file-name" class="block mb-1 text-sm font-medium text-gray-900">File Size</label>
          <div v-if="uploadedPDF" id="file-name" class="text-sm">{{ convertSize(uploadedPDF.data.size) }}</div>
          <div v-else id="file-name" class="text-sm">-</div>
        </div>
        <div class="mb-4">
          <label for="file-name" class="block mb-1 text-sm font-medium text-gray-900">File Type</label>
          <div v-if="uploadedPDF" id="file-name" class="text-sm">{{ uploadedPDF.data.type === "application/pdf" ? "PDF" : "Not Supported Format" }}</div>
          <div v-else id="file-name" class="text-sm">-</div>
        </div>
      </div>
    </aside>
    <main class="w-full bg-gray-200">
        <div class="bg-gray-200">
            <div class="flex justify-center h-[91.25vh] overflow-scroll">
                <div id="pdf-viewer"></div>
            </div>
        </div>
    </main>
  </div>
</template>
