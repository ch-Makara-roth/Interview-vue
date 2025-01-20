<template>
  <div class="min-h-screen bg-gray-50 p-6">
    <!-- Add Task Form -->
    <div class="mb-6">
      <form @submit.prevent="addNewTask" class="bg-white rounded-xl p-4 shadow-sm">
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <input
            v-model="newTask.title"
            type="text"
            placeholder="Task title"
            class="px-4 py-2 border border-gray-200 rounded-lg focus:outline-none focus:ring-2 focus:ring-purple-500"
            required
          />
          <select
            v-model="newTask.category"
            class="px-4 py-2 border border-gray-200 rounded-lg focus:outline-none focus:ring-2 focus:ring-purple-500"
            required
          >
            <option value="">Select Category</option>
            <option value="Copywriting">Copywriting</option>
            <option value="UI Design">UI Design</option>
            <option value="Illustration">Illustration</option>
          </select>
          <button
            type="submit"
            class="px-4 py-2 bg-purple-600 text-white rounded-lg hover:bg-purple-700 transition-colors"
          >
            <PlusIcon class="h-5 w-5 inline-block mr-2" />
            Add Task
          </button>
        </div>
      </form>
    </div>

    <!-- Main Board -->
    <div class="grid grid-cols-1 lg:grid-cols-4 gap-4 mb-8">
      <!-- Task Ready Column -->
      <div
        class="bg-white rounded-xl p-4 shadow-sm"
        @dragover.prevent
        @drop.prevent="onDrop('ready')"
      >
        <div class="flex items-center justify-between mb-4">
          <h2 class="text-lg font-semibold text-gray-800">Task Ready</h2>
          <button class="text-gray-400 hover:text-gray-600">
            <MoreVerticalIcon class="h-5 w-5" />
          </button>
        </div>

        <div class="space-y-3">
          <div v-for="task in tasksByStatus('ready')"
               :key="task.id"
               class="bg-white rounded-lg border border-gray-100 p-4 shadow-sm hover:shadow-md transition-shadow cursor-move"
               draggable="true"
               @dragstart="onDragStart(task)"
               @dragend="onDragEnd">
            <div class="flex justify-between items-start mb-3">
              <span :class="[
                'px-3 py-1 rounded-full text-sm',
                task.category === 'Copywriting' ? 'bg-pink-100 text-pink-700' :
                task.category === 'UI Design' ? 'bg-blue-100 text-blue-700' :
                'bg-green-100 text-green-700'
              ]">
                {{ task.category }}
              </span>
              <div class="flex items-center space-x-2">
                <button
                  @click="editTask(task)"
                  class="text-gray-400 hover:text-gray-600">
                  <PencilIcon class="h-4 w-4" />
                </button>
                <button
                  @click="deleteTask(task.id)"
                  class="text-gray-400 hover:text-red-600">
                  <TrashIcon class="h-4 w-4" />
                </button>
              </div>
            </div>

            <h3 class="text-gray-800 font-medium mb-3">{{ task.title }}</h3>

            <div class="flex items-center text-sm text-gray-500 space-x-4">
              <span class="flex items-center">
                <CalendarIcon class="h-4 w-4 mr-1" />
                {{ task.date }}
              </span>
              <span class="flex items-center">
                <MessageSquareIcon class="h-4 w-4 mr-1" />
                {{ task.comments }}
              </span>
              <span class="flex items-center">
                <PaperclipIcon class="h-4 w-4 mr-1" />
                {{ task.attachments }}
              </span>
            </div>
          </div>
        </div>
      </div>

      <!-- In Progress Column -->
      <div
        class="bg-white rounded-xl p-4 shadow-sm"
        @dragover.prevent
        @drop.prevent="onDrop('inProgress')"
      >
        <div class="flex items-center justify-between mb-4">
          <h2 class="text-lg font-semibold text-gray-800">In Progress</h2>
          <button class="text-gray-400 hover:text-gray-600">
            <MoreVerticalIcon class="h-5 w-5" />
          </button>
        </div>

        <div class="space-y-3">
          <div v-for="task in tasksByStatus('inProgress')"
               :key="task.id"
               class="bg-white rounded-lg border border-gray-100 p-4 shadow-sm hover:shadow-md transition-shadow cursor-move"
               draggable="true"
               @dragstart="onDragStart(task)"
                @dragend="onDragEnd">
              <!-- Task Content (Same as Ready Column) -->
            <div class="flex justify-between items-start mb-3">
              <span :class="[
                'px-3 py-1 rounded-full text-sm',
                task.category === 'Copywriting' ? 'bg-pink-100 text-pink-700' :
                task.category === 'UI Design' ? 'bg-blue-100 text-blue-700' :
                'bg-green-100 text-green-700'
              ]">
                {{ task.category }}
              </span>
              <div class="flex items-center space-x-2">
                <button
                  @click="editTask(task)"
                  class="text-gray-400 hover:text-gray-600">
                  <PencilIcon class="h-4 w-4" />
                </button>
                <button
                  @click="deleteTask(task.id)"
                  class="text-gray-400 hover:text-red-600">
                  <TrashIcon class="h-4 w-4" />
                </button>
              </div>
            </div>

            <h3 class="text-gray-800 font-medium mb-3">{{ task.title }}</h3>

            <div class="flex items-center text-sm text-gray-500 space-x-4">
              <span class="flex items-center">
                <CalendarIcon class="h-4 w-4 mr-1" />
                {{ task.date }}
              </span>
              <span class="flex items-center">
                <MessageSquareIcon class="h-4 w-4 mr-1" />
                {{ task.comments }}
              </span>
              <span class="flex items-center">
                <PaperclipIcon class="h-4 w-4 mr-1" />
                {{ task.attachments }}
              </span>
            </div>
          </div>
        </div>
      </div>

      <!-- Progress Stats -->
      <div class="lg:col-span-2">
        <div class="bg-white rounded-xl p-6 shadow-sm mb-4">
          <h2 class="text-xl font-semibold text-gray-800 mb-6">Task Progress</h2>

          <div class="space-y-6">
            <div v-for="progress in progressStats" :key="progress.category">
              <div class="flex justify-between items-center mb-2">
                <span class="text-gray-700">{{ progress.category }}</span>
                <span class="text-gray-500">{{ progress.completed }}/{{ progress.total }}</span>
              </div>
              <div class="h-2 bg-gray-100 rounded-full overflow-hidden">
                <div :class="[
                  'h-full rounded-full transition-all duration-300',
                  progress.category === 'Copywriting' ? 'bg-purple-500' :
                  progress.category === 'Illustration' ? 'bg-green-500' :
                  'bg-blue-500'
                ]" :style="{ width: `${(progress.completed / progress.total) * 100}%` }"></div>
              </div>
            </div>
          </div>
        </div>

        <!-- Recent Activity -->
        <div class="bg-white rounded-xl p-6 shadow-sm">
          <h2 class="text-xl font-semibold text-gray-800 mb-6">Recent Activity</h2>

          <div class="space-y-4">
            <div v-for="activity in recentActivities" :key="activity.id"
                 class="flex items-center space-x-3">
              <div :class="[
                'w-8 h-8 rounded-full flex items-center justify-center',
                activity.type === 'upload' ? 'bg-orange-100' : 'bg-green-100'
              ]">
                <component :is="activity.type === 'upload' ? FileIcon : MessageSquareIcon"
                          class="h-4 w-4"
                          :class="activity.type === 'upload' ? 'text-orange-500' : 'text-green-500'" />
              </div>
              <div class="flex-1">
                <p class="text-gray-800">
                  <span class="font-medium">{{ activity.user }}</span>
                  {{ activity.action }}
                </p>
                <p class="text-sm text-gray-500">{{ activity.date }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import {
  MoreVerticalIcon,
  CalendarIcon,
  MessageSquareIcon,
  PaperclipIcon,
  FileIcon,
  PlusIcon,
  PencilIcon,
  TrashIcon
} from 'lucide-vue-next'

const tasks = ref([
  {
    id: 1,
    category: 'Copywriting',
    title: 'Konsep hero title yang menarik',
    date: 'Nov 24',
    comments: 3,
    attachments: 7,
    status: 'ready'
  },
  {
    id: 2,
    category: 'UI Design',
    title: 'Replace lorem ipsum text in the final designs',
    date: 'Nov 24',
    comments: 5,
    attachments: 5,
    status: 'inProgress'
  }
])

const newTask = ref({
  title: '',
  category: '',
})

const draggedTask = ref(null)

const progressStats = computed(() => {
  const stats = {
    'Copywriting': { completed: 0, total: 0 },
    'Illustration': { completed: 0, total: 0 },
    'UI Design': { completed: 0, total: 0 }
  }

  tasks.value.forEach(task => {
    if (stats[task.category]) {
      stats[task.category].total++
      if (task.status === 'done') {
        stats[task.category].completed++
      }
    }
  })

  return Object.entries(stats).map(([category, data]) => ({
    category,
    completed: data.completed,
    total: data.total
  }))
})

const recentActivities = ref([
  {
    id: 1,
    user: 'Andrea',
    action: 'uploaded 3 documents',
    date: 'Aug 10',
    type: 'upload'
  },
  {
    id: 2,
    user: 'Karen',
    action: 'left a comment',
    date: 'Aug 10',
    type: 'comment'
  }
])

const tasksByStatus = (status) => {
  return tasks.value.filter(task => task.status === status)
}

const addNewTask = () => {
  if (newTask.value.title && newTask.value.category) {
    tasks.value.push({
      id: Date.now(),
      ...newTask.value,
      date: new Date().toLocaleDateString('en-US', { month: 'short', day: 'numeric' }),
      comments: 0,
      attachments: 0,
      status: 'ready'
    })

    // Add to recent activities
    recentActivities.value.unshift({
      id: Date.now(),
      user: 'You',
      action: 'created a new task',
      date: new Date().toLocaleDateString('en-US', { month: 'short', day: 'numeric' }),
      type: 'upload'
    })

    // Reset form
    newTask.value = {
      title: '',
      category: ''
    }
  }
}

const deleteTask = (taskId) => {
  const taskIndex = tasks.value.findIndex(t => t.id === taskId)
  if (taskIndex !== -1) {
    const deletedTask = tasks.value[taskIndex]
    tasks.value.splice(taskIndex, 1)

    // Add to recent activities
    recentActivities.value.unshift({
      id: Date.now(),
      user: 'You',
      action: 'deleted a task',
      date: new Date().toLocaleDateString('en-US', { month: 'short', day: 'numeric' }),
      type: 'upload'
    })
  }
}

const editTask = (task) => {
  // Implementation for editing task (could show a modal or inline form)
  console.log('Edit task:', task)
}

const onDragStart = (task) => {
  draggedTask.value = task
}

const onDragEnd = () => {
  draggedTask.value = null
}

const onDrop = (status) => {
  if (draggedTask.value) {
    const taskIndex = tasks.value.findIndex(t => t.id === draggedTask.value.id)
    if (taskIndex !== -1) {
      tasks.value[taskIndex] = {
        ...tasks.value[taskIndex],
        status
      }

      // Add to recent activities
      recentActivities.value.unshift({
        id: Date.now(),
        user: 'You',
        action: `moved task to ${status}`,
        date: new Date().toLocaleDateString('en-US', { month: 'short', day: 'numeric' }),
        type: 'upload'
      })
    }
  }
}
</script>
