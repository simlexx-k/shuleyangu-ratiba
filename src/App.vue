<script setup>
import { computed, reactive, ref } from 'vue'
import { useStorage } from '@vueuse/core'
import { nanoid } from 'nanoid'

const defaultTemplate = {
  meta: {
    schoolName: 'ShuleYangu School',
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

const standardTemplates = [
  {
    id: 'ecde',
    title: 'ECDE Class Timetable',
    subtitle: 'Daily schedule (Monday to Friday)',
    days: ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday'],
    rows: [
      {
        time: 'Before 8:30',
        cells: [
          'Free Choice Activities',
          'Free Choice Activities',
          'Free Choice Activities',
          'Free Choice Activities',
          'Free Choice Activities',
        ],
      },
      {
        time: '8:30 - 9:00',
        cells: [
          'Health Check & Roll Call',
          'Health Check & Roll Call',
          'Health Check & Roll Call',
          'Health Check & Roll Call',
          'Health Check & Roll Call',
        ],
      },
      {
        time: '9:00 - 9:30',
        cells: [
          'Language Activities',
          'Mathematics Activities',
          'Environmental Activities',
          'Mathematics Activities',
          'PPI',
        ],
      },
      {
        time: '9:30 - 10:00',
        cells: [
          'Mathematics Activities',
          'Creative Activities',
          'Religious Activities',
          'Language Activities',
          'Creative Activities',
        ],
      },
      {
        time: '10:00 - 10:10',
        cells: ['BREAK', 'BREAK', 'BREAK', 'BREAK', 'BREAK'],
      },
      {
        time: '10:10 - 10:40',
        cells: [
          'Creative Activities',
          'Language Activities',
          'Creative Activities',
          'Creative Activities',
          'Environmental Activities',
        ],
      },
      {
        time: '10:40 - 11:00',
        cells: [
          'Religious Activities',
          'Environmental Activities',
          'Language Activities',
          'Environmental Activities',
          'Language Activities',
        ],
      },
      {
        time: '11:00 - 11:30',
        cells: [
          'Environmental Activities',
          'Creative Activities',
          'Mathematical Activities',
          'Religious Activities',
          'Mathematical Activities',
        ],
      },
      {
        time: '11:30 - 12:00',
        cells: ['LUNCH', 'LUNCH', 'LUNCH', 'LUNCH', 'LUNCH'],
      },
    ],
  },
  {
    id: 'lower',
    title: 'Lower Class Timetable',
    subtitle: 'Daily schedule (Monday to Friday)',
    days: ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday'],
    rows: [
      {
        time: '8:00 - 8:20',
        cells: [
          'Health Check & Roll Call',
          'Health Check & Roll Call',
          'Health Check & Roll Call',
          'Health Check & Roll Call',
          'PPI',
        ],
      },
      {
        time: '8:20 - 8:50',
        cells: [
          'Indigenous Language',
          'Kiswahili Language',
          'English Language',
          'Mathematical Activities',
          'English Language',
        ],
      },
      {
        time: '8:50 - 9:20',
        cells: [
          'Creative Activities',
          'Mathematical Activities',
          'Religious Activities',
          'Creative Activities',
          'Environmental Activities',
        ],
      },
      {
        time: '9:20 - 9:30',
        cells: ['BREAK', 'BREAK', 'BREAK', 'BREAK', 'BREAK'],
      },
      {
        time: '9:30 - 10:00',
        cells: [
          'English Language',
          'English Language',
          'Mathematical Activities',
          'Environmental Activities',
          'Kiswahili Language',
        ],
      },
      {
        time: '10:00 - 10:30',
        cells: [
          'Mathematical Activities',
          'Creative Activities',
          'Creative Activities',
          'Kiswahili Language',
          'Creative Activities',
        ],
      },
      {
        time: '10:30 - 11:00',
        cells: ['BREAK', 'BREAK', 'BREAK', 'BREAK', 'BREAK'],
      },
      {
        time: '11:00 - 11:30',
        cells: [
          'Religious Activities',
          'Indigenous Language',
          'Kiswahili Language',
          'English Language',
          'Mathematical Activities',
        ],
      },
      {
        time: '11:30 - 12:00',
        cells: [
          'Environmental Activities',
          'Creative Activities',
          'Environmental Activities',
          'Creative Activities',
          'Religious Activities',
        ],
      },
      {
        time: '12:00 - 12:30',
        cells: ['LUNCH', 'LUNCH', 'LUNCH', 'LUNCH', 'LUNCH'],
      },
    ],
  },
  {
    id: 'upper-primary',
    title: 'Upper Primary Timetable',
    subtitle: 'Blank template (manual filling)',
    days: ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday'],
    rows: [
      { time: '8:00 - 8:20', cells: ['', '', '', '', ''] },
      { time: '8:20 - 8:55', cells: ['', '', '', '', ''] },
      { time: '8:55 - 9:30', cells: ['', '', '', '', ''] },
      { time: '9:30 - 9:50', cells: ['BREAK', 'BREAK', 'BREAK', 'BREAK', 'BREAK'] },
      { time: '9:50 - 10:25', cells: ['', '', '', '', ''] },
      { time: '10:25 - 11:00', cells: ['', '', '', '', ''] },
      { time: '11:00 - 11:30', cells: ['BREAK', 'BREAK', 'BREAK', 'BREAK', 'BREAK'] },
      { time: '11:30 - 12:05', cells: ['', '', '', '', ''] },
      { time: '12:05 - 12:40', cells: ['', '', '', '', ''] },
      { time: '12:40 - 2:00', cells: ['LUNCH', 'LUNCH', 'LUNCH', 'LUNCH', 'LUNCH'] },
      { time: '2:00 - 2:35', cells: ['', '', '', '', ''] },
      { time: '2:35 - 4:00', cells: ['', '', '', '', ''] },
    ],
  },
  {
    id: 'junior-school',
    title: 'Junior School Timetable',
    subtitle: 'Blank template (manual filling)',
    days: ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday'],
    rows: [
      { time: '8:20 - 9:00', cells: ['', '', '', '', ''] },
      { time: '9:00 - 9:40', cells: ['', '', '', '', ''] },
      { time: '9:40 - 9:50', cells: ['BREAK', 'BREAK', 'BREAK', 'BREAK', 'BREAK'] },
      { time: '9:50 - 10:30', cells: ['', '', '', '', ''] },
      { time: '10:30 - 11:10', cells: ['', '', '', '', ''] },
      { time: '11:10 - 11:40', cells: ['BREAK', 'BREAK', 'BREAK', 'BREAK', 'BREAK'] },
      { time: '11:40 - 12:20', cells: ['', '', '', '', ''] },
      { time: '12:20 - 1:00', cells: ['', '', '', '', ''] },
      { time: '1:00 - 2:00', cells: ['LUNCH', 'LUNCH', 'LUNCH', 'LUNCH', 'LUNCH'] },
      { time: '2:00 - 2:40', cells: ['', '', '', '', ''] },
      { time: '2:40 - 3:20', cells: ['', '', '', '', ''] },
      { time: '3:20 - 4:45', cells: ['', '', '', '', ''] },
    ],
  },
]

const template = useStorage('ratiba_template_v1', defaultTemplate, undefined, {
  mergeDefaults: true,
})

const activeView = ref('builder')

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

const escapeHtml = (value) => {
  return String(value)
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#39;')
}

const cellClass = (value) => {
  const normalized = String(value || '').trim().toLowerCase()
  if (!normalized) return 'is-empty'
  if (normalized === 'break') return 'is-break'
  if (normalized === 'lunch') return 'is-lunch'
  return ''
}

const parseTimeRange = (label) => {
  if (!label.includes('-')) return { start: '', end: '' }
  const parts = label.split('-').map(part => part.trim())
  if (parts.length < 2) return { start: '', end: '' }
  return { start: parts[0], end: parts.slice(1).join('-').trim() }
}

const buildTemplateFromStandard = (standard) => {
  const baseDays = [
    { id: 'mon', label: 'Monday', short: 'Mon' },
    { id: 'tue', label: 'Tuesday', short: 'Tue' },
    { id: 'wed', label: 'Wednesday', short: 'Wed' },
    { id: 'thu', label: 'Thursday', short: 'Thu' },
    { id: 'fri', label: 'Friday', short: 'Fri' },
    { id: 'sat', label: 'Saturday', short: 'Sat' },
  ]
  const daySet = new Set(standard.days)
  const days = baseDays.map(day => ({
    ...day,
    active: daySet.has(day.label),
  }))

  const timeSlots = standard.rows.map((row, index) => {
    const parsed = parseTimeRange(row.time)
    return {
      id: `slot-${index + 1}`,
      label: row.time,
      start: parsed.start,
      end: parsed.end,
    }
  })

  const uniqueSubjects = []
  standard.rows.forEach(row => {
    row.cells.forEach(cell => {
      const value = String(cell || '').trim()
      if (!value) return
      if (!uniqueSubjects.includes(value)) uniqueSubjects.push(value)
    })
  })

  const fixedColors = {
    BREAK: '#0f172a',
    LUNCH: '#334155',
    PPI: '#f97316',
    'Health Check & Roll Call': '#2563eb',
    'Free Choice Activities': '#0ea5a4',
  }
  const palette = ['#8b5cf6', '#16a34a', '#0ea5a4', '#f97316', '#2563eb', '#d97706']

  const subjects = uniqueSubjects.map((name, index) => ({
    id: nanoid(),
    name,
    color: fixedColors[name] || palette[index % palette.length],
  }))
  const subjectIdByName = new Map(subjects.map(subject => [subject.name, subject.id]))

  const activeDayList = days.filter(day => day.active)
  const sessions = []
  standard.rows.forEach((row, rowIndex) => {
    const slotId = timeSlots[rowIndex]?.id
    if (!slotId) return
    row.cells.forEach((cell, dayIndex) => {
      const value = String(cell || '').trim()
      if (!value) return
      const day = activeDayList[dayIndex]
      if (!day) return
      sessions.push({
        id: nanoid(),
        dayId: day.id,
        slotId,
        subjectId: subjectIdByName.get(value),
        teacher: '',
        room: '',
      })
    })
  })

  return {
    meta: {
      schoolName: template.value.meta.schoolName,
      className: standard.title,
      term: template.value.meta.term,
      academicYear: template.value.meta.academicYear,
      coordinator: template.value.meta.coordinator,
    },
    days,
    timeSlots,
    subjects,
    sessions,
  }
}

const applyStandardTemplate = (standard) => {
  template.value = buildTemplateFromStandard(standard)
  activeView.value = 'builder'
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

const buildStandardTemplateHtml = (standard) => {
  const timestamp = new Date().toLocaleString()
  const headerCells = standard.days.map(day => `<th>${escapeHtml(day)}</th>`).join('')
  const rows = standard.rows
    .map(row => {
      const cells = row.cells
        .map(cell => {
          const value = String(cell || '')
          const blank = !value.trim()
          const className = cellClass(value)
          return `
            <td class="${className}">
              ${blank ? '<span class="blank-line"></span>' : escapeHtml(value)}
            </td>
          `
        })
        .join('')
      return `
        <tr>
          <td class="time">${escapeHtml(row.time)}</td>
          ${cells}
        </tr>
      `
    })
    .join('')

  return `
    <!doctype html>
    <html>
      <head>
        <meta charset="utf-8" />
    <title>${escapeHtml(standard.title)} | ShuleYangu Ratiba</title>
        <style>
          * { box-sizing: border-box; }
          body { margin: 0; font-family: 'Segoe UI', Arial, sans-serif; color: #0f172a; background: #f8fafc; }
          .sheet { max-width: 980px; margin: 24px auto; background: #fff; border: 1px solid #e2e8f0; border-radius: 16px; padding: 24px; }
          .print-header { display: flex; align-items: center; justify-content: space-between; gap: 16px; border-bottom: 1px solid #e2e8f0; padding-bottom: 12px; }
          .print-brand { display: flex; align-items: center; gap: 10px; }
          .print-logo { width: 36px; height: 36px; border-radius: 10px; border: 1px solid #e2e8f0; object-fit: cover; }
          .brand-text { font-size: 11px; text-transform: uppercase; letter-spacing: 0.12em; color: #64748b; }
          h1 { margin: 0; font-size: 22px; }
          .subtitle { margin-top: 6px; font-size: 12px; color: #64748b; }
          .meta { display: flex; gap: 12px; font-size: 11px; color: #64748b; }
          table { width: 100%; border-collapse: collapse; margin-top: 16px; font-size: 12px; }
          th, td { border: 1px solid #e2e8f0; padding: 8px; text-align: left; vertical-align: top; }
          th { background: #f1f5f9; text-transform: uppercase; letter-spacing: 0.08em; font-size: 10px; }
          td.time { font-weight: 600; background: #f8fafc; min-width: 120px; }
          td.is-break { background: #fef3c7; font-weight: 600; }
          td.is-lunch { background: #dbeafe; font-weight: 600; }
          td.is-empty { background: #fff; }
          .blank-line { display: block; border-bottom: 1px dashed #cbd5f5; height: 16px; }
          .footer { margin-top: 16px; font-size: 11px; color: #94a3b8; }
          @media print { body { background: #fff; } .sheet { border: none; box-shadow: none; } }
        </style>
      </head>
      <body>
        <div class="sheet">
          <div class="print-header">
            <div class="print-brand">
              <img class="print-logo" src="/icons/icon-192.png" alt="ShuleYangu logo" />
              <div>
                <div class="brand-text">ShuleYangu School Systems</div>
                <h1>${escapeHtml(standard.title)}</h1>
              </div>
            </div>
            <div class="meta">
              <div>Generated: ${escapeHtml(timestamp)}</div>
              <div>Ratiba Standard Template</div>
            </div>
          </div>
          <div class="subtitle">${escapeHtml(standard.subtitle)}</div>
          <table>
            <thead>
              <tr>
                <th>Time</th>
                ${headerCells}
              </tr>
            </thead>
            <tbody>
              ${rows}
            </tbody>
          </table>
          <div class="footer">Generated by ShuleYangu School Systems • Ratiba.</div>
        </div>
      </body>
    </html>
  `
}

const printStandardTemplate = (standard) => {
  const win = window.open('', '_blank', 'width=1100,height=900')
  if (!win) return
  win.document.open()
  win.document.write(buildStandardTemplateHtml(standard))
  win.document.close()
  win.focus()
  setTimeout(() => {
    win.print()
  }, 300)
}

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
        <img class="brand-logo" src="/icons/icon-192.png" alt="ShuleYangu logo" />
        <div>
          <div class="eyebrow">ShuleYangu School Systems</div>
          <h1>Ratiba Timetable Studio</h1>
          <p>Design printable timetables for every class in minutes.</p>
        </div>
      </div>
      <div class="hero-actions no-print">
        <template v-if="activeView === 'builder'">
          <button class="btn primary" type="button" @click="handlePrint">Print to PDF</button>
          <button class="btn ghost" type="button" @click="resetTemplate">Reset</button>
        </template>
        <template v-else>
          <button class="btn primary" type="button" @click="activeView = 'builder'">Back to builder</button>
        </template>
      </div>
    </header>

    <nav class="view-tabs no-print">
      <button
        class="view-tab"
        :class="{ active: activeView === 'builder' }"
        type="button"
        @click="activeView = 'builder'"
      >
        Builder
      </button>
      <button
        class="view-tab"
        :class="{ active: activeView === 'templates' }"
        type="button"
        @click="activeView = 'templates'"
      >
        Standard templates
      </button>
    </nav>

    <section v-if="activeView === 'templates'" class="templates">
      <div class="templates-header">
        <div>
          <div class="eyebrow">ShuleYangu starter packs</div>
          <h2>Standard timetable templates</h2>
          <p>Pick a template, print as-is, or load it into the builder to customize.</p>
        </div>
      </div>
      <div class="templates-grid">
        <article v-for="standard in standardTemplates" :key="standard.id" class="template-card">
          <div class="template-head">
            <div>
              <h3>{{ standard.title }}</h3>
              <p>{{ standard.subtitle }}</p>
            </div>
            <div class="template-actions">
              <button class="btn primary" type="button" @click="applyStandardTemplate(standard)">
                Use template
              </button>
              <button class="btn" type="button" @click="printStandardTemplate(standard)">
                Print
              </button>
            </div>
          </div>
          <div class="template-table-wrapper">
            <table class="template-table">
              <thead>
                <tr>
                  <th>Time</th>
                  <th v-for="day in standard.days" :key="day">{{ day }}</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="row in standard.rows" :key="row.time">
                  <td class="time">{{ row.time }}</td>
                  <td
                    v-for="(cell, index) in row.cells"
                    :key="index"
                    :class="cellClass(cell)"
                  >
                    <span v-if="cell">{{ cell }}</span>
                    <span v-else class="blank-line"></span>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </article>
      </div>
    </section>

    <div v-else class="layout">
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
              <div class="sheet-brand">
                <img class="sheet-logo" src="/icons/icon-192.png" alt="ShuleYangu logo" />
                <span class="sheet-eyebrow">ShuleYangu School Systems • Ratiba</span>
              </div>
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
                    <span v-if="getSession(day.id, slot.id)?.teacher">{{ getSession(day.id, slot.id)?.teacher }}</span>
                    <span v-if="getSession(day.id, slot.id)?.room">{{ getSession(day.id, slot.id)?.room }}</span>
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
            <div>ShuleYangu School Systems • Ratiba Timetable Studio.</div>
            <div class="no-print">Tip: click any cell to load the editor on the left.</div>
          </div>
        </section>
      </main>
    </div>
  </div>
</template>
