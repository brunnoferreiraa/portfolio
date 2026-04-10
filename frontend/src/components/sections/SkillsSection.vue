<script setup>
import { useI18n } from 'vue-i18n';

const { t } = useI18n();

const skills = [
  {
    id: 'react',
    label: 'ReactJS',
    icon: 'https://cdn.jsdelivr.net/gh/devicons/devicon/icons/react/react-original.svg',
  },
  {
    id: 'vue',
    label: 'VueJS',
    icon: 'https://cdn.jsdelivr.net/gh/devicons/devicon/icons/vuejs/vuejs-original.svg',
  },
  {
    id: 'angular',
    label: 'Angular',
    icon: 'https://cdn.jsdelivr.net/gh/devicons/devicon/icons/angularjs/angularjs-original.svg',
  },
  {
    id: 'node',
    label: 'Node.js (Backend)',
    icon: 'https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nodejs/nodejs-original.svg',
  },
  {
    id: 'python',
    label: 'Python',
    icon: 'https://cdn.jsdelivr.net/gh/devicons/devicon/icons/python/python-original.svg',
  },
];
</script>

<template>
  <div class="flex items-center justify-center min-h-screen w-full">
    <div class="w-full max-w-5xl px-4 sm:px-6">
      <h2 class="text-2xl md:text-3xl font-bold text-slate-900 dark:text-slate-100 mb-3 text-center">
        {{ t('sections.skills.heading') }}
      </h2>
      <p class="text-slate-600 dark:text-slate-300 text-center mb-10">
        {{ t('sections.skills.description') }}
      </p>

      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-4 md:gap-6">
        <article
          v-for="skill in skills"
          :key="skill.id"
          class="bg-amber-50/70 dark:bg-slate-900/80 border border-amber-100 dark:border-slate-700 rounded-xl p-6 flex flex-col items-center text-center shadow-md hover:shadow-lg transition-all duration-300"
        >
          <img :src="skill.icon" :alt="skill.label" class="w-16 h-16 mb-4" loading="lazy" />
          <h3 class="text-lg font-semibold text-slate-900 dark:text-slate-100 mb-1">
            {{ skill.label }}
          </h3>
          <p class="text-sm text-slate-600 dark:text-slate-300">
            {{ t(`sections.skills.items.${skill.id}`) }}
          </p>
        </article>
      </div>
    </div>
  </div>
</template>
