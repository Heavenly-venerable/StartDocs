<script setup lang="ts">
const searchQuery = ref<string>("")
const recentSearchList = ref<string[]>([])

function addToRecentSearches(term: string): void {
  recentSearchList.value = [term, ...recentSearchList.value.filter(item => item !== term)].slice(0, 5)
  localStorage.setItem('recent-searches', JSON.stringify(recentSearchList.value))
}

function searchYouTube(): void {
  if (!searchQuery.value.trim()) return
  const encodedQuery = encodeURIComponent(searchQuery.value)
  addToRecentSearches(searchQuery.value)
  window.open(`https://www.youtube.com/results?search_query=${encodedQuery}`, "_blank")
}

onMounted(() => {
  const savedSearches = localStorage.getItem('recent-searches')
  if (savedSearches) recentSearchList.value = JSON.parse(savedSearches)
})
</script>

<template>
  <div class="p-4 bg-gradient-to-b from-gray-50 to-white rounded-xl border border-gray-200 shadow-sm">
    <div class="flex gap-2 mb-4">
      <UInput v-model="searchQuery" variant="outline" placeholder="Search..." class="flex-1" />
      <UButton @click="searchYouTube" icon="i-heroicons-magnifying-glass" square />
    </div>
    <h3 class="text-lg font-semibold text-gray-700 mb-2">Recent Search</h3>
    <div class="flex flex-wrap gap-2">
      <UBadge v-for="term in recentSearchList" :key="term" color="gray"
        class="bg-gray-100 text-gray-700 hover:bg-gray-200 transition-colors cursor-pointer" :label="term"
        @click="searchQuery = term; searchYouTube()" />
    </div>
  </div>
</template>

<style scoped></style>
