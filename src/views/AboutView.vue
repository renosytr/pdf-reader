<script setup>
import { ref } from 'vue'
import NavbarMain from '@/components/NavbarMain.vue'

const activeMenu = ref('about');
</script>

<template>
  <NavbarMain :active-menu="activeMenu"></NavbarMain>
</template>
