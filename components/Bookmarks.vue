<script setup lang="ts">
type Bookmark = { label: string, url: string }
const toast = useToast()
const bookmarks = ref<Bookmark[]>([])
const showAddBookmarkModal = ref<boolean>(false)
const newBookmarkLabel = ref<string>("")
const newBookmarkURL = ref<string>("")
const isBookmarksLoading = ref<boolean>(true)

function openAddBookmarkModal() {
  showAddBookmarkModal.value = true
}

function loadBookmarks() {
  const saved = localStorage.getItem("bookmarks")
  if (saved) bookmarks.value = JSON.parse(saved)
}

function saveBookmarks() {
  localStorage.setItem("bookmarks", JSON.stringify(bookmarks.value))
}

function submitBookmark() {
  const label = newBookmarkLabel.value.trim()
  const url = newBookmarkURL.value.trim()
  if (label && url && url.startsWith("http")) {
    bookmarks.value.push({ label, url })
    saveBookmarks()
    newBookmarkLabel.value = ''
    newBookmarkURL.value = ''
    showAddBookmarkModal.value = false
  } else {
    toast.add({ color: "yellow", title: "Label dan URL harus valid.", timeout: 800 })
  }
}

function confirmRemoveBookmark(index: number) {
  let confirmed = false
  toast.add({
    color: "red",
    title: "Apakah kamu yakin ingin menghapus bookmark ini?",
    actions: [{
      color: "red",
      label: "Hapus", click: () => {
        confirmed = true
        deleteBookmark(index)
      }
    }],
    onClose: () => {
      if (!confirmed) {
        toast.add({ color: "yellow", title: "Dibatalkan", timeout: 800 })
      }
    }
  })
}

function deleteBookmark(index: number) {
  bookmarks.value.splice(index, 1)
  saveBookmarks()
}

function handleBookmarkClick(bookmark: Bookmark) {
  window.open(bookmark.url, '_blank')
}

onMounted(() => {
  setTimeout(() => {
    loadBookmarks()
    isBookmarksLoading.value = false
  }, 800);
})
</script>

<template>
  <div class="p-4 bg-gradient-to-b from-gray-50 to-white rounded-xl border border-gray-200 shadow-sm space-y-4">
    <div class="flex justify-between items-center">
      <h3 class="text-lg font-semibold text-gray-700">Bookmarks</h3>
      <UButton @click="openAddBookmarkModal" label="Add" icon="i-heroicons-plus" :trailing="true" />
    </div>
    <div v-if="isBookmarksLoading" class="flex flex-wrap gap-2">
      <USkeleton v-for="n in 5" :key="n" class="h-6 w-20" />
    </div>
    <ul v-else class="flex flex-wrap gap-2">
      <li v-for="(bm, index) in bookmarks" :key="index" class="flex items-center gap-1">
        <UBadge @click="handleBookmarkClick(bm)" :label="bm.label" color="gray"
          class="bg-gray-100 text-gray-700 hover:bg-gray-200 transition-colors cursor-pointer" />
        <UButton @click="confirmRemoveBookmark(index)" icon="i-heroicons-trash" color="red" variant="ghost"
          size="2xs" />
      </li>
    </ul>
  </div>
  <UModal v-model="showAddBookmarkModal"
    :ui="{ container: 'flex items-start justify-center', base: 'relative max-w-md w-full p-6 rounded-lg bg-white dark:bg-gray-900' }">
    <div class="p-4 space-y-4">
      <h3 class="text-lg font-semibold">Tambah Bookmark</h3>
      <UInput v-model="newBookmarkLabel" placeholder="Label (misal: GitHub)" />
      <UInput v-model="newBookmarkURL" placeholder="URL (harus diawali http/https)" />
      <div class="flex justify-end gap-2">
        <UButton color="gray" variant="soft" @click="showAddBookmarkModal = false">Batal</UButton>
        <UButton color="primary" @click="submitBookmark">Simpan</UButton>
      </div>
    </div>
  </UModal>
</template>

<style scoped></style>
