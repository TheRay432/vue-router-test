<script>
import { onMounted, onUnmounted, reactive, ref } from "@vue/runtime-core";
import { useRoute, useRouter } from "vue-router";
import axios from "axios";
export default {
  setup() {
    const route = useRoute();
    const router = useRouter();
    const isError = ref(false);
    let timer = null;
    const course = reactive({ data: {} });
    onMounted(async () => {
      try {
        const res = await axios.get(
          `https://vue-lessons-api.herokuapp.com/courses/${route.params.id}`
        );
        console.log(route.params.id);
        course.data = res.data.data[0];
      } catch (err) {
        isError.value = true;
        course.data["error_message"] = err.response.data.error_message;
        timer = setTimeout(() => {
          /*  router.push({ path: "/Courses" }); */
          router.go(-1);
        }, 3000);
      }
    });
    onUnmounted(() => {
      clearTimeout(timer);
    });
    return { course, isError };
  },
};
</script>
<template>
  <div>
    <div v-if="!isError">
      <h1>{{ course.data.name }}</h1>
      <h2 v-show="Object.keys(course.data).length !== 0">
        NTD:{{ course.data.money }}
      </h2>
      <img :src="course.data.photo" alt="" />
      <div v-if="Object.keys(course.data).length !== 0">
        <img :src="course.data.teacher.img" alt="" />
        <p>{{ course.data.teacher.name }}</p>
      </div>
    </div>

    <h1 v-if="isError">{{ course.data.error_message }}</h1>
  </div>
</template>

<style></style>
