<script setup lang="ts">
import { computed, ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

// Step tracker: 1=Campaign, 2=Target Audience, 3=Campaign (workflow builder)
const currentStep = ref(1)

// ─── Step 1: Campaign Setup ─────────────────────────────────────────────────
const campaignName = ref('')
const campaignType = ref('LinkedIn Invitation')
const campaignTypeOptions = ['LinkedIn Invitation', 'Email Outreach', 'LinkedIn Message', 'LinkedIn + Email']
const outreachChannel = ref('Email Only')
const outreachChannels = ['Email Only', 'LinkedIn Only', 'LinkedIn + Email']

interface TimeSlot {
  label: string
  active: boolean
}

const sendingSchedules = ref<TimeSlot[]>([
  { label: 'Mon - Fri 9:00 AM - 5:00 PM', active: true },
])

const addNewTime = () => {
  sendingSchedules.value.push({ label: 'Mon - Fri 9:00 AM - 5:00 PM', active: false })
}

// ─── Step 2: Target Audience ────────────────────────────────────────────────
const linkedinUrl = ref('')
const csvFile = ref<File | null>(null)
const useWebhook = ref(false)
const isDragging = ref(false)

const onFileDrop = (e: DragEvent) => {
  isDragging.value = false
  const files = e.dataTransfer?.files
  if (files && files.length > 0) csvFile.value = files[0]
}

const onFileInput = (e: Event) => {
  const target = e.target as HTMLInputElement
  if (target.files && target.files.length > 0) csvFile.value = target.files[0]
}

// ─── Step 3: Campaign Workflow Builder ──────────────────────────────────────
interface FollowUp {
  id: number
  message: string
  waitMinutes: number
  waitHours: number
  waitDays: number
}

const connectionMessage = ref('Hi {{first_name}}..')
const followUps = ref<FollowUp[]>([
  { id: 1, message: 'Hi {{first_name}}..', waitMinutes: 3, waitHours: 3, waitDays: 3 },
])

const editingConnection = ref(false)
const editingFollowUp = ref<number | null>(null)

const addFollowUp = () => {
  followUps.value.push({
    id: Date.now(),
    message: 'Hi {{first_name}}..',
    waitMinutes: 3,
    waitHours: 3,
    waitDays: 3,
  })
}

const removeFollowUp = (id: number) => {
  followUps.value = followUps.value.filter(f => f.id !== id)
}

// ─── Stepper header config ───────────────────────────────────────────────────
const steps = [
  { step: 1, icon: 'tabler-list', label: 'Leads List' },
  { step: 2, icon: 'tabler-target', label: 'Target Audience' },
  { step: 3, icon: 'tabler-speakerphone', label: 'Campaign' },
]

const goBack = () => {
  if (currentStep.value > 1) currentStep.value--
  else router.push({ name: 'campaign' })
}

const goNext = () => {
  if (currentStep.value < 3) currentStep.value++
}

const launchCampaign = () => {
  // handle launch
  router.push({ name: 'campaign' })
}

const stepperStepLabel = computed(() => {
  if (currentStep.value === 1) return 'Campaign'
  if (currentStep.value === 2) return 'Target Audience'
  return 'Campaign'
})
</script>

<template>
  <div>
    <VCard elevation="0" class="rounded-lg" style="min-block-size: calc(100vh - 140px);">
      <VCard variant="outlined" class="d-flex align-center px-3 px-sm-4 py-3 ma-2 ma-sm-4 mb-0 justify-space-between elevation-0 flex-wrap gap-2"
        style=" border-color: rgba(var(--v-theme-on-surface), 0.12) !important;border-radius: 8px;">
        <template v-for="(s, i) in steps" :key="s.step">
          <div class="d-flex align-center">
            <div class="rounded d-flex align-center justify-center me-0 me-sm-3" :style="{
              blockSize: '32px',
              inlineSize: '32px',
              backgroundColor: currentStep === s.step
                ? 'rgba(var(--v-theme-primary), 1)'
                : 'rgba(var(--v-theme-primary), 0.1)',
              boxShadow: currentStep === s.step ? '0 4px 10px rgba(var(--v-theme-primary), 0.3)' : 'none',
            }">
              <VIcon :icon="s.icon" size="17" :color="currentStep === s.step ? 'white' : 'primary'" />
            </div>
            <span class="text-subtitle-2 font-weight-medium d-none d-md-flex"
              :style="{ color: '#4b465c' }">
              {{ s.label }}
            </span>
          </div>
          <VIcon v-if="i < steps.length - 1" icon="tabler-chevron-right" size="18" color="disabled" class="mx-1 mx-sm-4" />
        </template>
      </VCard>
      <div v-if="currentStep === 1" class="pa-4 pa-sm-6">
        <VRow>
          <VCol cols="12" md="6">
            <div class="mb-1 text-body-2 font-weight-medium" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">
              Campaign Name
            </div>
            <VTextField v-model="campaignName" placeholder="e.g. Founder Outreach – India" variant="outlined"
              density="compact" hide-details="auto" class="mb-1" />
            <div class="text-caption" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));">
              Give your workflow a clear name so you can find it later.
            </div>
          </VCol>
          <VCol cols="12" md="6">
            <div class="mb-1 text-body-2 font-weight-medium" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">
              Campaign Type
            </div>
            <VSelect v-model="campaignType" :items="campaignTypeOptions" variant="outlined" density="compact"
              hide-details="auto" class="mb-1" />
            <div class="text-caption" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));">
              Choose how this workflow will start.
            </div>
          </VCol>
          <VCol cols="12" md="6">
            <div class="mb-2 text-body-2 font-weight-medium" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">
              Outreach Channel
            </div>
            <div class="d-flex flex-column flex-sm-row"
              style=" overflow: hidden;border: 1px solid rgba(var(--v-theme-on-surface), 0.15); border-radius: 6px; inline-size: 100%;">
              <button v-for="ch in outreachChannels" :key="ch" class="flex-1 text-body-2 py-2 px-4" :style="{
                background: outreachChannel === ch ? 'rgba(var(--v-theme-primary), 0.1)' : 'transparent',
                color: outreachChannel === ch ? 'rgba(var(--v-theme-primary), 1)' : 'rgba(var(--v-theme-on-surface), 0.6)',
                border: 'none',
                borderRight: $vuetify.display.smAndUp && ch !== 'LinkedIn + Email' ? '1px solid rgba(var(--v-theme-on-surface), 0.15)' : 'none',
                borderBottom: !$vuetify.display.smAndUp && ch !== 'LinkedIn + Email' ? '1px solid rgba(var(--v-theme-on-surface), 0.15)' : 'none',
                cursor: 'pointer',
                fontWeight: outreachChannel === ch ? '600' : '400',
                transition: 'all 0.2s',
              }" @click="outreachChannel = ch">
                {{ ch }}
              </button>
            </div>
            <div class="text-caption mt-1" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));">
              Select how you want to reach your leads in this workflow.
            </div>
          </VCol>
          <VCol cols="12" md="6">
            <div class="mb-2 text-body-2 font-weight-medium" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">
              Sending Schedule
            </div>
            <div class="d-flex align-center gap-2 flex-wrap w-100">
              <div v-for="(slot, idx) in sendingSchedules" :key="idx"
                class="d-flex align-center gap-2 px-3 py-2 rounded"
                style="border: 1px solid rgba(var(--v-theme-on-surface), 0.15); background: transparent;">
                <span class="text-body-2" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">{{ slot.label }}</span>
                <VIcon icon="tabler-circle" size="16" color="disabled" />
              </div>
              <VBtn variant="outlined" color="primary" size="small" class="text-none" style="border-radius: 6px;"
                @click="addNewTime">
                + Add new time
              </VBtn>
            </div>
            <div class="text-caption mt-1" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));">
              Messages are sent only within your selected hours.
            </div>
          </VCol>
        </VRow>
        <div class="d-flex justify-end mt-8">
          <VBtn color="primary" class="px-8 text-none w-100 w-sm-auto" style="border-radius: 6px;" elevation="0" @click="goNext">
            Continue
          </VBtn>
        </div>
      </div>
      <div v-if="currentStep === 2" class="pa-4 pa-sm-6">
        <VRow align="stretch">
          <VCol cols="12" md="6">
            <VCard variant="outlined" class="pa-3 pa-sm-4 h-100" style="border-radius: 8px;">
              <div class="text-body-2 font-weight-semibold mb-3" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">
                Basic LinkedIn Search
              </div>
              <VTextField v-model="linkedinUrl" placeholder="https://www.linkedin.com/search/results/people/..."
                variant="outlined" density="compact" hide-details />
              <div class="text-caption mt-2" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));">
                Filter profiles in the
                <a href="#" class="text-primary text-decoration-none">LinkedIn search</a>
                box and paste the link here.
              </div>
            </VCard>
          </VCol>

          <VCol cols="12" sm="3">
            <VCard variant="outlined" class="pa-3 pa-sm-4 h-100 d-flex flex-column align-center justify-center"
              style="border-radius: 8px; min-block-size: 130px;">
              <div class="text-body-2 font-weight-semibold mb-3 align-self-start" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">
                Upload CSV File
              </div>
              <div class="d-flex flex-column align-center justify-center rounded flex-grow-1 w-100"
                style="border: 2px dashed rgba(var(--v-theme-primary), 0.4); background: rgba(var(--v-theme-primary), 0.03); cursor: pointer; min-block-size: 80px;"
                :style="{ borderColor: isDragging ? 'rgba(var(--v-theme-primary), 0.9)' : undefined }"
                @dragover.prevent="isDragging = true" @dragleave="isDragging = false" @drop.prevent="onFileDrop"
                @click="($refs.csvInput as HTMLInputElement)?.click()">
                <input ref="csvInput" type="file" accept=".csv" class="d-none" @change="onFileInput" />
                <VIcon icon="tabler-upload" size="22" color="primary" class="mb-1" />
                <span class="text-body-2 text-primary font-weight-medium text-center px-2">
                  {{ csvFile ? csvFile.name : 'Drop file here' }}
                </span>
              </div>
            </VCard>
          </VCol>
          <VCol cols="12" md="3">
            <VCard variant="outlined" class="pa-4 h-100" style="border-radius: 8px; min-block-size: 130px;">
              <div class="text-body-2 font-weight-semibold mb-3" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">
                Advanced options
              </div>
              <VCheckbox v-model="useWebhook" label="Use Webhook" density="compact" hide-details color="primary"
                class="mb-1" />
              <div class="text-caption" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity)); padding-inline-start: 28px;">
                Use a webhook to send leads automatically.
              </div>
            </VCard>
          </VCol>
        </VRow>
        <div class="d-flex flex-column flex-sm-row align-center justify-space-between mt-8 gap-4">
          <VBtn variant="text" class="text-none px-0" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));" @click="goBack">
            Back
          </VBtn>
          <div class="d-flex gap-3 w-100 w-sm-auto justify-end">
            <VBtn variant="outlined" color="secondary" class="text-none flex-grow-1 flex-sm-grow-0 px-6" style="border-radius: 6px;">
              Save as Draft
            </VBtn>
            <VBtn color="primary" class="text-none flex-grow-1 flex-sm-grow-0 px-8" elevation="0" style="border-radius: 6px;" @click="goNext">
              Continue
            </VBtn>
          </div>
        </div>
      </div>
      <div v-if="currentStep === 3" class="pa-4 pa-sm-6">
        <div class="d-flex flex-column" style=" position: relative;gap: 0;">
          <div class="timeline-item">
            <div class="timeline-connector">
              <div class="node" />
              <div class="line" />
            </div>
            <VCard variant="outlined" class="flex-grow-1 mb-0"
              style=" border-color: rgba(var(--v-theme-on-surface), 0.1);border-radius: 8px;">
              <div class="pa-3 pa-sm-4 d-flex align-center gap-2">
                <VIcon icon="tabler-stars" size="18" color="primary" />
                <span class="text-body-2 font-weight-semibold" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">Campaign Start</span>
              </div>
              <div class="px-4 pb-3 pt-2 text-caption" style="
                  background: rgba(var(--v-theme-primary), 0.03);
                  border-block-start: 1px solid rgba(var(--v-theme-on-surface), 0.08);
                  color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));">
                When a lead enters your target audience
              </div>
            </VCard>
          </div>
          <div class="timeline-item">
            <div class="timeline-connector">
              <div class="node" />
              <div class="line" />
            </div>
            <VCard variant="outlined" class="flex-grow-1 mt-3 mb-0"
              style=" border-color: rgba(var(--v-theme-on-surface), 0.12);border-radius: 8px;">
              <div class="pa-3 pa-sm-4 d-flex align-center justify-space-between flex-wrap gap-2">
                <div class="d-flex align-center gap-2">
                  <VIcon icon="tabler-brand-linkedin" size="18" color="primary" />
                  <span class="text-body-2 font-weight-semibold" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">
                    Send LinkedIn Connection Request
                  </span>
                </div>
                <div class="d-flex gap-2">
                  <VIcon icon="tabler-pencil" size="17" color="secondary" style="cursor: pointer;"
                    @click="editingConnection = !editingConnection" />
                  <VIcon icon="tabler-trash" size="17" color="error" style="cursor: pointer;" />
                </div>
              </div>
              <div class="px-4 pb-4"
                style="border-block-start: 1px solid rgba(var(--v-theme-on-surface), 0.06); padding-block-start: 10px;">
                <div v-if="!editingConnection" class="text-body-2 mb-3" style="color: #6b7280;">
                  {{ connectionMessage }}
                </div>
                <VTextarea v-else v-model="connectionMessage" variant="outlined" density="compact" hide-details rows="2"
                  class="mb-3" />
                <div class="d-flex flex-wrap gap-2">
                  <VBtn color="primary" variant="elevated" size="small" class="text-none px-4 flex-grow-1 flex-sm-grow-0" elevation="0"
                    style="border-radius: 6px;">
                    Edit Message
                  </VBtn>
                  <VBtn color="secondary" variant="outlined" size="small" class="text-none px-4"
                    style="border-radius: 6px;">
                    <VIcon icon="tabler-sparkles" size="15" class="me-1" />
                    Make with AI
                  </VBtn>
                </div>
              </div>
            </VCard>
          </div>
          <template v-for="(fu, idx) in followUps" :key="fu.id">
            <div class="timeline-item">
              <div class="timeline-connector">
                <div class="node" />
                <div class="line" />
              </div>
              <VCard variant="outlined" class="flex-grow-1 mt-3 mb-0"
                style=" border-color: rgba(var(--v-theme-on-surface), 0.12);border-radius: 8px;">
                <div class="pa-3 pa-sm-4 d-flex align-center justify-space-between flex-wrap gap-2">
                  <div class="d-flex align-center gap-2">
                    <VIcon icon="tabler-refresh" size="18" color="primary" />
                    <span class="text-body-2 font-weight-semibold" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));">
                      Set Follow-up message
                    </span>
                  </div>
                  <div class="d-flex gap-2">
                    <VIcon icon="tabler-pencil" size="17" color="secondary" style="cursor: pointer;"
                      @click="editingFollowUp = editingFollowUp === fu.id ? null : fu.id" />
                    <VIcon icon="tabler-trash" size="17" color="error" style="cursor: pointer;"
                      @click="removeFollowUp(fu.id)" />
                  </div>
                </div>
                <div class="px-4 pb-4"
                  style="border-block-start: 1px solid rgba(var(--v-theme-on-surface), 0.06); padding-block-start: 10px;">
                  <div v-if="editingFollowUp !== fu.id" class="text-body-2 mb-3" style="color: #6b7280;">
                    {{ fu.message }}
                  </div>
                  <VTextarea v-else v-model="fu.message" variant="outlined" density="compact" hide-details rows="2"
                    class="mb-3" />
                  <div class="d-flex gap-2 mb-3">
                    <VBtn color="primary" variant="elevated" size="small" class="text-none px-4" elevation="0"
                      style="border-radius: 6px;">
                      Edit Message
                    </VBtn>
                    <VBtn color="secondary" variant="outlined" size="small" class="text-none px-4"
                      style="border-radius: 6px;">
                      <VIcon icon="tabler-sparkles" size="15" class="me-1" />
                      Make with AI
                    </VBtn>
                  </div>
                  <div class="wait-duration-group px-3 py-2 mt-3"
                    style="border: 1px dashed rgba(var(--v-theme-on-surface), 0.12); border-radius: 8px; color: #6b7280; font-size: 13px;">
                    <span class="text-no-wrap">Once accepted wait</span>
                    <div class="d-flex align-center gap-2">
                      <VTextField v-model="fu.waitMinutes" type="number" variant="outlined" density="compact" hide-details
                        class="wait-field" />
                      <span>Minutes</span>
                    </div>
                    <div class="d-flex align-center gap-2">
                      <VTextField v-model="fu.waitHours" type="number" variant="outlined" density="compact" hide-details
                        class="wait-field" />
                      <span>Hour</span>
                    </div>
                    <div class="d-flex align-center gap-2">
                      <VTextField v-model="fu.waitDays" type="number" variant="outlined" density="compact" hide-details
                        class="wait-field" />
                      <span>days</span>
                    </div>
                  </div>
                </div>
              </VCard>
            </div>
          </template>
          <div class="timeline-item">
            <div class="timeline-connector">
              <div class="node" style="border-color: rgba(var(--v-theme-primary), 0.5); cursor: pointer;" @click="addFollowUp" />
              <div class="line" />
            </div>
            <VCard variant="outlined" class="flex-grow-1 mt-3 mb-0"
              style=" border-color: rgba(var(--v-theme-on-surface), 0.1);border-radius: 8px; background: rgba(var(--v-theme-primary), 0.02); cursor: pointer;"
              @click="addFollowUp">
              <div class="pa-4 d-flex align-center gap-2">
                <VIcon icon="tabler-circle-plus" size="18" color="primary" />
                <span class="text-body-2 font-weight-medium" style="color: rgba(var(--v-theme-primary), 1);">
                  Add new follow-up
                </span>
              </div>
            </VCard>
          </div>
          <div class="timeline-item">
            <div class="timeline-connector">
              <div class="node" style="border-color: rgba(var(--v-theme-on-surface), 0.2); margin-block-end: 4px;" />
            </div>
            <VCard variant="outlined" class="flex-grow-1 mt-3"
              style=" border-color: rgba(var(--v-theme-on-surface), 0.1);border-radius: 8px; background: rgba(var(--v-theme-on-surface), 0.02);">
              <div class="pa-4 d-flex align-center gap-2">
                <VIcon icon="tabler-flag" size="18" color="disabled" />
                <span class="text-body-2 font-weight-medium" style="color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));">
                  End of Campaign
                </span>
              </div>
            </VCard>
          </div>
        </div>
        <div class="d-flex flex-column flex-sm-row align-center justify-space-between mt-6 gap-4">
          <VBtn variant="text" class="text-none px-0 w-100 w-sm-auto mb-2 mb-sm-0" style="color: rgba(var(--v-theme-on-surface), var(--v-high-emphasis-opacity));" @click="goBack">
            Back
          </VBtn>
          <div class="d-flex flex-column flex-sm-row gap-3 w-100 w-sm-auto justify-end">
            <VBtn variant="outlined" color="secondary" class="text-none w-100 w-sm-auto px-6" style="border-radius: 6px;">
              Save as Draft
            </VBtn>
            <VBtn color="primary" class="text-none w-100 w-sm-auto px-8" elevation="0" style="border-radius: 6px;"
              @click="launchCampaign">
              Launch Campaign
            </VBtn>
          </div>
        </div>
      </div>
    </VCard>
  </div>
</template>

<style scoped>
button:focus {
  outline: none;
}

.wait-field :deep(.v-field__input) {
  display: flex !important;
  align-items: center !important;
  justify-content: center !important;
  padding: 0 !important;
  min-block-size: 24px !important;
  text-align: center !important;
}

.wait-field :deep(input) {
  padding: 0 !important;
  min-block-size: 24px !important;
  text-align: center !important;
}

.wait-field :deep(input::-webkit-outer-spin-button),
.wait-field :deep(input::-webkit-inner-spin-button) {
  margin: 0;
  appearance: none;
}

.wait-field :deep(input[type="number"]) {
  appearance: textfield;
}

.wait-field :deep(.v-field__outline) {
  --v-field-border-opacity: 0.15 !important;
}
</style>
