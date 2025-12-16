<template>
  <div class="space-y-6 my-6 px-4">
    <section class="glass p-5 rounded-xl shadow w-full max-w-4xl mx-auto">
      <div class="flex gap-3 items-center">
        <input v-model="keyword" type="text" placeholder="搜索号码 / 省份 / 城市"
          class="flex-1 px-3 py-1.5 text-[14px] bg-transparent border-none border-b border-gray-300 outline-none focus:border-b-2 focus:border-primary transition" />
        <div @click="search"
          class="px-4 py-1.5 rounded-lg bg-primary text-white text-sm hover:bg-primary/80 cursor-pointer transition">
          搜索
        </div>
        <div @click="openAdd"
          class="px-4 py-1.5 rounded-lg bg-secondary text-white text-sm hover:bg-secondary/80 cursor-pointer transition">
          添加
        </div>
      </div>
    </section>
    <section class="glass p-5 rounded-xl shadow w-full max-w-4xl mx-auto">
      <table class="w-full border-collapse text-[14px]">
        <thead>
          <tr class="text-gray-600 border-b border-gray-200">
            <th class="text-left py-2 font-medium">号码段</th>
            <th class="text-left py-2 font-medium">省份</th>
            <th class="text-left py-2 font-medium">城市</th>
            <th class="text-left py-2 font-medium">运营商</th>
            <th class="text-left py-2 font-medium w-[80px]">操作</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in pagedData" :key="item.Key" class="border-b border-gray-100 hover:bg-gray-50/60 transition">
            <td class="py-2">{{ item.key }}</td>
            <td class="py-2">{{ item.province }}</td>
            <td class="py-2">{{ item.city }}</td>
            <td class="py-2">{{ item.isp }}</td>
            <td class="py-2">
              <div class="flex gap-3 text-primary">
                <div @click="openEdit(item)"
                  class="cursor-pointer hover:text-primary/70 i-material-symbols-edit-outline text-lg">
                </div>
                <div @click="openDelete(item)"
                  class="cursor-pointer hover:text-red-500 text-red-400 i-material-symbols-delete-outline text-lg">
                </div>
              </div>
            </td>
          </tr>
          <tr v-if="pagedData.length === 0">
            <td colspan="5" class="text-center py-6 text-gray-500">
              没有找到相关数据
            </td>
          </tr>
        </tbody>
      </table>
      <div class="flex justify-center mt-5 gap-2 text-[14px] select-none">
        <button @click="prevPage" :disabled="page === 1"
          class="px-3 py-1 rounded border border-gray-300 bg-white/60 hover:bg-gray-100 disabled:opacity-40">
          上一页
        </button>
        <span class="px-2 py-1 text-gray-700">
          {{ page }} / {{ totalPages }}
        </span>
        <button @click="nextPage" :disabled="page === totalPages"
          class="px-3 py-1 rounded border border-gray-300 bg-white/60 hover:bg-gray-100 disabled:opacity-40">
          下一页
        </button>
      </div>
    </section>
  </div>
  <Toast ref="toastRef" />
  <div v-if="showEditModal"
    class="fixed top-0 left-0 w-full h-full bg-black/40 flex items-center justify-center z-50 px-16px">
    <div class="bg-white rounded-12px w-full max-w-360px p-24px shadow space-y-20px">
      <div class="text-18px font-600">
        {{ editForm.mode === 'add' ? '添加号码段' : '编辑号码段' }}
      </div>
      <div class="space-y-16px">
        <div class="flex flex-col">
          <div class="text-14px text-gray-600">号码段</div>
          <input v-model="editForm.number" @blur="autoFetchCarrier" class="
            flex-1 py-2 mt-1
            bg-transparent
            border-l-0 border-r-0 border-t-0 border-b border-gray-300
            outline-none
            text-[14px]
            focus:border-b-2 focus:border-primary
            transition
          " />
        </div>
        <div class="flex flex-col">
          <div class="text-14px text-gray-600">省份</div>
          <input v-model="editForm.province" class="
            flex-1 py-2 mt-1
            bg-transparent
            border-l-0 border-r-0 border-t-0 border-b border-gray-300
            outline-none
            text-[14px]
            focus:border-b-2 focus:border-primary
            transition
          " />
        </div>
        <div class="flex flex-col">
          <div class="text-14px text-gray-600">城市</div>
          <input v-model="editForm.city" class="
            flex-1 py-2 mt-1
            bg-transparent
            border-l-0 border-r-0 border-t-0 border-b border-gray-300
            outline-none
            text-[14px]
            focus:border-b-2 focus:border-primary
            transition
          " />
        </div>
        <div class="flex flex-col">
          <div class="text-14px text-gray-600">运营商</div>
          <input v-model="editForm.carrier" class="
            flex-1 py-2 mt-1
            bg-transparent
            border-l-0 border-r-0 border-t-0 border-b border-gray-300
            outline-none
            text-[14px]
            focus:border-b-2 focus:border-primary
            transition
          " />
        </div>
      </div>
      <div class="flex justify-end gap-12px pt-12px">
        <div @click="showEditModal = false"
          class="px-16px py-8px rounded-8px border border-gray-300 text-gray-700 hover:bg-gray-100 cursor-pointer user-select-none">
          取消
        </div>
        <div @click="saveRecord"
          class="px-16px py-8px rounded-8px bg-primary text-white hover:bg-primary/80 cursor-pointer user-select-none">
          保存
        </div>
      </div>
    </div>
  </div>
  <div v-if="showDeleteConfirm"
    class="fixed top-0 left-0 w-full h-full bg-black/40 flex items-center justify-center z-50 px-16px">
    <div class="bg-white rounded-12px w-full max-w-300px p-24px shadow space-y-20px">
      <div class="text-18px font-600">确认删除？</div>
      <div class="text-gray-600 text-14px">删除后不可恢复。</div>
      <div class="flex justify-end gap-12px">
        <div @click="showDeleteConfirm = false"
          class="px-16px py-8px rounded-8px border border-gray-300 text-gray-700 hover:bg-gray-100 cursor-pointer user-select-none">
          取消
        </div>
        <div @click="deleteRecord"
          class="px-16px py-8px rounded-8px bg-red-500 text-white hover:bg-red-600 cursor-pointer user-select-none">
          删除
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import Toast from '@/components/Toast.vue'
import { CarrierList, CarrierGet, CarrierUpdate, CarrierDelete, CarrierCreate } from '../../wailsjs/go/main/App'

const toastRef = ref(null)
const pagedData = ref([]);
const keyword = ref('')
const page = ref(1)
const pageSize = ref(10)
const totalPages = ref(1)

// ----------- 弹窗相关 ----------------
const showEditModal = ref(false)
const showDeleteConfirm = ref(false)
const deleteTarget = ref(null)

const editForm = ref({
  mode: 'add', // add | edit
  number: '',
  province: '',
  city: '',
  carrier: '',
})

// 打开“添加”弹窗
const openAdd = () => {
  editForm.value = {
    mode: 'add',
    number: '',
    province: '',
    city: '',
    carrier: '',
  }
  showEditModal.value = true
}

// 打开“编辑”弹窗
const openEdit = (row) => {
  editForm.value = {
    mode: 'edit',
    number: row.key,
    province: row.province,
    city: row.city,
    carrier: row.isp,
  }
  showEditModal.value = true
}

// 号码段输入后自动获取数据
const autoFetchCarrier = () => {
  if (!editForm.value.number) return

  CarrierGet(editForm.value.number).then(res => {
    if (res.code == 0 && res.data) {
      editForm.value.province = res.data.province
      editForm.value.city = res.data.city
      editForm.value.carrier = res.data.isp
    } else {
      editForm.value.province = ''
      editForm.value.city = ''
      editForm.value.carrier = ''
    }
  })
}

// 保存（新增 / 修改）
const saveRecord = () => {
  CarrierGet(editForm.value.number).then(res => {
    if (res.code == 0) {
      CarrierUpdate(editForm.value.number, {
        province: editForm.value.province,
        city: editForm.value.city,
        isp: editForm.value.carrier,
      }).then(res => {
        if (res.code === 0) {
          toastRef.value.showToast("保存成功")
          showEditModal.value = false
          fetchData()
        } else {
          toastRef.value.showToast(res.message)
        }
      })
    } else {
      CarrierCreate({
        key: editForm.value.number,
        province: editForm.value.province,
        city: editForm.value.city,
        isp: editForm.value.carrier,
      }).then(res => {
        if (res.code === 0) {
          toastRef.value.showToast("添加成功")
          showEditModal.value = false
          fetchData()
        } else {
          toastRef.value.showToast(res.message)
        }
      })
    }
  })
}


// 打开删除确认弹窗
const openDelete = (row) => {
  deleteTarget.value = row
  showDeleteConfirm.value = true
}

// 删除记录
const deleteRecord = () => {
  CarrierDelete(deleteTarget.value.key).then(res => {
    if (res.code === 0) {
      toastRef.value.showToast("删除成功")
      showDeleteConfirm.value = false
      fetchData()
    } else {
      toastRef.value.showToast(res.message)
    }
  })
}


// ----------- 列表分页相关 ----------
const fetchData = () => {
  CarrierList(keyword.value.trim(), page.value, pageSize.value).then(res => {
    if (res.code == 0) {
      pagedData.value = res.data.list
      totalPages.value = Math.ceil(res.data.total / pageSize.value);
    } else {
      toastRef.value.showToast(res.message)
    }
  })
}

const search = () => {
  page.value = 1
  fetchData()
}

const prevPage = () => {
  if (page.value > 1) page.value--
  fetchData()
}

const nextPage = () => {
  if (page.value < totalPages.value) page.value++
  fetchData()
}

onMounted(() => {
  fetchData()
})
</script>
