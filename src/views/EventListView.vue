<script setup lang="ts">
import EventCard from '@/components/EventCard.vue'
import EventType from '@/components/EventType.vue'
import EventService from '@/services/EventService'
import type { Event } from '@/types'
import { ref, onMounted } from 'vue'

const events = ref<Event[]>(null)

onMounted(() => {
  EventService.getEvents()
    .then((response) => {
      events.value = response.data
    })
    .catch((error) => {
      console.error('There was an error!', error)
    })
})
</script>

<template>
  <h1>Events For Good</h1>
  <!-- new element -->
  <div class="events-container">
    <div class="events">
      <EventCard v-for="event in events" :key="event.id" :event="event" />
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
