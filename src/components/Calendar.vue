<!-- eslint-disable no-console -->
<script setup lang="ts">
import type { Ref } from 'vue'
import { onMounted, ref } from 'vue'

interface Day {
  day: string
  date: string
}

const props = defineProps({
  date: {
    type: String,
    default: '',
  },
})

const currentDate = ref(props.date ? new Date(props.date) : new Date())
const selectedDate = ref('')
const isRussian = ref(false)
const daysOfWeek = ref(isRussian.value ? ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'] : ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'])
const calendar: Ref<Day[][]> = ref([])
const currentMonthYear = ref('')

function initCalendar() {
  const currentYear = currentDate.value.getFullYear()
  const currentMonth = currentDate.value.getMonth()

  currentMonthYear.value = `${getMonthName(currentMonth)} ${currentYear}`

  const firstDayOfMonth = new Date(currentYear, currentMonth, 1)
  const lastDayOfMonth = new Date(currentYear, currentMonth + 1, 0)
  const firstDayOfWeek = firstDayOfMonth.getDay()
  const lastDay = lastDayOfMonth.getDate()

  let date = 1
  const calendarData = []

  for (let i = 0; i < 6; i++) {
    const week: Day[] = []

    for (let j = 0; j < 7; j++) {
      if (i === 0 && j < firstDayOfWeek) { week.push({ day: '', date: '' }) }
      else if (date > lastDay) { break }
      else {
        week.push({ day: date.toString(), date: `${currentYear}-${currentMonth}-${date}` })
        date++
      }
    }
    calendarData.push(week)
  }

  calendar.value = calendarData
}
function getMonthName(month: number) {
  const months = isRussian.value ? ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октбярь', 'Ноябрь', 'Декабрь'] : ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']

  return (months[month])
}

function toggleLanguage() {
  isRussian.value = !isRussian.value
  daysOfWeek.value = isRussian.value ? ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'] : ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
  initCalendar()
}

function selectDate(date: string) {
  selectedDate.value = date
}

function prevMonth() {
  currentDate.value.setMonth(currentDate.value.getMonth() - 1)
  initCalendar()
}

function nextMonth() {
  currentDate.value.setMonth(currentDate.value.getMonth() + 1)
  initCalendar()
}

onMounted(() => {
  initCalendar()
})
</script>

<template>
  <div>
    <h1>{{ currentMonthYear }}</h1>
    <button @click="prevMonth">
      prev
    </button>
    <button @click="nextMonth">
      next
    </button>
    <button @click="toggleLanguage">
      Lang
    </button>
    <table>
      <thead>
        <tr>
          <th v-for="day in daysOfWeek" :key="day">
            {{ day }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(week, index) in calendar" :key="index">
          <td v-for="day in week" :key="day.date" :class="{ 'selected-date': day.date && day.date === selectedDate }" @click="selectDate(day.date)">
            {{ day.day }}
          </td>
        </tr>
      </tbody>
    </table>
    <input v-model="selectedDate" type="text" placeholder="selected date">
  </div>
</template>

<style scoped>
    .selected-date {
        border: 0.5px solid lightblue;
    }
</style>
