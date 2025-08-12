<script setup>
import { computed, ref, inject } from 'vue'
import MediaLibraryModal from '../../../Modals/MediaLibraryModal.vue'
import { sharedPageBuilderStore } from '../../../../stores/shared-store'
import { delay } from '../../../../composables/delay'
import { useTranslations } from '../../../../composables/useTranslations'

const { translate } = useTranslations()

// Use shared store instance
const pageBuilderStateStore = sharedPageBuilderStore
const customMediaComponent = inject('CustomMediaComponent')

const getIsLoadingAudio = ref(false)

const showMediaLibraryModal = ref(false)
// modal content
const titleMedia = ref('')
const descriptionMedia = ref('')
const firstButtonMedia = ref('')
const secondButtonMedia = ref(null)
const thirdButtonMedia = ref(null)
// set dynamic modal handle functions
const firstMediaButtonFunction = ref(null)

// get current audio from store
const getBasePrimaryAudio = computed(() => {
  if (pageBuilderStateStore.getBasePrimaryAudio) {
    loadingAudio(pageBuilderStateStore.getBasePrimaryAudio)
  }
  return pageBuilderStateStore.getBasePrimaryAudio
})

const handleAddAudio = function () {
  // open modal to true
  showMediaLibraryModal.value = true

  titleMedia.value = translate('Media Library')
  descriptionMedia.value = null
  firstButtonMedia.value = translate('Close')
  secondButtonMedia.value = translate(' Select audio')

  // handle click
  firstMediaButtonFunction.value = function () {
    showMediaLibraryModal.value = false
  }
  //
  // end modal
}

const loadingAudio = async function (audioURL) {
  getIsLoadingAudio.value = true

  if (audioURL && typeof audioURL === 'string' && audioURL.length > 2) {
    await delay(200)
    getIsLoadingAudio.value = false
  }
}
</script>
<template>
  <div>
    <div v-show="getIsLoadingAudio">
      <div class="pbx-flex pbx-items-center pbx-justify-center pbx-mt-4 pbx-min-h-80">
        <div
          class="pbx-inline-block pbx-h-8 pbx-w-8 pbx-animate-spin pbx-rounded-full pbx-border-4 pbx-border-solid pbx-border-current pbx-border-r-transparent pbx-align-[-0.125em] motion-reduce:pbx-animate-[spin_1.5s_linear_infinite]"
        >
          <span
            class="!pbx-absolute !pbx-m-px !pbx-h-px !pbx-w-px !pbx-overflow-hidden !pbx-whitespace-nowrap !pbx-border-0 !pbx-p-0 !pbx-[clip:rect(0,0,0,0)]"
            >Loading...</span
          >
        </div>
      </div>
    </div>
    <div v-show="getBasePrimaryAudio && !getIsLoadingAudio">
      <audio
        controls
        preload="metadata"
        controlsList="nodownload"
        class="pbx-object-cover pbx-object-center pbx-w-full pbx-cursor-pointer"
        :src="getBasePrimaryAudio"
        crossorigin="anonymous"
        @click="handleAddAudio"
        alt="audio"
      />
    </div>
    <MediaLibraryModal
      :open="showMediaLibraryModal"
      :title="titleMedia"
      :description="descriptionMedia"
      :firstButtonText="firstButtonMedia"
      :secondButtonText="secondButtonMedia"
      :thirdButtonText="thirdButtonMedia"
      :customMediaComponent="customMediaComponent"
      @firstMediaButtonFunction="firstMediaButtonFunction"
    >
    </MediaLibraryModal>
  </div>
</template>
