<script lang="ts" setup>
import navItems from '@/navigation/vertical'
import { themeConfig } from '@themeConfig'

// Components
import Footer from '@/layouts/components/Footer.vue'
// import NavbarThemeSwitcher from '@/layouts/components/NavbarThemeSwitcher.vue'
import UserProfile from '@/layouts/components/UserProfile.vue'
import NavBarI18n from '@core/components/I18n.vue'

// @layouts plugin
import { VerticalNavLayout } from '@layouts'
</script>

<template>
  <VerticalNavLayout :nav-items="navItems">
    <!-- 👉 navbar -->
    <template #navbar="{ toggleVerticalOverlayNavActive }">
      <div class="d-flex h-100 align-center">
        <IconBtn id="vertical-nav-toggle-btn" class="ms-n3 d-lg-none" @click="toggleVerticalOverlayNavActive(true)">
          <VIcon size="26" icon="tabler-menu-2" />
        </IconBtn>

        <!-- 👉 Breadcrumbs -->
        <div class="d-none d-sm-flex align-center gap-4 ms-lg-2 ms-4">
          <RouterLink to="/" class="d-flex align-center text-primary text-decoration-none">
            <VIcon icon="tabler-home" size="28" stroke-width="2" />
          </RouterLink>
          <VIcon icon="tabler-chevron-right" size="24" class="text-disabled" />
          <span class="text-h5 font-weight-medium text-capitalize fs-14"
            style="color: #5a5c69; font-family: Montserrat, sans-serif; letter-spacing: 0;">
            {{ $route.name === 'root' ? 'Home' : String($route.name || '').replace('-', ' ') }}
          </span>
        </div>

        <VSpacer />

        <NavBarI18n v-if="themeConfig.app.i18n.enable && themeConfig.app.i18n.langConfig?.length"
          :languages="themeConfig.app.i18n.langConfig" />
        <div class="d-none d-sm-flex flex-column me-2">
          <VListItemTitle class="font-weight-semibold">
            John Doe
          </VListItemTitle>
          <VListItemSubtitle class="text-end">Admin</VListItemSubtitle>
        </div>
        <UserProfile />
      </div>
    </template>

    <!-- 👉 Pages -->
    <slot />

    <!-- 👉 Footer -->
    <template #footer>
      <Footer />
    </template>

    <!-- 👉 Customizer -->
    <!-- <TheCustomizer /> -->
  </VerticalNavLayout>
</template>
