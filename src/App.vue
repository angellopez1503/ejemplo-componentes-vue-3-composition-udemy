<script setup>
import BlogPost from "./components/BlogPost.vue";
import { ref, computed, onMounted } from "vue";
import PaginatePost from "./components/PaginatePost.vue";
import LoadingSpinner from "./components/LoadingSpinner.vue";

const posts = ref([]);

const favorito = ref("");
const postXPage = 10;
const inicio = ref(0);
const fin = ref(postXPage);
const loading = ref(false);

const cambiarFavorito = (title) => {
  favorito.value = title;
};

const next = () => {
  inicio.value += postXPage;
  fin.value += postXPage;
};

const prev = () => {
  inicio.value -= postXPage;
  fin.value -= postXPage;
};

const fetchData = async () => {
  loading.value = true;
  try {
    const res = await fetch("https://jsonplaceholder.typicode.com/posts");
    posts.value = await res.json();
  } catch (error) {
    console.error(error);
  } finally {
    loading.value = false;
  }
};

fetchData();

// onMounted(async () => {
//   loading.value = true
//   try {
//     const res = await fetch("https://jsonplaceholder.typicode.com/posts");
//     posts.value = await res.json()
//   } catch (error) {
//     console.error(error)
//   } finally {

//      loading.value = false

//   }
// })

// fetch("https://jsonplaceholder.typicode.com/posts")
//   .then((res) => res.json())
//   .then((data) => {
//     posts.value = data;
//   })
//   .catch((e) => {
//     console.log(e);
//   })
//   .finally(() => {
//     loading.value = false;
//   });

const maxLength = computed(() => {
  return posts.value.length;
});
</script>
<template>
  <LoadingSpinner v-if="loading" />
  <div class="container" v-else>
    <h1>App</h1>
    <h2>Mi post favorito : {{ favorito }}</h2>

    <PaginatePost
      @next="next"
      @prev="prev"
      class="mb-2"
      :inicio="inicio"
      :fin="fin"
      :maxLength="maxLength"
    />

    <BlogPost
      v-for="post in posts.slice(inicio, fin)"
      :key="post.id"
      :title="post.title"
      :id="post.id"
      :body="post.body"
      :cambiarFavorito="cambiarFavorito"
      class="mb-2"
    />
  </div>
</template>
