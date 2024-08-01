<script setup lang="ts">
import EventCard from '@/components/EventCard.vue'
import EventType from '@/components/EventType.vue'
import EventService from '@/services/EventService'
import type { Event } from '@/types'
import { ref, onMounted, watchEffect, computed } from 'vue'
import { useRouter } from 'vue-router'
const totalEvent = ref<number>(0)
const props = defineProps({
  page: {
    type: Number,
    required: true
  },
  size: {
    type: Number,
    required: true
  }
})

const events = ref<Event[]>(null)
const router = useRouter()
onMounted(() => {
  watchEffect(() => {
    EventService.getEvents(props.size, props.page)
      .then((response) => {
        events.value = response.data
        totalEvent.value = response.headers['x-total-count']
      })
      .catch((error) => {
        if (error.response && error.response.status === 404) {
          router.push({ name: '404-resource', params: { resource: 'event' } })
        } else {
          router.push({ name: 'network-error-view' })
        }
      })
  })
})
const hasNextPage = computed(() => {
  // first calculate the the total page
  const totalPages = Math.ceil(totalEvent.value / props.size)
  return props.page < totalPages
})
</script>

<template>
  <h1>Events For Good</h1>
  <!-- new element -->
  <div class="events-container">
    <div class="events">
      <EventCard v-for="event in events" :key="event.id" :event="event" />
      <div class="pagination">
        <RouterLink
          :to="{ name: 'event-list', query: { page: page - 1, size: props.size } }"
          rel="prev"
          v-if="props.page != 1"
          id="page-prev"
        >
          Prev Page
        </RouterLink>
        <RouterLink
          :to="{ name: 'event-list', query: { page: props.page + 1, size: props.size } }"
          rel="next"
          v-if="hasNextPage"
          id="page-next"
        >
          Next Page
        </RouterLink>
      </div>
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

.pagination {
  display: flex;
  width: 290px;
}

.paginatio a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}

#page-prev {
  text-align: left;
}

#page-next {
  text-align: right;
}
</style>
