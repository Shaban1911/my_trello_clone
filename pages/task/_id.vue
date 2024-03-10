<template>
  <div>
    <h1>Task Details</h1>
    <TaskDetails :taskId="taskId" />
  </div>
</template>

<script>
import TaskDetails from "@/components/taskdetails.vue";

export default {
  name: 'taskDetails',
  components: {
    taskdetails
  },
  data() {
    return {
      taskId: this.$route.params.id,
    };
  },
};
</script>
