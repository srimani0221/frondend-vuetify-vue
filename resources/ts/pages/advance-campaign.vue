<script setup lang="ts">
import { ref } from 'vue'

const panelMethod = ref([0])
const panelUpload = ref([0])
const selectedImportMethod = ref('linkedin')
const currentStep = ref(2)
const isLookalikeDialogOpen = ref(false)
const hasLookalikeLeads = ref(false)
const selectedLookalikeList = ref('founder')
const topStep = ref(1)

const handleNext = () => {
  if (topStep.value === 1) {
    if (selectedImportMethod.value === 'csv' && currentStep.value === 2) {
      currentStep.value = 3;
      panelMethod.value = [];
      panelUpload.value = [];
    } else if (selectedImportMethod.value === 'lookalike' && currentStep.value === 2) {
      isLookalikeDialogOpen.value = true;
    } else {
      panelMethod.value = [];
      panelUpload.value = [];
      topStep.value = 2;
    }
  } else {
    // Final submit logic here
  }
}

const handlePrevious = () => {
  if (topStep.value === 2) {
    topStep.value = 1;
    panelMethod.value = [0];
  }
}

const handleLookalikeSelect = () => {
  isLookalikeDialogOpen.value = false;
  panelMethod.value = [];
  panelUpload.value = [];
  topStep.value = 2;
}

const selectedTab = ref('linkedin')
</script>

<template>
  <div class="h-100">
    <VCard class="pa-4 pa-sm-6 elevation-0 d-flex flex-column h-100"
      style=" border-radius: 8px;min-block-size: calc(100vh - 140px);">


      <div v-if="topStep === 1" class="d-flex flex-grow-1 position-relative animate-fade-in">
        <div class="d-flex flex-column align-center pe-6 ms-4" style="inline-size: 24px;">
          <VIcon v-if="currentStep > 1" icon="tabler-circle-check-filled" color="success" size="18"
            class="mt-4 bg-surface" style="z-index: 2;" />
          <div v-else class="rounded-circle bg-surface mt-4"
            style="z-index: 2; border: 2px solid rgb(var(--v-theme-primary)); block-size: 14px; inline-size: 14px;">
          </div>

          <div class="flex-grow-1" style=" background-color: #e2e8f0;inline-size: 1px; margin-block: -2px;"></div>

          <template v-if="currentStep >= 2">
            <VIcon v-if="currentStep > 2" icon="tabler-circle-check-filled" color="success" size="18" class="bg-surface"
              style="z-index: 2; margin-block-start: 2px;" />
            <div v-else class="rounded-circle bg-surface"
              style="z-index: 2; border: 2px solid rgb(var(--v-theme-primary)); block-size: 14px; inline-size: 14px; margin-block-start: 2px;">
            </div>

            <div class="flex-grow-1" style=" background-color: #e2e8f0;inline-size: 1px; margin-block: -2px 20px;">
            </div>
          </template>
        </div>

        <div class="flex-grow-1 overflow-hidden" style="max-inline-size: 100%;">

          <VExpansionPanels v-model="panelMethod" variant="accordion"
            class="border rounded-lg elevation-0 bg-transparent mb-6">
            <VExpansionPanel elevation="0" class="bg-transparent" :value="0">
              <VExpansionPanelTitle class="font-weight-medium text-subtitle-2 py-4"
                style="color: #4b465c; font-size: 0.95rem !important;">
                <div class="d-flex align-center gap-3">
                  {{ currentStep > 2 ? 'Upload CSV file Selected' : 'Choose Import Method' }}
                  <VChip v-if="currentStep === 2" size="small" color="secondary" variant="tonal"
                    class="rounded font-weight-medium px-2" style=" block-size: 20px;font-size: 0.7rem;">Step 1 of 2
                  </VChip>
                  <VChip v-if="currentStep === 3" size="small" color="secondary" variant="tonal"
                    class="rounded font-weight-medium px-2" style=" block-size: 20px;font-size: 0.7rem;">Step 1 of 2
                  </VChip>
                </div>
              </VExpansionPanelTitle>
              <VExpansionPanelText class="bg-transparent px-2 pb-6 pt-2">

                <VRow>
                  <VCol cols="12" sm="6" md="3">
                    <VCard
                      class="h-100 pa-4 cursor-pointer outline-none transition-all d-flex flex-column position-relative"
                      variant="outlined"
                      :style="selectedImportMethod === 'linkedin' ? 'background-color: rgba(var(--v-theme-primary), 0.03); border-color: rgba(var(--v-theme-primary), 1) !important;' : 'border-color: rgba(var(--v-theme-on-surface), 0.08) !important; background-color: rgba(var(--v-theme-on-surface), 0.04);'"
                      @click="() => { selectedImportMethod = 'linkedin'; currentStep = 2; }">
                      <div v-if="selectedImportMethod === 'linkedin'"
                        class="position-absolute bg-primary rounded d-flex align-center justify-center"
                        style=" block-size: 18px; inline-size: 18px;inset-block-start: 10px; inset-inline-end: 10px;">
                        <VIcon icon="tabler-check" size="14" color="white" />
                      </div>

                      <VIcon icon="tabler-brand-linkedin"
                        :color="selectedImportMethod === 'linkedin' ? 'primary' : 'secondary'" size="22"
                        class="mb-3 opacity-80" />
                      <div class="text-subtitle-2 text-sm-subtitle-1 font-weight-bold mb-2 leading-tight"
                        :style="{ color: selectedImportMethod === 'linkedin' ? '#4b465c' : '#a8a3b5' }">
                        LinkedIn Search</div>
                      <div class="text-caption mt-auto" style="color: #a8a3b5; line-height: 1.4;">
                        (Basic, Sales Nav, Post, Group or Event URL)
                      </div>
                    </VCard>
                  </VCol>
                  <VCol cols="12" sm="6" md="3">
                    <VCard
                      class="h-100 pa-4 cursor-pointer outline-none transition-all d-flex flex-column position-relative"
                      variant="outlined"
                      :style="selectedImportMethod === 'csv' ? 'background-color: rgba(var(--v-theme-primary), 0.03); border-color: rgba(var(--v-theme-primary), 1) !important;' : 'border-color: rgba(var(--v-theme-on-surface), 0.08) !important; background-color: rgba(var(--v-theme-on-surface), 0.04);'"
                      @click="() => { selectedImportMethod = 'csv'; currentStep = 2; }">
                      <div v-if="selectedImportMethod === 'csv'"
                        class="position-absolute bg-primary rounded d-flex align-center justify-center"
                        style=" block-size: 18px; inline-size: 18px;inset-block-start: 10px; inset-inline-end: 10px;">
                        <VIcon icon="tabler-check" size="14" color="white" />
                      </div>

                      <VIcon icon="tabler-calendar-event"
                        :color="selectedImportMethod === 'csv' ? 'primary' : 'secondary'" size="22"
                        class="mb-3 opacity-80" />
                      <div class="text-subtitle-2 text-sm-subtitle-1 font-weight-bold mb-2 leading-tight"
                        :style="{ color: selectedImportMethod === 'csv' ? '#4b465c' : '#a8a3b5' }">
                        Upload CSV File
                      </div>
                      <div class="text-caption mt-auto" style="color: #a8a3b5; line-height: 1.4;">
                        Upload LinkedIn profiles via CSV. <span
                          class="text-primary text-decoration-none cursor-pointer">Download Sample</span>
                      </div>
                    </VCard>
                  </VCol>
                  <VCol cols="12" sm="6" md="3">
                    <VCard
                      class="h-100 pa-4 cursor-pointer outline-none transition-all d-flex flex-column position-relative"
                      variant="outlined"
                      :style="selectedImportMethod === 'lookalike' ? 'background-color: rgba(var(--v-theme-primary), 0.03); border-color: rgba(var(--v-theme-primary), 1) !important;' : 'border-color: rgba(var(--v-theme-on-surface), 0.08) !important; background-color: rgba(var(--v-theme-on-surface), 0.04);'"
                      @click="() => { selectedImportMethod = 'lookalike'; currentStep = 2; }">
                      <div v-if="selectedImportMethod === 'lookalike'"
                        class="position-absolute bg-primary rounded d-flex align-center justify-center"
                        style=" block-size: 18px; inline-size: 18px;inset-block-start: 10px; inset-inline-end: 10px;">
                        <VIcon icon="tabler-check" size="14" color="white" />
                      </div>

                      <VIcon icon="tabler-users" :color="selectedImportMethod === 'lookalike' ? 'primary' : 'secondary'"
                        size="22" class="mb-3 opacity-80" />
                      <div class="text-subtitle-2 font-weight-bold mb-2 leading-tight"
                        :style="{ color: selectedImportMethod === 'lookalike' ? '#4b465c' : '#a8a3b5' }">
                        Lookalike<br>Audience
                      </div>
                      <div class="text-caption mt-auto" style="color: #a8a3b5; line-height: 1.4;">
                        Upload your best profiles, let AI find their lookalikes.
                      </div>
                    </VCard>
                  </VCol>
                  <VCol cols="12" sm="6" md="3">
                    <VCard
                      class="h-100 pa-4 cursor-pointer outline-none transition-all d-flex flex-column position-relative"
                      variant="outlined"
                      :style="selectedImportMethod === 'webhook' ? 'background-color: rgba(var(--v-theme-primary), 0.03); border-color: rgba(var(--v-theme-primary), 1) !important;' : 'border-color: rgba(var(--v-theme-on-surface), 0.08) !important; background-color: rgba(var(--v-theme-on-surface), 0.04);'"
                      @click="() => { selectedImportMethod = 'webhook'; currentStep = 2; }">
                      <div v-if="selectedImportMethod === 'webhook'"
                        class="position-absolute bg-primary rounded d-flex align-center justify-center"
                        style=" block-size: 18px; inline-size: 18px;inset-block-start: 10px; inset-inline-end: 10px;">
                        <VIcon icon="tabler-check" size="14" color="white" />
                      </div>

                      <VIcon icon="tabler-api-app" :color="selectedImportMethod === 'webhook' ? 'primary' : 'secondary'"
                        size="22" class="mb-3 opacity-80" />
                      <div class="text-subtitle-2 font-weight-bold mb-2 leading-tight"
                        :style="{ color: selectedImportMethod === 'webhook' ? '#4b465c' : '#a8a3b5' }">
                        Inbound<br>Webhook</div>
                      <div class="text-caption mt-auto" style="color: #a8a3b5; line-height: 1.4;">
                        Sync leads from zapier, n8n make in real time
                      </div>
                    </VCard>
                  </VCol>
                </VRow>

              </VExpansionPanelText>
            </VExpansionPanel>
          </VExpansionPanels>
          <div v-if="selectedImportMethod === 'linkedin'" class="animate-fade-in">
            <VExpansionPanels v-model="panelUpload" variant="accordion"
              class="border rounded-lg elevation-0 bg-transparent mb-6">
              <VExpansionPanel elevation="0" class="bg-transparent" :value="0">
                <VExpansionPanelTitle class="font-weight-medium text-subtitle-2 py-4"
                  style="color: #4b465c; font-size: 0.95rem !important;">
                  <div class="d-flex align-center gap-3">
                    Paste LinkedIn Search URL
                  </div>
                </VExpansionPanelTitle>
                <VExpansionPanelText class="bg-transparent px-1 px-sm-2 pb-4 pt-2">
                  <VCard variant="outlined" class="pa-3 pa-sm-4 elevation-0 mb-2"
                    style=" border-color: rgba(var(--v-theme-on-surface), 0.08) !important;border-radius: 8px;">
                    <div class="d-flex flex-column gap-3">
                      <div class="d-flex flex-column flex-md-row align-start align-md-center justify-space-between w-100 gap-2">
                        <div class="d-flex align-start flex-grow-1">
                          <VIcon icon="tabler-brand-linkedin" color="primary" size="20" class="me-2 mt-1" />
                          <span class="text-body-2 text-high-emphasis">
                            Find your target audience with
                            <a href="#" class="text-primary text-decoration-underline"
                              style="font-weight: 500;">LinkedIn
                              Search</a> or
                            <a href="#" class="text-primary text-decoration-underline" style="font-weight: 500;">Sales
                              Navigator</a> or
                            <a href="#" class="text-primary text-decoration-underline" style="font-weight: 500;">Post
                              URL</a> or
                            <a href="#" class="text-primary text-decoration-underline" style="font-weight: 500;">Group
                              URL</a>
                          </span>
                        </div>
                        <div class="d-flex align-center cursor-pointer ms-0 ms-md-4">
                          <VIcon icon="tabler-info-circle" size="16" color="primary" class="me-1" />
                          <span class="text-primary text-decoration-underline text-body-2"
                            style="font-size: 0.8rem;">Search
                            Guide</span>
                        </div>
                      </div>

                      <div class="d-flex flex-column flex-sm-row gap-2 gap-sm-4 mt-2">
                        <VTextField density="compact" hide-details
                          placeholder="https://www.linkedin.com/search/results/people/?keywords="
                          class="flex-grow-1 bg-surface rounded" variant="outlined" />
                        <VBtn color="primary" class="text-none px-6 rounded w-100 w-sm-auto" elevation="0">Validate</VBtn>
                      </div>

                      <div class="d-flex align-center mt-1">
                        <VIcon icon="tabler-point-filled" color="primary" size="12" class="me-1" />
                        <span class="text-caption text-disabled" style="font-size: 0.75rem;">Paste the search URL
                          directly from
                          LinkedIn</span>
                      </div>
                    </div>
                  </VCard>
                </VExpansionPanelText>
              </VExpansionPanel>
            </VExpansionPanels>
          </div>
          <div v-show="selectedImportMethod === 'csv'" class="animate-fade-in">
            <VExpansionPanels v-model="panelUpload" variant="accordion"
              class="border rounded-lg elevation-0 bg-transparent mb-6">
              <VExpansionPanel elevation="0" class="bg-transparent" :value="0">
                <VExpansionPanelTitle class="font-weight-medium text-subtitle-2 py-4"
                  style="color: #4b465c; font-size: 0.95rem !important;">
                  <div class="d-flex align-center gap-3">
                    Upload CSV File
                    <VChip v-if="currentStep >= 2" size="small" color="secondary" variant="tonal"
                      class="rounded font-weight-medium px-2" style=" block-size: 20px;font-size: 0.7rem;">Step 1 of 2
                    </VChip>
                  </div>
                </VExpansionPanelTitle>
                <VExpansionPanelText class="bg-transparent px-2 pb-2 pt-2">
                  <div
                    class="d-flex flex-column align-center justify-center pa-8 rounded-lg cursor-pointer transition-all hover-opacity"
                    style="border: 1.5px dashed rgba(var(--v-theme-primary), 0.5); background-color: rgba(var(--v-theme-primary), 0.02);">
                    <!-- Small upload icon in a faint primary square box -->
                    <div class="d-flex align-center justify-center rounded mb-3"
                      style=" background-color: #eaebfa; block-size: 36px;inline-size: 36px;">
                      <VIcon icon="tabler-upload" color="primary" size="20" />
                    </div>
                    <div class="text-body-2 mb-1">
                      <span style="color: rgba(var(--v-theme-primary), 0.8);">Drag a File or </span>
                      <span class="text-primary font-weight-medium">click a browse</span>
                    </div>
                    <div class="text-caption text-disabled">File with up to 100 rows works best</div>
                  </div>

                  <div class="mt-4 mb-2 d-flex align-center cursor-pointer hover-opacity w-fit">
                    <VIcon icon="tabler-cloud-download" color="primary" size="18" class="me-2" />
                    <span class="text-caption" style="color: #4b465c;">Download a sample file</span>
                  </div>
                </VExpansionPanelText>
              </VExpansionPanel>
            </VExpansionPanels>
          </div>
          <div v-if="currentStep === 3" class="animate-fade-in mt-2 mb-4">
            <VCard variant="outlined" class="pa-4 pa-sm-6 elevation-0"
              style=" border-color: rgba(var(--v-theme-on-surface), 0.08) !important;border-radius: 8px;">

              <div class="d-flex flex-column flex-sm-row justify-space-between align-start mb-6 gap-4">
                <div>
                  <h2 class="text-h6 text-sm-h5 font-weight-medium mb-1" style="color: #4b465c; line-height: 1.2;">Map Properties</h2>
                  <div class="d-flex align-center text-subtitle-2 mt-2" style="color: #64748b;">
                    <VIcon icon="tabler-check" color="primary" size="16" class="me-2" />
                    Make sure file includes contact name and phone number
                  </div>
                </div>
                <VIcon icon="tabler-trash" color="error" size="22" class="cursor-pointer hover-opacity align-self-end align-self-sm-start" />
              </div>

              <div class="mapping-container">
                <div class="w-100 border-b-xs border-e-sm">
                  <div class="mapping-table-header">
                    <div class="font-weight-bold text-subtitle-2 flex-grow-1" style="color: #4b465c;">Contact Field
                    </div>
                    <div class="font-weight-bold text-subtitle-2 flex-grow-1" style="color: #4b465c;">CSV Column</div>
                  </div>
                  <div class="pa-3 pa-sm-4 d-flex flex-column gap-3">
                    <div class="mapping-table-row">
                      <VCard variant="outlined" class="mapping-card d-flex align-center rounded-lg"
                        style=" border-color: rgba(var(--v-theme-success), 0.3) !important;">
                        <VIcon icon="tabler-list" color="success" size="18" class="me-3" />
                        <span class="text-body-2" style="color: #4b465c;">Full name</span>
                      </VCard>
                      <VCard variant="outlined"
                        class="mapping-card d-flex align-center justify-space-between rounded-lg bg-surface"
                        style=" border-color: rgba(var(--v-theme-secondary), 0.2) !important;">
                        <div class="d-flex align-center">
                          <VIcon icon="tabler-user" color="secondary" size="18" class="me-3" />
                          <span class="text-body-2" style="color: #4b465c;">Full name</span>
                        </div>
                        <span class="text-caption text-disabled">(35)</span>
                      </VCard>
                    </div>
                    <div class="mapping-table-row">
                      <VCard variant="outlined" class="mapping-card d-flex align-center rounded-lg"
                        style=" border-color: rgba(var(--v-theme-success), 0.3) !important;">
                        <VIcon icon="tabler-list" color="success" size="18" class="me-3" />
                        <span class="text-body-2" style="color: #4b465c;">First name</span>
                      </VCard>
                      <VCard variant="outlined"
                        class="mapping-card d-flex align-center justify-space-between rounded-lg bg-surface"
                        style=" border-color: rgba(var(--v-theme-secondary), 0.2) !important;">
                        <div class="d-flex align-center">
                          <VIcon icon="tabler-user" color="secondary" size="18" class="me-3" />
                          <span class="text-body-2" style="color: #4b465c;">First name</span>
                        </div>
                        <span class="text-caption text-disabled">(3)</span>
                      </VCard>
                    </div>
                    <div class="mapping-table-row">
                      <VCard variant="outlined" class="mapping-card d-flex align-center rounded-lg"
                        style=" border-color: rgba(var(--v-theme-success), 0.3) !important;">
                        <VIcon icon="tabler-list" color="success" size="18" class="me-3" />
                        <span class="text-body-2" style="color: #4b465c;">Last name</span>
                      </VCard>
                      <VCard variant="outlined"
                        class="mapping-card d-flex align-center justify-space-between rounded-lg bg-surface"
                        style=" border-color: rgba(var(--v-theme-secondary), 0.2) !important;">
                        <div class="d-flex align-center">
                          <VIcon icon="tabler-user" color="secondary" size="18" class="me-3" />
                          <span class="text-body-2" style="color: #4b465c;">Last name</span>
                        </div>
                        <span class="text-caption text-disabled">(12)</span>
                      </VCard>
                    </div>
                    <div class="mapping-table-row">
                      <VCard variant="outlined" class="mapping-card d-flex align-center rounded-lg"
                        style=" border-color: rgba(var(--v-theme-success), 0.3) !important;">
                        <VIcon icon="tabler-list" color="success" size="18" class="me-3" />
                        <span class="text-body-2" style="color: #4b465c;">Company Name</span>
                      </VCard>
                      <VCard variant="outlined"
                        class="mapping-card d-flex align-center justify-space-between rounded-lg bg-surface"
                        style=" border-color: rgba(var(--v-theme-secondary), 0.2) !important;">
                        <div class="d-flex align-center">
                          <VIcon icon="tabler-building" color="secondary" size="18" class="me-3" />
                          <span class="text-body-2" style="color: #4b465c;">Company Name</span>
                        </div>
                        <span class="text-caption text-disabled">(36)</span>
                      </VCard>
                    </div>
                    <div class="mapping-table-row">
                      <VCard variant="outlined" class="mapping-card d-flex align-center rounded-lg"
                        style=" border-color: rgba(var(--v-theme-success), 0.3) !important;">
                        <VIcon icon="tabler-list" color="success" size="18" class="me-3" />
                        <span class="text-body-2" style="color: #4b465c;">Position</span>
                      </VCard>
                      <VCard variant="outlined"
                        class="mapping-card d-flex align-center justify-space-between rounded-lg bg-surface"
                        style=" border-color: rgba(var(--v-theme-secondary), 0.2) !important;">
                        <div class="d-flex align-center">
                          <VIcon icon="tabler-briefcase" color="secondary" size="18" class="me-3" />
                          <span class="text-body-2" style="color: #4b465c;">Position</span>
                        </div>
                        <span class="text-caption text-disabled">(25)</span>
                      </VCard>
                    </div>
                    <div class="mapping-table-row">
                      <VCard variant="outlined" class="mapping-card d-flex align-center rounded-lg"
                        style=" border-color: rgba(var(--v-theme-success), 0.3) !important;">
                        <VIcon icon="tabler-list" color="success" size="18" class="me-3" />
                        <span class="text-body-2" style="color: #4b465c;">Headline</span>
                      </VCard>
                      <VCard variant="outlined"
                        class="mapping-card d-flex align-center justify-space-between rounded-lg bg-surface"
                        style=" border-color: rgba(var(--v-theme-secondary), 0.2) !important;">
                        <div class="d-flex align-center">
                          <VIcon icon="tabler-calendar" color="secondary" size="18" class="me-3" />
                          <span class="text-body-2" style="color: #4b465c;">Headline</span>
                        </div>
                        <span class="text-caption text-disabled">(25)</span>
                      </VCard>
                    </div>

                  </div>
                </div>
                <div class="pa-4 pa-sm-6 w-100">
                  <div class="d-flex align-center mb-4">
                    <div class="font-weight-bold text-subtitle-2" style="color: #4b465c;">Unmapped Works</div>
                  </div>

                  <VTextField density="compact" variant="outlined" placeholder="Search"
                    prepend-inner-icon="tabler-search" hide-details class="mb-4 bg-surface" />

                  <div class="d-flex flex-column gap-3 mb-6">
                    <VCard variant="outlined"
                      class="pa-2 d-flex align-center justify-space-between rounded-lg bg-surface"
                      style="border-color: rgba(var(--v-theme-primary), 0.3) !important;">
                      <div class="d-flex align-center">
                        <VIcon icon="tabler-menu-2" color="secondary" size="18" class="me-3" />
                        <span class="text-body-2" style="color: #4b465c;">Location (9)</span>
                      </div>
                      <span class="text-caption text-disabled">(3)</span>
                    </VCard>
                    <VCard variant="outlined"
                      class="pa-2 d-flex align-center justify-space-between rounded-lg bg-surface"
                      style="border-color: rgba(var(--v-theme-primary), 0.3) !important;">
                      <div class="d-flex align-center">
                        <VIcon icon="tabler-menu-2" color="secondary" size="18" class="me-3" />
                        <span class="text-body-2" style="color: #4b465c;">Industry (3)</span>
                      </div>
                      <span class="text-caption text-disabled">(3)</span>
                    </VCard>
                    <VCard variant="outlined"
                      class="pa-2 d-flex align-center justify-space-between rounded-lg bg-surface"
                      style="border-color: rgba(var(--v-theme-primary), 0.3) !important;">
                      <div class="d-flex align-center">
                        <VIcon icon="tabler-menu-2" color="secondary" size="18" class="me-3" />
                        <span class="text-body-2" style="color: #4b465c;">Notes (9)</span>
                      </div>
                      <span class="text-caption text-disabled">(9)</span>
                    </VCard>
                  </div>

                  <div class="text-end">
                    <span class="text-primary text-caption cursor-pointer hover-opacity">Clear All Matched</span>
                  </div>
                </div>

              </div>
            </VCard>
          </div>

        </div>
      </div>
      <div v-if="topStep === 2" class="d-flex flex-column flex-grow-1 animate-fade-in mb-6">
        <VCard variant="outlined" class="d-flex align-center px-3 px-sm-4 py-3 mb-6 elevation-0 flex-wrap gap-2"
          style=" border-color: rgba(var(--v-theme-on-surface), 0.12) !important;border-radius: 8px;">
          <div class="d-flex align-center cursor-pointer hover-opacity" @click="handlePrevious">
            <div class="rounded d-flex align-center justify-center me-0 me-sm-3"
              style=" background-color: rgba(var(--v-theme-primary), 0.1); block-size: 32px;inline-size: 32px;">
              <VIcon icon="tabler-list-check" size="18" color="primary" />
            </div>
            <span class="text-subtitle-2 font-weight-medium d-none d-sm-flex" style="color: #4b465c;">Define Target Audience</span>
            <span class="text-subtitle-2 font-weight-medium d-flex d-sm-none ms-2" style="color: #4b465c;">Targeting</span>
          </div>

          <VIcon icon="tabler-chevron-right" size="18" class="mx-1 mx-sm-4 text-disabled" />

          <div class="d-flex align-center">
            <div class="rounded d-flex align-center justify-center me-0 me-sm-3"
              style=" background-color: rgba(var(--v-theme-primary), 1); block-size: 32px; box-shadow: 0 4px 10px rgba(var(--v-theme-primary), 0.3);inline-size: 32px;">
              <VIcon icon="tabler-user-check" size="18" color="white" />
            </div>
            <span class="text-subtitle-2 font-weight-medium ms-2" style="color: #4b465c;">Sender Profiles</span>
          </div>
        </VCard>
        <div class="tab-campaign d-inline-flex flex-column flex-sm-row mb-4 rounded border overflow-hidden w-fit"
          style="border-color: rgba(var(--v-theme-primary), 0.4);">
          <div
            class="tab-campaign-item px-4 px-sm-6 d-flex align-center justify-center cursor-pointer font-weight-medium transition-all"
            style=" font-size: 0.9rem;min-block-size: 38px;"
            :style="selectedTab === 'linkedin'
              ? 'background-color: rgba(var(--v-theme-primary), 1); color: white;'
              : 'background-color: rgb(var(--v-theme-surface)); color: rgba(var(--v-theme-primary), 1);'"
            @click="selectedTab = 'linkedin'"
          >
            LinkedIn Profile
          </div>
          <div
            class="tab-campaign-item px-4 px-sm-6 d-flex align-center justify-center cursor-pointer font-weight-medium transition-all"
            style=" border-color: rgba(var(--v-theme-primary), 0.4); font-size: 0.9rem;min-block-size: 38px;"
            :style="selectedTab === 'email'
              ? 'background-color: rgba(var(--v-theme-primary), 1); color: white;'
              : 'background-color: rgb(var(--v-theme-surface)); color: rgba(var(--v-theme-primary), 1);'"
            @click="selectedTab = 'email'"
          >
            Email Accounts
          </div>

        </div>

        <VCard variant="outlined" class="flex-grow-1 elevation-0"
          style=" border-color: rgba(var(--v-theme-on-surface), 0.08) !important;border-radius: 8px;">
          <div class="pa-4 pa-sm-6 border-b transition-all">
            <div class="d-flex flex-column flex-sm-row justify-space-between align-start gap-4">
              <div class="d-flex flex-column">
                <div class="d-flex align-center mb-1">
                  <VIcon v-if="selectedTab === 'linkedin'" icon="tabler-brand-linkedin" color="primary" size="26"
                    class="me-2 rounded shadow-sm" style="box-shadow: 0 4px 10px rgba(0, 0, 0, 10%) !important;" />
                  <VIcon v-else icon="tabler-mail" color="error" size="26" class="me-2 rounded shadow-sm"
                    style="box-shadow: 0 4px 10px rgba(0, 0, 0, 10%) !important;" />
                  <h2 class="text-subtitle-1 text-sm-h6 font-weight-medium" style="color: #4b465c;">{{ selectedTab === 'linkedin' ? 'LinkedIn Profile' : 'Email Accounts' }}</h2>
                </div>
                <span class="text-body-2" style="color: #64748b;">Pick which {{ selectedTab === 'linkedin' ? 'LinkedIn profiles' : 'Email accounts' }} you want to use for this campaign.</span>
              </div>
              <VBtn color="primary" class="text-none rounded-lg font-weight-medium px-4 w-100 w-sm-auto" size="small" elevation="0"
                prepend-icon="tabler-plus" style="block-size: 34px;">
                Add Account
              </VBtn>
            </div>
            <div class="d-flex flex-column flex-sm-row align-center justify-space-between mt-8 gap-4">
              <div class="d-flex align-center gap-3 w-100 w-sm-auto">
                <span class="text-body-2" style="color: #a8a3b5;">Show</span>
                <div
                  class="d-flex align-center justify-center border rounded px-3 bg-surface cursor-pointer hover-opacity"
                  style=" border-color: rgba(var(--v-theme-on-surface), 0.08) !important;block-size: 32px; min-inline-size: 60px;">
                  <span class="text-body-2 me-6 text-disabled">10</span>
                  <VIcon icon="tabler-chevron-down" size="14" class="text-disabled" />
                </div>
              </div>

              <div class="w-100 w-sm-auto">
                <VTextField density="compact" variant="outlined" placeholder="Search" prepend-inner-icon="tabler-search"
                  hide-details style="max-inline-size: 100%; min-inline-size: 260px;" class="bg-surface w-100" />
              </div>
            </div>
          </div>

          <div class="overflow-x-auto w-100">
            <div style="min-inline-size: 900px;">
              <div class="d-flex align-center pa-4" style="background-color: rgba(var(--v-theme-on-surface), 0.05);">
                <div style="inline-size: 40px;" class="pl-2">
                  <div class="border rounded"
                    style=" border-color: rgba(var(--v-theme-on-surface), 0.15) !important; block-size: 18px;inline-size: 18px;">
                  </div>
                </div>
                <div class="flex-grow-1 font-weight-bold text-caption text-uppercase"
                  style="color: #64748b; min-inline-size: 250px;">Name</div>
                <div class="font-weight-bold text-caption text-uppercase" style="color: #64748b; inline-size: 15%;">Health
                </div>
                <div class="font-weight-bold text-caption text-uppercase" style="color: #64748b; inline-size: 25%;">Daily
                  Limits
                </div>
                <div class="font-weight-bold text-caption text-uppercase" style="color: #64748b; inline-size: 20%;">Account
                  Type
                </div>
                <div class="font-weight-bold text-caption text-uppercase" style="color: #64748b; inline-size: 15%;">Status
                </div>
              </div>
              <div class="d-flex align-center pa-3 pa-sm-4 border-b bg-surface hover-opacity cursor-pointer">
                <div style="inline-size: 40px;" class="pl-2">
                  <div class="border rounded cursor-pointer"
                    style=" border-color: rgba(var(--v-theme-on-surface), 0.15) !important; block-size: 18px;inline-size: 18px;">
                  </div>
                </div>
                <div class="flex-grow-1 d-flex align-center" style="min-inline-size: 250px;">
                  <VAvatar size="36" class="me-3">
                    <VImg src="https://i.pravatar.cc/150?u=a042581f4e29026704d" />
                </VAvatar>
                  <div class="d-flex flex-column">
                    <span class="font-weight-bold text-body-2" style="color: #4b465c;">Edgar Jones</span>
                    <span class="text-caption" style="color: #a8a3b5; font-size: 0.75rem !important;">{{ selectedTab === 'linkedin' ? '1,250 connections' :
                      'edgar@example.com' }}</span>
                  </div>
                </div>
                <div style="inline-size: 15%;">
                  <VProgressCircular :model-value="72" color="warning" :width="3" :size="38">
                    <span class="text-caption font-weight-bold" style="color: #4b465c;">72</span>
                  </VProgressCircular>
                </div>
                <div style="inline-size: 25%;">
                  <div class="border rounded px-3 py-1 bg-surface text-caption font-weight-medium d-inline-block"
                    style="border-color: rgba(var(--v-theme-on-surface), 0.2) !important; color: #4b465c;">
                    {{ selectedTab === 'linkedin' ? 'Invites: 40 / day' : 'Emails: 150 / day' }}
                  </div>
                </div>
                <div style="inline-size: 20%;" class="d-flex align-center">
                  <div :class="selectedTab === 'linkedin' ? 'bg-warning' : 'bg-error'"
                    class="rounded d-flex align-center justify-center me-3 font-weight-bold text-white shadow-sm"
                    style=" block-size: 22px; font-size: 0.7rem;inline-size: 22px;">
                    <span v-if="selectedTab === 'linkedin'">in</span>
                    <VIcon v-else icon="tabler-mail" size="14" />
                  </div>
                  <span class="text-body-2 font-weight-regular" style="color: #a8a3b5;">{{ selectedTab === 'linkedin' ?
                    'Premium'
                    : 'Gmail/GSuite' }}</span>
                </div>
                <div style="inline-size: 15%;">
                  <VChip size="small" color="success" variant="tonal" class="font-weight-bold px-3 py-1">
                    Connected
                  </VChip>
                </div>
              </div>
            </div>
          </div>

        </VCard>
      </div>
      <div class="mt-auto d-flex flex-column flex-sm-row justify-end pt-4 gap-4" :class="topStep === 1 ? 'border-t' : ''">
        <VBtn v-if="topStep === 2" variant="text" color="primary" class="px-5 rounded-lg text-none w-100 w-sm-auto"
          prepend-icon="tabler-arrow-back-up" style="font-weight: 500;" @click="handlePrevious">Previous</VBtn>
        <VBtn color="primary" class="px-8 rounded-lg text-none w-100 w-sm-auto" elevation="0" style="font-weight: 500;"
          @click="handleNext">{{
            topStep === 1 ? 'Next' : 'Submit' }}</VBtn>
      </div>

    </VCard>
    <VDialog v-model="isLookalikeDialogOpen" max-width="520">
      <VCard style="border-radius: 12px; box-shadow: 0 4px 24px 0 rgba(34, 41, 47, 10%);" class="">
        <VCardItem class="pa-6 pb-2 pt-2" style="background: rgba(var(--v-theme-on-surface), 0.03);">
          <div class="d-flex justify-space-between align-start">
            <div>
              <VCardTitle class="text-h6 font-weight-medium" style="color: #4b465c;">Lookalikes</VCardTitle>
              <VCardSubtitle class="text-subtitle-2 mt-1" style="color: #64748b;">Select a lookalike list for this
                campaign
              </VCardSubtitle>
            </div>
            <IconBtn @click="isLookalikeDialogOpen = false" class="ms-4 mt-3">
              <VIcon icon="tabler-circle-x" size="24" class="text-disabled" />
            </IconBtn>
          </div>
        </VCardItem>
        <VCardText v-if="!hasLookalikeLeads"
          class="d-flex flex-column align-center justify-center pa-10 animate-fade-in" style="min-block-size: 300px;">
          <h2 class="text-h5 font-weight-medium mb-3" style="color: #4b465c;">You don't have any leads</h2>
          <p class="text-body-2 mb-6" style="color: #64748b;">Create a lead list to start running campaigns</p>
          <VBtn color="primary" class="px-6 text-none rounded-lg" elevation="0" @click="hasLookalikeLeads = true">Create
            a
            List</VBtn>
        </VCardText>

        <VCardText v-else class="pa-6 pt-2 animate-fade-in">
          <div class="d-flex flex-column gap-3 mb-2">
            <VCard variant="outlined"
              class="pa-4 d-flex align-center justify-space-between cursor-pointer rounded-lg transition-all"
              :style="selectedLookalikeList === 'founder' ? 'border-color: rgba(var(--v-theme-primary), 1) !important; background-color: rgba(var(--v-theme-primary), 0.03);' : 'border-color: rgba(var(--v-theme-primary), 0.2) !important;'"
              @click="selectedLookalikeList = 'founder'">
              <div class="d-flex align-center flex-grow-1 overflow-hidden">
                <VIcon icon="tabler-list" color="secondary" size="20" class="me-3 flex-shrink-0" />
                <div class="d-flex flex-column flex-sm-row align-start align-sm-center flex-grow-1 overflow-hidden">
                  <span class="font-weight-bold text-body-1 me-2 text-truncate" style="color: #4b465c; max-inline-size: 100%;">Founder</span>
                  <span class="text-caption text-truncate" style="color: #a8a3b5; max-inline-size: 100%;">(1000+ Leads)</span>
                </div>
              </div>
              <div v-if="selectedLookalikeList === 'founder'"
                class="bg-primary rounded d-flex align-center justify-center flex-shrink-0 ms-2"
                style=" block-size: 18px;inline-size: 18px;">
                <VIcon icon="tabler-check" color="white" size="14" />
              </div>
            </VCard>
            <VCard variant="outlined"
              class="pa-4 d-flex align-center justify-space-between cursor-pointer rounded-lg transition-all"
              :style="selectedLookalikeList === 'tech' ? 'border-color: rgba(var(--v-theme-primary), 1) !important; background-color: rgba(var(--v-theme-primary), 0.03);' : 'border-color: rgba(var(--v-theme-primary), 0.2) !important;'"
              @click="selectedLookalikeList = 'tech'">
              <div class="d-flex align-center flex-grow-1 overflow-hidden">
                <VIcon icon="tabler-list" color="secondary" size="20" class="me-3 flex-shrink-0" />
                <div class="d-flex flex-column flex-sm-row align-start align-sm-center flex-grow-1 overflow-hidden">
                  <span class="font-weight-bold text-body-1 me-2 text-truncate" style="color: #4b465c; max-inline-size: 100%;">Tech Profiles</span>
                  <span class="text-caption text-truncate" style="color: #a8a3b5; max-inline-size: 100%;">(1000+ Leads)</span>
                </div>
              </div>
              <div v-if="selectedLookalikeList === 'tech'" class="bg-primary rounded d-flex align-center justify-center flex-shrink-0 ms-2"
                style=" block-size: 18px;inline-size: 18px;">
                <VIcon icon="tabler-check" color="white" size="14" />
              </div>
            </VCard>
          </div>

          <div class="text-end mb-6 py-2">
            <span class="text-primary text-body-2 cursor-pointer hover-opacity font-weight-medium">Add New</span>
          </div>

          <VDivider class="mb-4 mx-n6" />

          <div class="d-flex flex-column flex-sm-row justify-end gap-3 pt-2">
            <VBtn color="secondary" variant="tonal" class="w-100 w-sm-auto px-6 text-none rounded-lg font-weight-medium"
              @click="isLookalikeDialogOpen = false; hasLookalikeLeads = false;">Cancel</VBtn>
            <VBtn color="primary" class="w-100 w-sm-auto px-6 text-none rounded-lg font-weight-medium" elevation="0"
              @click="handleLookalikeSelect">Select List</VBtn>
          </div>
        </VCardText>
      </VCard>
    </VDialog>
  </div>
</template>

<style scoped>
.animate-fade-in {
  animation: fade-in 0.3s ease-in-out;
}

@keyframes fade-in {
  from {
    opacity: 0;
    transform: translateY(5px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.hover-opacity:hover {
  opacity: 0.85;
}

.w-fit {
  inline-size: fit-content;
}
</style>
