<template>
  <div id="app" class="min-h-screen bg-gray-50">
    <nav class="fixed top-3 left-0 w-full flex justify-center z-50">
      <div class="backdrop-blur-xl bg-white/60 border border-white/30 shadow-lg rounded-xl w-[calc(100%-24px)] max-w-6xl px-4">
        <div class="flex items-center h-14">
          <router-link to="/" class="text-xl font-bold text-primary no-underline">
            归属地查询
          </router-link>
          <div class="flex gap-3 ml-auto">
            <router-link to="/" :class="navLinkClass('/')">
              快速查询
            </router-link>
            <router-link to="/batch" :class="navLinkClass('/batch')">
              批量处理
            </router-link>
            <router-link to="/management" :class="navLinkClass('/management')">
              管理数据
            </router-link>
          </div>
        </div>
      </div>
    </nav>
    <div class="h-20"></div>

    <div class="container w-[calc(100%-24px)] max-w-6xl px-4 mx-auto">
      <router-view></router-view>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useRoute } from 'vue-router'
import appicon from '@/assets/images/appicon.png'

const route = useRoute()

const showNav = computed(() => !route.meta.hideNav)

// Glass nav buttons
const navLinkClass = (path) => {
  const active = route.path === path
  return [
    "px-4 py-2 rounded-lg text-sm font-medium transition-all no-underline",
    "backdrop-blur-md",
    active
      ? "bg-primary text-white shadow"
      : "text-gray-700 hover:bg-primary hover:text-white hover:shadow-sm"
  ]
}
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
}
</style>
