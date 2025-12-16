<template>
  <div class="flex flex-col items-center justify-center min-h-[60vh] px-4">
    <div class="glass p-5 rounded-xl w-full max-w-md shadow-lg">
      <h2 class="text-lg font-semibold text-gray-800 text-center mb-3 tracking-wide">
        手机号归属地查询
      </h2>
      <div class="flex gap-2 mb-3">
        <input v-model="phone" type="text" placeholder="请输入手机号"
          class="flex-1 px-3 py-1.5 bg-transparent border-l-0 border-r-0 border-t-0 border-b border-gray-300 outline-none text-[14px] focus:border-b-2 focus:border-primary transition" />
        <div @click="handleSubmit"
          class="px-4 py-1.5 bg-primary text-white rounded-lg text-sm hover:bg-primary/80 transition cursor-pointer">
          查询
        </div>
      </div>
      <transition name="fade">
        <div v-if="result"
          class=" mt-3 p-4 rounded-lg bg-white/60 backdrop-blur-md border border-white/30 shadow-sm text-gray-800 text-[14px] leading-relaxed">
          <p><span class="font-medium">手机号：</span>{{ result.number }}</p>
          <p><span class="font-medium">省份：</span>{{ result.province }}</p>
          <p><span class="font-medium">城市：</span>{{ result.city }}</p>
          <p><span class="font-medium">运营商：</span>{{ result.carrier }}</p>
        </div>
      </transition>
    </div>
  </div>
  <Toast ref="toastRef" />
</template>

<script setup>
import { ref } from 'vue'
import Toast from '@/components/Toast.vue'
import { CarrierGet } from '../../wailsjs/go/main/App'
const toastRef = ref(null)

const phone = ref('')
const result = ref(null)

// 查询号码
const handleSubmit = () => {
  if (!/^1[3-9]\d{9}$/.test(phone.value)) {
    toastRef.value.showToast('请输入正确的手机号')
    return
  }
  CarrierGet(phone.value).then(res => {
    if (res.code == 0) {
      result.value = {
        number: phone.value,
        province: res.data.province,
        city: res.data.city,
        carrier: res.data.isp
      }
    } else {
      toastRef.value.showToast(res.message)
    }
  })
}
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.25s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
