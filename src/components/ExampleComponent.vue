<template>
  <div class="q-pa-md">
    <div class="text-h5 q-mb-md">{{ title }}</div>

    <!-- KPI Cards -->
    <div class="row q-col-gutter-md q-mb-md">
      <div class="col-12 col-sm-6 col-md-3">
        <q-card flat bordered class="bg-primary text-white">
          <q-card-section class="row items-center no-wrap">
            <q-icon name="format_list_bulleted" size="32px" class="q-mr-md" />
            <div>
              <div class="text-subtitle2">Todos</div>
              <div class="text-h6">{{ todoCount }}</div>
            </div>
          </q-card-section>
        </q-card>
      </div>

      <div class="col-12 col-sm-6 col-md-3">
        <q-card flat bordered>
          <q-card-section class="row items-center no-wrap">
            <q-icon name="inventory" color="secondary" size="32px" class="q-mr-md" />
            <div>
              <div class="text-subtitle2">Total Items</div>
              <div class="text-h6">{{ meta.totalCount }}</div>
            </div>
          </q-card-section>
        </q-card>
      </div>

      <div class="col-12 col-sm-6 col-md-3">
        <q-card flat bordered>
          <q-card-section class="row items-center no-wrap">
            <q-icon
              :name="active ? 'toggle_on' : 'toggle_off'"
              :color="active ? 'positive' : 'grey-6'"
              size="32px"
              class="q-mr-md"
            />
            <div>
              <div class="text-subtitle2">Status</div>
              <div class="text-h6 text-green">{{ active ? 'Active' : 'Inactive' }}</div>
            </div>
          </q-card-section>
        </q-card>
      </div>

      <div class="col-12 col-sm-6 col-md-3">
        <q-card flat bordered>
          <q-card-section class="row items-center no-wrap">
            <q-icon name="mouse" color="deep-orange" size="32px" class="q-mr-md" />
            <div>
              <div class="text-subtitle2">Clicks</div>
              <div class="text-h6">{{ clickCount }}</div>
            </div>
          </q-card-section>
        </q-card>
      </div>
    </div>

    <!-- Todo List Card -->
    <q-card bordered flat>
      <q-card-section class="row items-center justify-between">
        <div class="text-subtitle1">Todo List</div>
        <div class="row items-center">
          <q-input
            dense
            outlined
            v-model="filter"
            placeholder="Filter todos..."
            debounce="250"
            class="q-mr-sm"
            style="min-width: 180px"
          />
          <q-btn dense icon="refresh" round flat @click="resetClicks" :disable="clickCount === 0" />
        </div>
      </q-card-section>

      <q-separator />

      <q-list separator>
        <q-item v-for="todo in filteredTodos" :key="todo.id" clickable @click="increment">
          <q-item-section avatar>
            <q-avatar color="primary" text-color="white" size="32px">{{ todo.id }}</q-avatar>
          </q-item-section>
          <q-item-section>
            <q-item-label>{{ todo.content }}</q-item-label>
            <q-item-label caption>Tap to increase click count</q-item-label>
          </q-item-section>
          <q-item-section side>
            <q-badge color="secondary" align="middle">ID: {{ todo.id }}</q-badge>
          </q-item-section>
        </q-item>

        <q-item v-if="filteredTodos.length === 0">
          <q-item-section>
            <q-item-label caption>No todos found.</q-item-label>
          </q-item-section>
        </q-item>
      </q-list>

      <q-separator />
      <q-card-section>
        <div class="text-caption text-grey-7">Progress</div>
        <q-linear-progress :value="progress" color="accent" track-color="grey-3" class="q-mt-sm" />
      </q-card-section>
    </q-card>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import type { Todo, Meta } from './models';

interface Props {
  title: string;
  todos?: Todo[];
  meta: Meta;
  active: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  todos: () => [],
});

const clickCount = ref(0);
function increment() {
  clickCount.value += 1;
}
function resetClicks() {
  clickCount.value = 0;
}

const filter = ref('');
const filteredTodos = computed(() => {
  const f = filter.value.trim().toLowerCase();
  if (!f) return props.todos;
  return props.todos.filter(
    (t) => String(t.id).toLowerCase().includes(f) || t.content.toLowerCase().includes(f),
  );
});

const todoCount = computed(() => props.todos.length);
const progress = computed(() => {
  const total = props.meta.totalCount || 0;
  if (total <= 0) return 0;
  const val = todoCount.value / total;
  return Math.min(1, Math.max(0, val));
});
</script>
