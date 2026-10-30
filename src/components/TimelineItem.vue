<script setup>
defineProps({ item: { type: Object, required: true } })
</script>

<template>
  <article class="timeline-item">
    <div class="timeline-date">{{ item.date }}</div>
    <div><h3>{{ item.title }}</h3><p class="place">{{ item.place }}</p><p class="detail">{{ item.detail }}</p></div>
    <span class="item-arrow" aria-hidden="true">↗</span>
  </article>
</template>
