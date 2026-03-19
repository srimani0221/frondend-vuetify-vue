<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const selectedFilter = ref('All')
const searchQuery = ref('')
const filterOptions = ['All', 'Active', 'Completed', 'Draft']

const isWorkflowDialogOpen = ref(false)
const selectedWorkflow = ref('advanced')
</script>

<template>
  <div class="h-100">
    <VCard
      class="pa-4 pa-sm-6 d-flex flex-column h-100"
      elevation="0"
      style="border-radius: 8px; min-block-size: calc(100vh - 140px);"
    >
      <div class="d-flex flex-column flex-sm-row align-center gap-4 mb-6">
        <VSelect
          v-model="selectedFilter"
          :items="filterOptions"
          variant="outlined"
          density="compact"
          hide-details
          class="w-100 w-sm-auto"
          style="inline-size: 100%; max-inline-size: 200px;"
        />
        <VTextField
          v-model="searchQuery"
          placeholder="Search"
          variant="outlined"
          density="compact"
          hide-details
          class="w-100 w-sm-auto"
          style="inline-size: 100%; max-inline-size: 300px;"
        >
          <template #prepend-inner>
            <VIcon
              icon="tabler-search"
              size="20"
              class="text-disabled"
            />
          </template>
        </VTextField>
      </div>
      <div class="d-flex flex-column align-center justify-center flex-grow-1">
        <div
          class="mb-6 position-relative w-100 d-flex justify-center"
          style=" aspect-ratio: 1/1; max-inline-size: 320px;"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 500 500"
            width="100%"
            height="100%"
          >
            <g transform="translate(10, 50)">
              <rect
                x="180"
                y="40"
                width="200"
                height="240"
                rx="8"
                fill="#f1f5f9"
                stroke="#e2e8f0"
                stroke-width="3"
                transform="rotate(15 280 160)"
              />
              <rect
                x="150"
                y="60"
                width="220"
                height="260"
                rx="8"
                fill="#ffffff"
                stroke="#e2e8f0"
                stroke-width="3"
              />
              <line x1="180" y1="100" x2="300" y2="100" stroke="#cbd5e1" stroke-width="6" stroke-linecap="round" />
              <line x1="180" y1="130" x2="340" y2="130" stroke="#cbd5e1" stroke-width="6" stroke-linecap="round" />
              <line x1="180" y1="160" x2="310" y2="160" stroke="#cbd5e1" stroke-width="6" stroke-linecap="round" />
              
              <path
                d="M 120 120 L 220 120 L 250 160 L 420 160 C 430 160 440 168 440 180 L 440 380 C 440 390 430 400 420 400 L 120 400 C 110 400 100 390 100 380 Z"
                fill="#f1f5f9"
              />
              
              <path
                d="M 80 180 L 400 180 C 410 180 420 188 425 200 L 460 380 C 465 390 455 400 440 400 L 100 400 C 85 400 75 390 80 380 L 115 200 C 118 190 125 180 140 180 Z"
                fill="#8c90fe"
              />
              
              <text
                x="300"
                y="330"
                font-size="110"
                font-family="Arial, sans-serif"
                font-weight="bold"
                fill="#ffffff"
                text-anchor="middle"
                transform="rotate(-5, 300, 330)"
              >?</text>
              
              <path
                d="M 60 400 C 40 330 60 260 110 240 C 140 230 160 250 180 270"
                fill="#f8fafc"
                stroke="#cbd5e1"
                stroke-width="3"
                stroke-linecap="round"
              />
              <circle cx="100" cy="200" r="30" fill="#e2e8f0" />
              <path
                d="M 100 240 L 160 280 L 210 250"
                fill="none"
                stroke="#f9a8d4"
                stroke-width="18"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
              <path
                d="M 100 330 L 70 410 L 90 410 L 115 360"
                fill="#60a5fa"
              />
              <path
                d="M 120 340 L 150 410 L 170 410 L 135 345"
                fill="#60a5fa"
              />
              <rect x="60" y="410" width="35" height="10" rx="4" fill="#fbbf24" />
              <rect x="145" y="410" width="35" height="10" rx="4" fill="#fbbf24" />
              <path
                d="M 60 260 Q 100 230 140 260 L 140 340 Q 100 350 70 340 Z"
                fill="#fbcfe8"
              />
              <line x1="220" y1="260" x2="280" y2="300" stroke="#8c90fe" stroke-width="16" stroke-linecap="round" />
              <circle cx="210" cy="200" r="52" fill="#ffffff" opacity="0.6" />
              <circle cx="210" cy="200" r="60" fill="none" stroke="#a5b4fc" stroke-width="12" />
              <path d="M 180 170 A 40 40 0 0 1 230 170" fill="none" stroke="#ffffff" stroke-width="8" stroke-linecap="round" opacity="0.8" />
              <text x="80" y="100" font-size="50" font-family="Arial, sans-serif" font-weight="bold" fill="#a5b4fc" text-anchor="middle" transform="rotate(-15, 80, 100)">?</text>
              <text x="210" y="50" font-size="60" font-family="Arial, sans-serif" font-weight="bold" fill="#a5b4fc" text-anchor="middle" transform="rotate(20, 210, 50)">?</text>
            </g>
          </svg>
        </div>
        <VBtn
          color="primary"
          class="px-8 mt-2"
          style="border-radius: 6px; block-size: 44px; font-size: 15px; font-weight: 500;"
          elevation="0"
          @click="isWorkflowDialogOpen = true"
        >
          New Campaign
        </VBtn>
      </div>
      
    </VCard>
    <VDialog
      v-model="isWorkflowDialogOpen"
      max-width="650"
      persistent
    >
      <VCard class="pa-0 pa-sm-2 rounded-xl">
        <VCardItem class="pb-1 pt-6 px-4 px-sm-6">
          <template #prepend>
            <div class="pe-2">
              <VCardTitle class="text-h6 text-sm-h5 font-weight-regular" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity)); line-height: 1.2;">Select Workflow Mode</VCardTitle>
              <VCardSubtitle class="text-caption text-sm-body-2 mt-1" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));">Choose how you want your campaign to behave</VCardSubtitle>
            </div>
          </template>
          <template #append>
            <IconBtn @click="isWorkflowDialogOpen = false" color="disabled" class="mt-n8 me-n2">
              <VIcon icon="tabler-circle-x" size="24" />
            </IconBtn>
          </template>
        </VCardItem>

        <VCardText class="pt-5 px-4 px-sm-6 pb-6">
          <div class="d-flex flex-column gap-3 gap-sm-4">
            <div
              class="pa-3 pa-sm-4 border rounded-lg cursor-pointer d-flex transition-all"
              :style="selectedWorkflow === 'advanced' ? 'border-color: rgba(var(--v-theme-primary), 1) !important; background-color: rgba(var(--v-theme-primary), 0.05);' : 'border-color: rgba(var(--v-theme-on-surface), 0.12);'"
              @click="selectedWorkflow = 'advanced'"
              style="transition: all 0.2s ease-in-out;"
            >
              <div class="pt-1 pe-3 pe-sm-4">
                <VIcon 
                  :icon="selectedWorkflow === 'advanced' ? 'tabler-circle-dot' : 'tabler-circle'" 
                  :color="selectedWorkflow === 'advanced' ? 'primary' : 'disabled'"
                  size="22"
                />
              </div>
              <div class="flex-grow-1 overflow-hidden">
                <div class="d-flex align-center gap-2 mb-1 flex-wrap">
                  <span class="text-subtitle-2 font-weight-bold text-truncate" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity)); font-size: 0.9rem !important;">Advanced Workflow</span>
                  <VChip size="x-small" color="success" variant="tonal" class="font-weight-medium px-2" style="block-size: 18px; font-size: 0.65rem;">Recommended</VChip>
                </div>
                <div class="text-caption mb-2" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity)); font-size: 0.75rem;">Best for high-volume outreach</div>
                <div class="d-flex align-center gap-2 gap-sm-3 text-caption flex-wrap" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity)); font-size: 0.7rem;">
                  <div class="d-flex align-center gap-1"><VIcon icon="tabler-point-filled" size="8" color="secondary"/> Conditions</div>
                  <div class="d-flex align-center gap-1"><VIcon icon="tabler-point-filled" size="8" color="secondary"/> Multi-paths</div>
                  <div class="d-flex align-center gap-1"><VIcon icon="tabler-point-filled" size="8" color="secondary"/> Precision</div>
                </div>
              </div>
              <div class="d-none d-md-flex align-center ms-4 opacity-80">
                <svg width="60" height="36" viewBox="0 0 100 60" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <rect x="0" y="10" width="80" height="40" rx="6" fill="#f1f5f9" />
                  <rect x="10" y="20" width="60" height="6" rx="3" fill="#cbd5e1" />
                  <rect x="10" y="35" width="25" height="6" rx="3" fill="#e2e8f0" />
                  <rect x="50" y="10" width="40" height="35" rx="6" fill="#bfdbfe" />
                  <rect x="55" y="20" width="30" height="4" rx="2" fill="#ffffff" />
                  <rect x="55" y="28" width="20" height="4" rx="2" fill="#ffffff" />
                  <rect x="75" y="32" width="15" height="12" rx="2" fill="#60a5fa" />
                </svg>
              </div>
            </div>
            <div
              class="pa-3 pa-sm-4 border rounded-lg cursor-pointer d-flex transition-all"
              :style="selectedWorkflow === 'standard' ? 'border-color: rgba(var(--v-theme-primary), 1) !important; background-color: rgba(var(--v-theme-primary), 0.05);' : 'border-color: rgba(var(--v-theme-on-surface), 0.12);'"
              @click="selectedWorkflow = 'standard'"
              style="transition: all 0.2s ease-in-out;"
            >
              <div class="pt-1 pe-3 pe-sm-4">
                <VIcon 
                  :icon="selectedWorkflow === 'standard' ? 'tabler-circle-dot' : 'tabler-circle'" 
                  :color="selectedWorkflow === 'standard' ? 'primary' : 'disabled'"
                  size="22"
                />
              </div>
              <div class="flex-grow-1 overflow-hidden">
                <div class="d-flex align-center gap-2 mb-1 flex-wrap">
                  <span class="text-subtitle-2 font-weight-bold text-truncate" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity)); font-size: 0.9rem !important;">Standard Workflow</span>
                </div>
                <div class="text-caption mb-2" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity)); font-size: 0.75rem;">Best for beginners</div>
                <div class="d-flex align-center gap-2 gap-sm-3 text-caption flex-wrap" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity)); font-size: 0.7rem;">
                  <div class="d-flex align-center gap-1"><VIcon icon="tabler-point-filled" size="8" color="secondary"/> Linear</div>
                  <div class="d-flex align-center gap-1"><VIcon icon="tabler-point-filled" size="8" color="secondary"/> Basic</div>
                  <div class="d-flex align-center gap-1"><VIcon icon="tabler-point-filled" size="8" color="secondary"/> Easy Setup</div>
                </div>
              </div>
              <div class="d-none d-md-flex align-center ms-4 opacity-70">
                <svg width="60" height="36" viewBox="0 0 100 60" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <rect x="0" y="5" width="80" height="50" rx="6" fill="#f8fafc" stroke="#e2e8f0" stroke-width="2" />
                  <rect x="10" y="15" width="60" height="12" rx="2" fill="#93c5fd" />
                  <rect x="10" y="35" width="20" height="4" rx="2" fill="#cbd5e1" />
                  <rect x="10" y="42" width="15" height="4" rx="2" fill="#cbd5e1" />
                  <rect x="35" y="35" width="35" height="4" rx="2" fill="#e2e8f0" />
                  <rect x="35" y="42" width="35" height="4" rx="2" fill="#e2e8f0" />
                </svg>
              </div>
            </div>

          </div>
        </VCardText>

        <VCardActions class="px-4 px-sm-6 pb-6 pt-2">
          <VSpacer />
          <div class="d-flex align-center gap-2">
            <VBtn
              color="secondary"
              variant="tonal"
              class="px-4 text-none"
              style="border-radius: 6px; font-weight: 500; min-inline-size: 80px;"
              @click="isWorkflowDialogOpen = false"
            >
              Close
            </VBtn>
            <VBtn
              color="primary"
              variant="elevated"
              class="px-6 text-none"
              style="border-radius: 6px; font-weight: 500; min-inline-size: 100px;"
              @click="() => { isWorkflowDialogOpen = false; if(selectedWorkflow === 'advanced') { router.push({ name: 'advance-campaign' }) } else if(selectedWorkflow === 'standard') { router.push({ name: 'standard-campaign' }) } }"
            >
              Next
            </VBtn>
          </div>
        </VCardActions>
      </VCard>
    </VDialog>
  </div>
</template>
