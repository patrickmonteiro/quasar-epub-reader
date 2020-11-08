<template>
  <q-page class="flex flex-center">
    <img
      :style="$q.screen.lt.sm ? 'width: 100%' : 'width: 300px'"
      alt="Quasar logo"
      src="~assets/book_lover.svg"
    >
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex'
}
</script>
