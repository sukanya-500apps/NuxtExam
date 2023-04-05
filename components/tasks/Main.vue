<template>
  <div>
    <div>
      <TasksList :allTasks="allTasks"/>
      <!-- <TasksAdd @add="add"/> -->
           <!--<TasksEdit /> -->
    </div>
  </div>
</template>
<script setup lang="ts">
const authHeader = {
  Authorization:
    "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1IjoiNjgzZGM0MDhmMjFmNDIyYjk1NmY5YjQxMzZmYjRjYTMiLCJkIjoiMTY4MDA0NiIsInIiOiJzYSIsInAiOiJmcmVlIiwiYSI6ImZpbmRlci5pbyIsImwiOiJ1czEiLCJleHAiOjE2ODMxNzY2NTh9.mQOm1NxFMiIcVmhpz_FKpzN-Cn3rRZWxDG3u9NbQg-M",
};
const { pending, data: task } = useLazyFetch(`https://v1-orm-lib.mars.hipso.cc/tasks/CONTACTS/1`, {
  method: "GET",
  headers: authHeader,
});
const allTasks = task.value;
// Add - add to database and create task
const add = async (name: string) => {
  await useLazyFetch(`https://v1-orm-lib.mars.hipso.cc/tasks/CONTACTS/1`, {
    method: "POST",
    headers: authHeader,
    body: {
      project_id: "1",
      entity_id: "1",
      owner_id: "1",
      name: "sukanya",
      category: "new",
      status: "NEW",
      description: "hello",
      entity: "CONTACTS",
      is_active: "ACTIVE",
      due_date: "2023-04-05",
      kanban_order: [],
    },
  });
  // Push new tag into existing tags
  allTasks.push(name);
};
</script>
