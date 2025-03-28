<script setup lang="ts">
import type { DropdownItem } from '#ui/types'

const { loggedIn, user, clear } = useUserSession()
const colorMode = useColorMode()

watch(loggedIn, () => {
  if (!loggedIn.value) {
    navigateTo('/')
  }
})

const isDarkMode = computed({
  get: () => colorMode.preference === 'dark',
  set: () =>
    (colorMode.preference = colorMode.value === 'dark' ? 'light' : 'dark')
})

useHead({
  htmlAttrs: { lang: 'en' },
  link: [{ rel: 'icon', href: '/icon.png' }]
})

useSeoMeta({
  viewport: 'width=device-width, initial-scale=1, maximum-scale=1',
  title: 'Atidone',
  description:
    'A Nuxt demo hosted with edge-side rendering, authentication and queyring a Cloudflare D1 database',
  ogImage: '/social-image.png',
  twitterImage: '/social-image.png',
  twitterCard: 'summary_large_image'
})

const items = [
  [
    {
      label: 'Logout',
      icon: 'i-heroicons-arrow-left-on-rectangle',
      click: clear
    }
  ]
] satisfies DropdownItem[][]
</script>

<template>
  <UApp>
    <UContainer class="min-h-screen flex flex-col my-4">
      <div class="mb-2 text-right">
        <UButton
          color="neutral"
          :icon="
            $colorMode.preference === 'dark' || $colorMode.preference === 'system'
              ? 'i-heroicons-moon'
              : 'i-heroicons-sun'
          "
          @click="isDarkMode = !isDarkMode"
        />
      </div>

      <UCard>
        <template #header>
          <div class="flex justify-between">
            <h3 class="text-lg font-semibold leading-6">
              <NuxtLink to="/">
                Atidone
              </NuxtLink>
            </h3>
            <UButton
              v-if="!loggedIn"
              to="/api/auth/github"
              icon="i-simple-icons-github"
              label="Login with GitHub"
              size="xs"
              external
            />
            <div
              v-else
              class="flex flex-wrap -mx-2 sm:mx-0"
            >
              <UButton
                to="/todos"
                icon="i-heroicons-list-bullet"
                label="Todos"
                :color="$route.path === '/todos' ? 'primary' : 'neutral'"
                variant="ghost"
              />
              <UButton
                to="/optimistic-todos"
                icon="i-heroicons-sparkles"
                label="Optimistic Todos"
                :color="$route.path === '/optimistic-todos' ? 'primary' : 'neutral'"
                variant="ghost"
              />
              <UDropdownMenu
                v-if="user"
                :items="items"
              >
                <UButton
                  color="neutral"
                  variant="ghost"
                  trailing-icon="i-heroicons-chevron-down-20-solid"
                >
                  <UAvatar
                    :src="`https://github.com/${user.login}.png`"
                    :alt="user.login"
                    size="3xs"
                  />
                  {{ user.login }}
                </UButton>
              </UDropdownMenu>
            </div>
          </div>
        </template>
        <NuxtPage />
      </UCard>

      <footer class="text-center mt-2">
        <NuxtLink
          href="https://github.com/atinux/atidone"
          target="_blank"
          class="text-sm text-gray-500 hover:text-gray-700"
        >
          GitHub
        </NuxtLink>
        ·
        <NuxtLink
          href="https://twitter.com/atinux"
          target="_blank"
          class="text-sm text-gray-500 hover:text-gray-700"
        >
          Twitter
        </NuxtLink>
      </footer>
    </UContainer>
  </UApp>
</template>

<style lang="postcss">
body {
  @apply font-sans text-gray-950 bg-gray-50 dark:bg-gray-950 dark:text-gray-50;
}
</style>
