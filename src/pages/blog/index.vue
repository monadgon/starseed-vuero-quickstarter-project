<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useHead } from '@vueuse/head'

// we import our useApi helper
import { useApi } from '/@src/composable/useApi'

// We may want to retrieve the posts from an API
// as we are using typescript, it is a good practice to always define our types
interface Article {
  id: string
  title: string
  slug: string
}

// articles and fetchArticles variables can be provided by a composable function
const articles = ref<Article[]>([]) // we know that the articles will be an array of Article

async function fetchArticles() {
  const api = useApi()

  try {
    const { data } = await api.get<Article[]>('/articles') // we know that our api respond with an array of Article
    articles.value = data
  } catch (error) {
    // here we can handle the error
    console.error(error)
  }
}

// We trigger the fetchArticles function when the component is mounted
onMounted(fetchArticles)

// don't forget to setup our page meta
useHead({
  title: 'My blog',
})
</script>

<template>
  <LandingLayout theme="light">
    <div class="blog-list-wrapper">
      <!-- This is a simple page example -->
      <h1>My blog posts:</h1>
      <ul>
        <li v-for="article in articles" :key="article.id">
          <!-- Here we are linking to the article detail page with a dynamic "slug" parameter -->
          <RouterLink
            :to="{
              name: 'blog-slug',
              params: {
                slug: article.slug,
              },
            }"
          >
            {{ article.title }}
          </RouterLink>
        </li>
      </ul>
    </div>
  </LandingLayout>
</template>

<style lang="scss" scoped>
.blog-list-wrapper {
  // Here we can add custom styles for the blog page
  // They will be only applied to this component
}
</style>
