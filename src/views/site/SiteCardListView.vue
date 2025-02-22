<script lang="ts" setup>
import api from '@/api'
import type { Site } from '@/api/types'
import SiteCard from '@/components/cards/SiteCard.vue'
import NoDataFound from '@/components/NoDataFound.vue'
import SiteAddEditForm from '@/components/form/SiteAddEditForm.vue'

// 数据列表
const dataList = ref<Site[]>([])

// 是否刷新过
const isRefreshed = ref(false)

// 新增站点对话框
const siteAddDialog = ref(false)

// 获取站点列表数据
async function fetchData() {
  try {
    dataList.value = await api.get('site/')
    isRefreshed.value = true
  }
  catch (error) {
    console.error(error)
  }
}

// 加载时获取数据
onBeforeMount(fetchData)
</script>

<template>
  <div
    v-if="!isRefreshed"
    class="mt-12 w-full text-center text-gray-500 text-sm flex flex-col items-center"
  >
    <VProgressCircular
      v-if="!isRefreshed"
      size="48"
      indeterminate
      color="primary"
    />
  </div>
  <div
    v-if="dataList.length > 0"
    class="grid gap-3 grid-site-card"
  >
    <SiteCard
      v-for="data in dataList"
      :key="data.id"
      :site="data"
      @remove="fetchData"
      @update="fetchData"
    />
  </div>
  <NoDataFound
    v-if="dataList.length === 0 && isRefreshed"
    error-code="404"
    error-title="没有站点"
    error-description="已添加并支持的站点将会在这里显示。"
  />
  <!-- 新增站点按钮 -->
  <VBtn
    icon="mdi-plus"
    size="x-large"
    class="fixed right-5 bottom-5"
    oper="add"
    @click="siteAddDialog = true"
  />
  <SiteAddEditForm
    v-if="siteAddDialog"
    v-model="siteAddDialog"
    oper="add"
    @save="siteAddDialog = false; fetchData()"
    @close="siteAddDialog = false"
  />
</template>

<style lang="scss">
.grid-site-card {
  grid-template-columns: repeat(auto-fill, minmax(20rem, 1fr));
  padding-block-end: 1rem;
}
</style>
