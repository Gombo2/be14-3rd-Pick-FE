<template>
    <v-row dense>
      <v-col
        v-for="(variant, i) in variants"
        :key="i"
        cols="12"
        md="4"
      >
        <v-card
          :variant="plain"
          class="mx-auto"
          color="surface-variant"
          max-width="344"
          subtitle="Greyhound divisely hello coldly fonwderfully"
          title="Headline"
        >
          <template v-slot:actions>
            <v-btn text="Button"></v-btn>
          </template>
        </v-card>
  
        <div class="text-center text-caption">{{ variant }}</div>
      </v-col>
    </v-row>
  </template>

  
  <script setup>
    const variants = ['elevated', 'flat', 'tonal', 'outlined', 'text', 'plain']
  </script>