<script setup>
import { computed, reactive } from 'vue'
import { useStorage } from '@vueuse/core'
import { nanoid } from 'nanoid'

const defaultTemplate = {
  meta: {
    schoolName: 'Ratiba Academy',
    className: 'Form 2 West',
    term: 'Term 1',
    academicYear: '2025',
    coordinator: 'Academic Office',
  },
  days: [
    { id: 'mon', label: 'Monday', short: 'Mon', active: true },
    { id: 'tue', label: 'Tuesday', short: 'Tue', active: true },
    { id: 'wed', label: 'Wednesday', short: 'Wed', active: true },
    { id: 'thu', label: 'Thursday', short: 'Thu', active: true },
    { id: 'fri', label: 'Friday', short: 'Fri', active: true },
    { id: 'sat', label: 'Saturday', short: 'Sat', active: false },
  ],
  timeSlots: [
    { id: 'slot-1', label: 'Period 1', start: '08:00', end: '08:50' },
    { id: 'slot-2', label: 'Period 2', start: '09:00', end: '09:50' },
    { id: 'slot-3', label: 'Period 3', start: '10:10', end: '11:00' },
    { id: 'slot-4', label: 'Period 4', start: '11:10', end: '12:00' },
    { id: 'slot-5', label: 'Period 5', start: '13:00', end: '13:50' },
  ],
  subjects: [
    { id: 'sub-math', name: 'Mathematics', color: '#f97316' },
    { id: 'sub-eng', name: 'English', color: '#2563eb' },
    { id: 'sub-bio', name: 'Biology', color: '#16a34a' },
    { id: 'sub-hist', name: 'History', color: '#8b5cf6' },
  ],
  sessions: [
    {
      id: 'sess-1',
      dayId: 'mon',
      slotId: 'slot-1',
      subjectId: 'sub-math',
      teacher: 'Ms. Wanjiru',
      room: 'Lab 2',
    },
    {
      id: 'sess-2',
      dayId: 'mon',
      slotId: 'slot-2',
      subjectId: 'sub-eng',
      teacher: 'Mr. Odhiambo',
      room: 'Room 4',
    },
    {
      id: 'sess-3',
      dayId: 'tue',
      slotId: 'slot-3',
      subjectId: 'sub-bio',
      teacher: 'Ms. Wachira',
      room: 'Lab 1',
    },
  ],
}

const template = useStorage('ratiba_template_v1', defaultTemplate, undefined, {
  mergeDefaults: true,
})

const sessionDraft = reactive({
  dayId: '',
  slotId: '',
  subjectId: '',
  teacher: '',
  room: '',
})

const newSubject = reactive({
  name: '',
  color: '#0ea5a4',
})

const newSlot = reactive({
  label: '',
  start: '08:00',
  end: '08:50',
})

const activeDays = computed(() => template.value.days.filter(day => day.active))

const subjectMap = computed(() => {
  return new Map(template.value.subjects.map(subject => [subject.id, subject]))
})

const sessionMap = computed(() => {
  const map = new Map()
  template.value.sessions.forEach(session => {
    map.set(`${session.dayId}:${session.slotId}`, session)
  })
  return map
})

const handlePrint = () => {
  window.print()
}

const resetTemplate = () => {
  if (!window.confirm('Reset this timetable to the default template?')) return
  template.value = JSON.parse(JSON.stringify(defaultTemplate))
}

const toggleDay = (id) => {
  const day = template.value.days.find(item => item.id === id)
  if (day) {
    day.active = !day.active
  }
}

const addSubject = () => {
  if (!newSubject.name.trim()) return
  template.value.subjects.push({
    id: nanoid(),
    name: newSubject.name.trim(),
    color: newSubject.color,
  })
  newSubject.name = ''
}

const removeSubject = (id) => {
  template.value.subjects = template.value.subjects.filter(subject => subject.id !== id)
  template.value.sessions = template.value.sessions.filter(session => session.subjectId !== id)
}

const addSlot = () => {
  if (!newSlot.label.trim()) return
  template.value.timeSlots.push({
    id: nanoid(),
    label: newSlot.label.trim(),
    start: newSlot.start,
    end: newSlot.end,
  })
  newSlot.label = ''
}

const removeSlot = (id) => {
  template.value.timeSlots = template.value.timeSlots.filter(slot => slot.id !== id)
  template.value.sessions = template.value.sessions.filter(session => session.slotId !== id)
}

const addSession = () => {
  if (!sessionDraft.dayId || !sessionDraft.slotId || !sessionDraft.subjectId) return
  const key = `${sessionDraft.dayId}:${sessionDraft.slotId}`
  const existing = sessionMap.value.get(key)
  if (existing) {
    existing.subjectId = sessionDraft.subjectId
    existing.teacher = sessionDraft.teacher.trim()
    existing.room = sessionDraft.room.trim()
  } else {
    template.value.sessions.push({
      id: nanoid(),
      dayId: sessionDraft.dayId,
      slotId: sessionDraft.slotId,
      subjectId: sessionDraft.subjectId,
      teacher: sessionDraft.teacher.trim(),
      room: sessionDraft.room.trim(),
    })
  }
  sessionDraft.teacher = ''
  sessionDraft.room = ''
}

const selectCell = (dayId, slotId) => {
  sessionDraft.dayId = dayId
  sessionDraft.slotId = slotId
}

const clearSession = (dayId, slotId) => {
  template.value.sessions = template.value.sessions.filter(
    session => !(session.dayId === dayId && session.slotId === slotId)
  )
}

const getSession = (dayId, slotId) => {
  return sessionMap.value.get(`${dayId}:${slotId}`)
}

const getSubject = (id) => {
  return subjectMap.value.get(id)
}
</script>

<template>
  <div class="app">
    <header class="hero">
      <div class="brand">
        <div class="logo">R</div>
        <div>
          <div class="eyebrow">Open timetable studio</div>
          <h1>Ratiba</h1>
          <p>Create printable, school-ready timetables in minutes.</p>
        </div>
      </div>
      <div class="hero-actions no-print">
        <button class="btn primary" type="button" @click="handlePrint">Print to PDF</button>
        <button class="btn ghost" type="button" @click="resetTemplate">Reset</button>
      </div>
    </header>

    <div class="layout">
      <aside class="editor no-print">
        <section class="panel">
          <h2>Template details</h2>
          <div class="field-grid">
            <label class="field">
              <span>School name</span>
              <input v-model="template.meta.schoolName" type="text" />
            </label>
            <label class="field">
              <span>Class name</span>
              <input v-model="template.meta.className" type="text" />
            </label>
            <label class="field">
              <span>Term</span>
              <input v-model="template.meta.term" type="text" />
            </label>
            <label class="field">
              <span>Academic year</span>
              <input v-model="template.meta.academicYear" type="text" />
            </label>
            <label class="field">
              <span>Coordinator</span>
              <input v-model="template.meta.coordinator" type="text" />
            </label>
          </div>
        </section>

        <section class="panel">
          <h2>Days of the week</h2>
          <div class="chip-row">
            <button
              v-for="day in template.days"
              :key="day.id"
              type="button"
              class="chip"
              :class="{ active: day.active }"
              @click="toggleDay(day.id)"
            >
              {{ day.short }}
            </button>
          </div>
        </section>

        <section class="panel">
          <h2>Time slots</h2>
          <div class="list">
            <div v-for="slot in template.timeSlots" :key="slot.id" class="list-item">
              <div class="list-main">
                <input v-model="slot.label" type="text" />
                <div class="time-row">
                  <input v-model="slot.start" type="time" />
                  <span>-</span>
                  <input v-model="slot.end" type="time" />
                </div>
              </div>
              <button class="icon-btn" type="button" @click="removeSlot(slot.id)">Remove</button>
            </div>
          </div>
          <div class="list-add">
            <input v-model="newSlot.label" type="text" placeholder="New slot label" />
            <div class="time-row">
              <input v-model="newSlot.start" type="time" />
              <span>-</span>
              <input v-model="newSlot.end" type="time" />
            </div>
            <button class="btn" type="button" @click="addSlot">Add slot</button>
          </div>
        </section>

        <section class="panel">
          <h2>Subjects</h2>
          <div class="list">
            <div v-for="subject in template.subjects" :key="subject.id" class="list-item">
              <div class="list-main">
                <div class="color" :style="{ background: subject.color }"></div>
                <input v-model="subject.name" type="text" />
              </div>
              <button class="icon-btn" type="button" @click="removeSubject(subject.id)">Remove</button>
            </div>
          </div>
          <div class="list-add">
            <input v-model="newSubject.name" type="text" placeholder="New subject" />
            <input v-model="newSubject.color" type="color" />
            <button class="btn" type="button" @click="addSubject">Add subject</button>
          </div>
        </section>

        <section class="panel">
          <h2>Schedule a session</h2>
          <div class="field-grid">
            <label class="field">
              <span>Day</span>
              <select v-model="sessionDraft.dayId">
                <option value="" disabled>Select day</option>
                <option v-for="day in activeDays" :key="day.id" :value="day.id">
                  {{ day.label }}
                </option>
              </select>
            </label>
            <label class="field">
              <span>Time slot</span>
              <select v-model="sessionDraft.slotId">
                <option value="" disabled>Select slot</option>
                <option v-for="slot in template.timeSlots" :key="slot.id" :value="slot.id">
                  {{ slot.label }} ({{ slot.start }}-{{ slot.end }})
                </option>
              </select>
            </label>
            <label class="field">
              <span>Subject</span>
              <select v-model="sessionDraft.subjectId">
                <option value="" disabled>Select subject</option>
                <option v-for="subject in template.subjects" :key="subject.id" :value="subject.id">
                  {{ subject.name }}
                </option>
              </select>
            </label>
            <label class="field">
              <span>Teacher</span>
              <input v-model="sessionDraft.teacher" type="text" placeholder="e.g. Ms. Amina" />
            </label>
            <label class="field">
              <span>Room</span>
              <input v-model="sessionDraft.room" type="text" placeholder="e.g. Room 10" />
            </label>
          </div>
          <button class="btn primary" type="button" @click="addSession">Save session</button>
        </section>
      </aside>

      <main class="preview">
        <section class="sheet">
          <div class="sheet-header">
            <div>
              <div class="sheet-eyebrow">Timetable template</div>
              <h2>{{ template.meta.schoolName }}</h2>
              <p>{{ template.meta.className }} | {{ template.meta.term }} {{ template.meta.academicYear }}</p>
            </div>
            <div class="sheet-meta">
              <div>
                <span class="meta-label">Coordinator</span>
                <span class="meta-value">{{ template.meta.coordinator }}</span>
              </div>
              <div>
                <span class="meta-label">Generated</span>
                <span class="meta-value">{{ new Date().toLocaleDateString() }}</span>
              </div>
            </div>
          </div>

          <div class="grid">
            <div class="grid-row header">
              <div class="grid-cell time">Time</div>
              <div v-for="day in activeDays" :key="day.id" class="grid-cell day">
                {{ day.label }}
              </div>
            </div>

            <div v-for="slot in template.timeSlots" :key="slot.id" class="grid-row">
              <div class="grid-cell time">
                <div class="slot-label">{{ slot.label }}</div>
                <div class="slot-time">{{ slot.start }} - {{ slot.end }}</div>
              </div>
              <div
                v-for="day in activeDays"
                :key="day.id"
                class="grid-cell slot"
              >
                <button
                  v-if="getSession(day.id, slot.id)"
                  class="session-card"
                  type="button"
                  @click="selectCell(day.id, slot.id)"
                  :style="{ borderColor: getSubject(getSession(day.id, slot.id)?.subjectId)?.color || '#94a3b8' }"
                >
                  <div
                    class="session-pill"
                    :style="{ background: getSubject(getSession(day.id, slot.id)?.subjectId)?.color || '#94a3b8' }"
                  ></div>
                  <div class="session-info">
                    <strong>{{ getSubject(getSession(day.id, slot.id)?.subjectId)?.name || 'Session' }}</strong>
                    <span>{{ getSession(day.id, slot.id)?.teacher || 'Teacher' }}</span>
                    <span>{{ getSession(day.id, slot.id)?.room || 'Room' }}</span>
                  </div>
                  <span class="session-action no-print">Edit</span>
                </button>
                <button
                  v-else
                  class="session-empty"
                  type="button"
                  @click="selectCell(day.id, slot.id)"
                >
                  Add session
                </button>
                <button
                  v-if="getSession(day.id, slot.id)"
                  class="cell-clear no-print"
                  type="button"
                  @click="clearSession(day.id, slot.id)"
                >
                  Clear
                </button>
              </div>
            </div>
          </div>

          <div class="sheet-footer">
            <div>Ratiba is an open timetable studio for any school.</div>
            <div class="no-print">Tip: click any cell to load the editor on the left.</div>
          </div>
        </section>
      </main>
    </div>
  </div>
</template>
