<script setup lang="ts">
import { ref, watch } from 'vue'
import { useModalStore } from '@/stores/ModalAppointment'
import { ModalAppointmentSteps } from '@/types/enums/modalAppointmentSteps'
import ModalTemplate from '@templates/ModalTemplate.vue'

const modalStore = useModalStore()
const emit = defineEmits(['sendSmsCode'])

const model = defineModel<string>()

const codeRegex = /^\d{4}$/
const isVerifieCodeValid = ref<boolean>(false)
const isVerifieCodeEnter = ref<boolean>(false)

watch(
  () => model.value,
  (newValue) => {
    const valueToCheck = newValue ?? ''

    if (valueToCheck) {
      isVerifieCodeEnter.value = true
    }

    if (codeRegex.test(valueToCheck)) {
      isVerifieCodeValid.value = true
      modalStore.setValueIsProceedButtonEnableForAppointmentModal(false)
    } else {
      isVerifieCodeValid.value = false
      modalStore.setValueIsProceedButtonEnableForAppointmentModal(true)
    }
  }
)

// const changeModalStepForSmsVerifier = () => {
//     emit('sendSmsCode');
//     modalStore.changeCurrentStepFor(ModalAppointmentSteps.SMS_CODE);
// }
</script>

<template>
  <ModalTemplate :next-button-text="'Подтверждение'" :title="'Подтвердить'">
    <p>Введите код из Telegram или SMS.</p>
    <div class="my-3">
      <input type="text" v-model="model" class="form-control" />
    </div>
    <p v-if="modalStore.errorMessage" style="color: red">{{ modalStore.errorMessage }}</p>
    <p v-if="!isVerifieCodeValid && isVerifieCodeEnter" style="color: red">
      Код должен состоять из 4 цифр
    </p>
    <!-- <p class="appointment-agree" @click="changeModalStepForSmsVerifier">
            <p href="#">Получить код по SMS</p>
        </p> -->
  </ModalTemplate>
</template>
