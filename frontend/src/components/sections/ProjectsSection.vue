<script setup>
import { computed, onMounted, ref } from 'vue';
import { useI18n } from 'vue-i18n';
import Carousel from '../TheCarousel.vue';

const { t } = useI18n();
const GITHUB_USERNAME = import.meta.env.VITE_GITHUB_USERNAME || 'brunnoferreiraa';
const MAX_REPOS = 9;

const data = [
  {
    "id": "project1",
    "technologies": ["Vue.js", "Vuex", "Tailwind CSS", "Firebase"]
  },
  {
    "id": "project2",
    "technologies": ["Vue.js", "Node.js", "Express", "MongoDB"]
  },
  {
    "id": "project3",
    "technologies": ["Vue.js", "Tailwind CSS", "Vite"]
  },
  {
    "id": "project4",
    "technologies": ["Vue.js", "Firebase", "Vuex", "Vue Router"]
  },
  {
    "id": "project5",
    "technologies": ["Vue.js", "Firebase", "Vuex", "Vue Router"]
  }
];

const githubProjects = ref([]);

const fallbackProjects = computed(() =>
  data.map(project => ({
    ...project,
    title: t(`sections.projects.items.${project.id}.title`),
    description: t(`sections.projects.items.${project.id}.description`),
    link: '#',
  })),
);

const projectsWithTranslations = computed(() =>
  githubProjects.value.length > 0 ? githubProjects.value : fallbackProjects.value,
);

const normalizeTechnologies = repo => {
  const items = [];

  if (repo.language) items.push(repo.language);
  if (Array.isArray(repo.topics)) {
    items.push(...repo.topics.slice(0, 3).map(topic => topic.replace(/-/g, ' ')));
  }

  return [...new Set(items)].slice(0, 4);
};

const loadGithubProjects = async () => {
  try {
    const response = await fetch(
      `https://api.github.com/users/${GITHUB_USERNAME}/repos?sort=updated&per_page=100`,
      {
        headers: {
          Accept: 'application/vnd.github+json',
        },
      },
    );

    if (!response.ok) return;

    const repos = await response.json();
    const filteredRepos = repos
      .filter(repo => !repo.fork)
      .sort((a, b) => new Date(b.updated_at) - new Date(a.updated_at))
      .slice(0, MAX_REPOS)
      .map(repo => ({
        id: repo.id,
        title: repo.name,
        description: repo.description || 'Projeto disponível no GitHub.',
        technologies: normalizeTechnologies(repo),
        link: repo.html_url,
      }));

    if (filteredRepos.length > 0) {
      githubProjects.value = filteredRepos;
    }
  } catch {
    // Keep fallback projects when GitHub API is unavailable.
  }
};

onMounted(() => {
  loadGithubProjects();
});

const responsiveOptions = ref([
  {
    breakpoint: '1024px',
    numVisible: 3,
    numScroll: 1
  },
  {
    breakpoint: '768px',
    numVisible: 2,
    numScroll: 1
  },
  {
    breakpoint: '560px',
    numVisible: 1,
    numScroll: 1
  }
]);
</script>

<template>
  <!--  flex container to center the section vertically -->
  <div class="flex items-center justify-center min-h-screen">
    <div class="min-w-0 max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
      <h2 class="text-xl sm:text-2xl font-bold dark:text-slate-100 text-slate-900 mb-4 sm:mb-6 text-center">
        {{ t('sections.projects.heading') }}
      </h2>

      <!-- Mobile Carousel: One item visible -->
      <Carousel v-if="projectsWithTranslations.length > 2" :items="projectsWithTranslations" :numVisible="1" :numScroll="1" circular
        :responsiveOptions="responsiveOptions" class="pb-6 sm:hidden">
        <template #item="slotProps">
          <div class="p-1 h-full">
            <div class="bg-amber-50/70 dark:bg-slate-900/80 border border-amber-100 dark:border-slate-700 rounded-lg shadow-md p-3 h-full flex flex-col">
              <h3 class="text-base font-semibold mb-2 text-slate-900 dark:text-slate-100">
                {{ slotProps.data.title }}
              </h3>
              <p class="text-slate-600 dark:text-slate-300 text-xs mb-3 flex-grow">
                {{ slotProps.data.description }}
              </p>
              <div class="mt-auto">
                <div class="flex flex-wrap gap-1 mb-2">
                  <span v-for="tech in slotProps.data.technologies.slice(0, 4)" :key="tech"
                    class="bg-cyan-100 text-cyan-900 text-xs font-medium px-2 py-0.5 rounded dark:bg-cyan-900 dark:text-cyan-200">
                    {{ tech }}
                  </span>
                </div>
                <a :href="slotProps.data.link" target="_blank" rel="noopener noreferrer"
                  class="text-cyan-700 dark:text-cyan-300 hover:underline text-xs inline-block">
                  {{ t('sections.projects.viewButton') }}
                </a>
              </div>
            </div>
          </div>
        </template>
      </Carousel>

      <!-- Tablet Carousel: Two items visible -->
      <Carousel v-if="projectsWithTranslations.length > 2" :items="projectsWithTranslations" :numVisible="2" :numScroll="1" circular
        :responsiveOptions="responsiveOptions" class="pb-6 hidden sm:block lg:hidden">
        <template #item="slotProps">
          <div class="p-2 h-full">
            <div class="bg-amber-50/70 dark:bg-slate-900/80 border border-amber-100 dark:border-slate-700 rounded-lg shadow-md p-4 h-full flex flex-col">
              <h3 class="text-lg font-semibold mb-2 text-slate-900 dark:text-slate-100">
                {{ slotProps.data.title }}
              </h3>
              <p class="text-slate-600 dark:text-slate-300 text-sm mb-4 flex-grow">
                {{ slotProps.data.description }}
              </p>
              <div class="mt-auto">
                <div class="flex flex-wrap gap-1 mb-3">
                  <span v-for="tech in slotProps.data.technologies.slice(0, 4)" :key="tech"
                    class="bg-cyan-100 text-cyan-900 text-xs font-medium px-2 py-0.5 rounded dark:bg-cyan-900 dark:text-cyan-200">
                    {{ tech }}
                  </span>
                </div>
                <a :href="slotProps.data.link" target="_blank" rel="noopener noreferrer"
                  class="text-cyan-700 dark:text-cyan-300 hover:underline text-sm inline-block">
                  {{ t('sections.projects.viewButton') }}
                </a>
              </div>
            </div>
          </div>
        </template>
      </Carousel>

      <!-- Desktop Carousel: Three items visible -->
      <Carousel v-if="projectsWithTranslations.length > 2" :items="projectsWithTranslations" :numVisible="3" :numScroll="1" circular
        :responsiveOptions="responsiveOptions" class="pb-6 hidden lg:block">
        <template #item="slotProps">
          <div class="p-2 h-full">
            <div class="bg-amber-50/70 dark:bg-slate-900/80 border border-amber-100 dark:border-slate-700 rounded-lg shadow-md p-4 h-full flex flex-col">
              <h3 class="text-lg md:text-xl font-semibold mb-2 text-slate-900 dark:text-slate-100">
                {{ slotProps.data.title }}
              </h3>
              <p class="text-slate-600 dark:text-slate-300 text-sm md:text-base mb-4 flex-grow">
                {{ slotProps.data.description }}
              </p>
              <div class="mt-auto">
                <div class="flex flex-wrap gap-1 mb-3">
                  <span v-for="tech in slotProps.data.technologies.slice(0, 4)" :key="tech"
                    class="bg-cyan-100 text-cyan-900 text-xs font-medium px-2 py-0.5 rounded dark:bg-cyan-900 dark:text-cyan-200">
                    {{ tech }}
                  </span>
                </div>
                <a :href="slotProps.data.link" target="_blank" rel="noopener noreferrer"
                  class="text-cyan-700 dark:text-cyan-300 hover:underline text-sm md:text-base inline-block">
                  {{ t('sections.projects.viewButton') }}
                </a>
              </div>
            </div>
          </div>
        </template>
      </Carousel>

      <!-- Grid view for when less than 2 projects -->
      <div v-else class="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
        <div v-for="project in projectsWithTranslations" :key="project.id"
          class="bg-amber-50/70 dark:bg-slate-900/80 border border-amber-100 dark:border-slate-700 rounded-lg shadow-md p-4 flex flex-col h-full">
          <h3 class="text-base sm:text-lg font-semibold mb-2 text-slate-900 dark:text-slate-100">
            {{ project.title }}
          </h3>
          <p class="text-slate-600 dark:text-slate-300 text-sm mb-4 flex-grow">{{ project.description }}</p>
          <div class="mt-auto">
            <div class="flex flex-wrap gap-1 mb-3">
              <span v-for="tech in project.technologies.slice(0, 4)" :key="tech"
                class="bg-cyan-100 text-cyan-900 text-xs font-medium px-2 py-0.5 rounded dark:bg-cyan-900 dark:text-cyan-200">
                {{ tech }}
              </span>
            </div>
            <a :href="project.link" target="_blank" rel="noopener noreferrer"
              class="text-cyan-700 dark:text-cyan-300 hover:underline text-xs sm:text-sm">
              {{ t('sections.projects.viewButton') }}
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
