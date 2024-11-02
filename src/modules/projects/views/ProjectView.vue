<template>
  <div class="w-full">
    <section m-2>
      <bread-crumbs :name="project?.name ?? 'No name'" />
    </section>
    <section class="m-2">
      <div class="overflow-x-auto">
        <table class="table">
          <thead>
            <tr>
              <th class="w-14">Completed</th>
              <th>Task</th>
              <th>Completed at</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="task in project?.tasks" :key="task.id" class="hover">
              <th>
                <input
                  type="checkbox"
                  :checked="!!task.completedAt"
                  class="checkbox checkbox-primary"
                  @change="projectStore.toggleTask(project?.id ?? '', task.id)"
                />
              </th>
              <td>{{ task.name }}</td>
              <td>{{ task.completedAt }}</td>
            </tr>
            <tr class="hover">
              <th></th>
              <td>
                <input
                  type="text"
                  class="input input-primary w-full opacity-60 transition-all hover:opacity-100 focus:opacity-100"
                  placeholder="New Task"
                  v-model="newTask"
                  @keyup.enter="addTask"
                />
              </td>
              <td></td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>
  </div>
</template>

<script lang="ts" setup>
import { useProjectStore } from '../store/projects.store';
import { ref, watch } from 'vue';
import { useRouter } from 'vue-router';
import BreadCrumbs from '@/modules/common/components/BreadCrumbs.vue';
import type { Project } from '../interfaces/project.interface';

interface Props {
  id: string;
}

const router = useRouter();
const props = defineProps<Props>();
const projectStore = useProjectStore();
const project = ref<Project | null>();
const newTask = ref('');

const addTask = () => {
  if (!project.value) return;

  projectStore.addTaskToProject(project.value?.id, newTask.value);
  newTask.value = '';
};

watch(
  () => props.id,
  () => {
    project.value = projectStore.projectList.find(
      project => project.id === props.id,
    );
    if (!project.value) {
      router.replace('/');
    }
  },
  {
    immediate: true,
  },
);
</script>
