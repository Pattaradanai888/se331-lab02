<script setup lang="ts">
import EventCard from '@/components/EventCard.vue'
import EventType from '@/components/EventType.vue'
import EventService from '@/services/EventService'
import type { Event } from '@/types'
import { ref, onMounted, watchEffect, computed } from 'vue'
const totalEvent = ref<number>(0)
const props = defineProps({
  page: {
    type: Number,
    required: true
  }
})

const events = ref<Event[]>(null)

onMounted(() => {
  watchEffect(() => {
    EventService.getEvents(2, props.page)
      .then((response) => {
        events.value = response.data
        totalEvent.value = response.headers['x-total-count']
      })
      .catch((error) => {
        console.error('There was an error!', error)
      })
  })
})
const hasNextPage = computed(() => {
  // first calculate the the total page
  const totalPages = Math.ceil(totalEvent.value / 2)
  return props.page.valueOf() < totalPages
})
</script>

<template>
  <h1>Events For Good</h1>
  <!-- new element -->
  <div class="events-container">
    <div class="events">
      <EventCard v-for="event in events" :key="event.id" :event="event" />
      <RouterLink
        :to="{ name: 'event-list', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1"
      >
        Prev Page
      </RouterLink>
      <RouterLink
        :to="{ name: 'event-list', query: { page: page + 1 } }"
        rel="next"
        v-if="hasNextPage"
      >
        Next Page
      </RouterLink>
    </div>
    <div class="events-type">
      <EventType v-for="event in events" :key="event.id" :event="event" />
    </div>
  </div>
</template>

<style scoped>
.events-container {
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.events,
.events-type {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.events-type {
  margin-left: 20px;
}
</style>
