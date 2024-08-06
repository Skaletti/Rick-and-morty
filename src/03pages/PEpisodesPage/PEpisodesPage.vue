<template>
  <div class="container">
    <div class="episode-page">
      <h1 class="episode-page__title">Rick and Morty Episodes</h1>
      <div v-if="isLoading" class="episode-page__loading">Loading...</div>
      <div v-else>
        <table class="episode-page__table">
          <thead>
            <tr>
              <th>ID</th>
              <th>Name</th>
              <th>Air Date</th>
              <th>Episode Code</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="episode in episodes" :key="episode.id">
              <td>
                <router-link :to="`${ROUTE_NAMES.episode}/${episode.id}`">{{ episode.id }}</router-link>
              </td>
              <td>
                <router-link :to="`${ROUTE_NAMES.episode}/${episode.id}`">
                  {{ episode.name }}
                </router-link>
              </td>
              <td>{{ episode.air_date }}</td>
              <td>{{ episode.episode }}</td>
            </tr>
          </tbody>
        </table>
      </div>
      <s-pagination
        v-if="episodeInfo"
        class="episode-page__pagination"
        :page-count="episodeInfo.info.pages || 0"
        @current-change="fetchEpisodes"
      />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { episodeApi, EpisodeType, EpisodeApiResponseType } from 'entities/EEpisodes'
import { ROUTE_NAMES } from 'shared/constants'
import { SPagination } from 'shared/ui'
import { onMounted, ref } from 'vue'

const isLoading = ref(false)
const episodes = ref<EpisodeType[]>([])
const episodeInfo = ref<EpisodeApiResponseType | null>(null)
const currentPage = ref(1)

const fetchEpisodes = async (page = currentPage.value) => {
  isLoading.value = true

  try {
    const response = await episodeApi.getAllEpisodes(page)

    episodes.value = response.results

    episodeInfo.value = response

    console.log(episodeInfo.value.info.pages)
  } catch (error) {
    console.error(error)
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  fetchEpisodes()
})
</script>

<style scoped lang="scss">
.episode-page {
  &__title {
    text-align: center;
    margin-bottom: 20px;
  }

  &__loading {
    font-size: 24px;
    text-align: center;
  }

  &__table {
    width: 100%;
    border-collapse: collapse;

    th,
    td {
      border: 1px solid #ddd;
      font-size: 12px;
      text-decoration: none;
      color: #f2f2f2;
      padding: 4px;

      @include responsive(md) {
        font-size: 16px;
        padding: 8px;
      }
    }

    th {
      text-align: left;
      color: var(--color-black);
      background-color: #f2f2f2;
    }
  }

  &__pagination {
    display: flex;
    justify-content: center;
    margin-top: 20px;
  }
}
</style>
