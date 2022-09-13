<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useHead } from '@vueuse/head'

// we import our useApi helper
import { useApi } from '/@src/composable/useApi'

// We want to retrieve the post from the API where the slug matches the current slug
const route = useRoute()
const currentSlug = (route.params?.slug as string) ?? ''

// Here we will have a more detailed Article
interface Article {
  id: string
  title: string
  slug: string
  content: string
  comments: string[]
}

const article = ref<Article>()

async function fetchArticleBySlug(slug: string) {
  const api = useApi()
  const router = useRouter()

  try {
    const { data } = await api.get<Article[]>(`/articles?slug=${slug}`)

    if (!data.length) {
      // the article does not exist, we replace the route to the 404 page
      // we also pass the original url to the 404 page as a query parameter
      // http://localhost:3000/article-not-found?original=/blog/a-fake-slug
      router.replace({
        name: 'all', // this will match the ./src/pages/[...all].vue route
        params: {
          all: 'article-not-found',
        },
        query: {
          original: router.currentRoute.value.fullPath,
        },
      })
    }

    article.value = data[0]
  } catch (error) {
    console.error(error)
  }
}

// We trigger the fetchArticles function when the component is mounted
onMounted(() => fetchArticleBySlug(currentSlug))

// here we setup our page meta with our article data
useHead({
  title: computed(() => article.value?.title ?? 'Loading article...'),
})
</script>

<template>
  <LandingLayout theme="light">
    <div class="blog-detail-wrapper">
      <!--
            Page content goes here
    
            You can see more complete pages content samples from 
            files in /src/components/pages directory
          -->

      <h1>{{ article?.title }}</h1>
      <div>{{ article?.content }}</div>
    </div>
  </LandingLayout>
</template>

<style lang="scss" scoped>
.blog-detail-wrapper {
  // Here we can add custom styles for the blog page
  // They will be only applied to this component
}
</style>
