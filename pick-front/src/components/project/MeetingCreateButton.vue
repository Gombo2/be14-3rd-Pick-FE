<template>
    <v-btn
      color="primary"
      variant="tonal"
      class="meeting-create-btn"
      prepend-icon="mdi-tag-plus"
      @click="handleClick"
    >
       회의록 작성하기
    </v-btn>
  </template>
  
  <script setup>
  import { useRouter } from 'vue-router'

  
  const router = useRouter()
  
  const handleClick = () => {
    router.push('/project/create-meeting')
  }
  </script>
  
  <style scoped>
  .meeting-create-btn {
    float: right;
    font-weight: 600;
    margin-bottom: 16px;
    text-transform: none;
  }
  </style>