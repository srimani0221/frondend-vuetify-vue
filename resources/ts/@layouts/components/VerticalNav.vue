<script lang="ts" setup>
import { useConfigStore } from '@core/stores/config'
import { layoutConfig } from '@layouts'
import { VerticalNavGroup, VerticalNavLink, VerticalNavSectionTitle } from '@layouts/components'
import { useLayoutConfigStore } from '@layouts/stores/config'
import { injectionKeyIsVerticalNavHovered } from '@layouts/symbols'
import type { NavGroup, NavLink, NavSectionTitle, VerticalNavItems } from '@layouts/types'
import type { Component } from 'vue'
import { PerfectScrollbar } from 'vue3-perfect-scrollbar'

interface Props {
  tag?: string | Component
  navItems: VerticalNavItems
  isOverlayNavActive: boolean
  toggleIsOverlayNavActive: (value: boolean) => void
}

const props = withDefaults(defineProps<Props>(), {
  tag: 'aside',
})

const refNav = ref()

const isHovered = useElementHover(refNav)

provide(injectionKeyIsVerticalNavHovered, isHovered)

const configStore = useLayoutConfigStore()
const themeConfigStore = useConfigStore()

const resolveNavItemComponent = (item: NavLink | NavSectionTitle | NavGroup): unknown => {
  if ('heading' in item)
    return VerticalNavSectionTitle
  if ('children' in item)
    return VerticalNavGroup

  return VerticalNavLink
}

/*
  ℹ️ Close overlay side when route is changed
  Close overlay vertical nav when link is clicked
*/
const route = useRoute()

watch(() => route.name, () => {
  props.toggleIsOverlayNavActive(false)
})

const isVerticalNavScrolled = ref(false)
const updateIsVerticalNavScrolled = (val: boolean) => isVerticalNavScrolled.value = val

const handleNavScroll = (evt: Event) => {
  isVerticalNavScrolled.value = (evt.target as HTMLElement).scrollTop > 0
}

const hideTitleAndIcon = configStore.isVerticalNavMini(isHovered)
</script>

<template>
  <Component
    :is="props.tag"
    ref="refNav"
    data-allow-mismatch
    class="layout-vertical-nav"
    :class="[
      {
        'overlay-nav': configStore.isLessThanOverlayNavBreakpoint,
        'hovered': isHovered,
        'visible': isOverlayNavActive,
        'scrolled': isVerticalNavScrolled,
      },
    ]"
  >
    <!-- 👉 Header -->
    <div class="nav-header">
      <slot name="nav-header">
        <RouterLink
          to="/"
          class="app-logo app-title-wrapper"
        >
          <VIcon icon="tabler-brand-vite" size="30" color="primary" />

          <Transition name="vertical-nav-app-title">
            <h1
              v-show="!hideTitleAndIcon"
              class="app-logo-title"
              style="font-size: 1.125rem;"
            >
              Frontend Task
            </h1>
          </Transition>
        </RouterLink>
        <!-- 👉 Vertical nav actions -->
        <!-- Show toggle collapsible in >md and close button in <md -->
        <div class="header-action">
          <Component
            :is="layoutConfig.app.iconRenderer || 'div'"
            v-show="configStore.isVerticalNavCollapsed"
            class="d-none nav-unpin"
            :class="configStore.isVerticalNavCollapsed && 'd-lg-block'"
            icon="tabler-layout-sidebar-left-collapse"
            size="20"
            @click="configStore.isVerticalNavCollapsed = !configStore.isVerticalNavCollapsed"
          />
          <Component
            :is="layoutConfig.app.iconRenderer || 'div'"
            v-show="!configStore.isVerticalNavCollapsed"
            class="d-none nav-pin"
            :class="!configStore.isVerticalNavCollapsed && 'd-lg-block'"
            icon="tabler-layout-sidebar-right-collapse"
            size="20"
            @click="configStore.isVerticalNavCollapsed = !configStore.isVerticalNavCollapsed"
          />
          <Component
            :is="layoutConfig.app.iconRenderer || 'div'"
            class="d-lg-none"
            v-bind="layoutConfig.icons.close"
            @click="toggleIsOverlayNavActive(false)"
          />
        </div>
      </slot>
    </div>
    <slot name="before-nav-items">
      <div class="vertical-nav-items-shadow" />
    </slot>
    <slot
      name="nav-items"
      :update-is-vertical-nav-scrolled="updateIsVerticalNavScrolled"
    >
      <PerfectScrollbar
        :key="String(configStore.isAppRTL)"
        tag="ul"
        class="nav-items"
        :options="{ wheelPropagation: false }"
        @ps-scroll-y="handleNavScroll"
      >
        <Component
          :is="resolveNavItemComponent(item)"
          v-for="(item, index) in navItems"
          :key="index"
          :item="item"
        />
      </PerfectScrollbar>
    </slot>
    <slot name="after-nav-items">
      <div 
        class="d-flex flex-column mt-auto transition-all"
        :class="hideTitleAndIcon ? 'pa-2 gap-2' : 'pa-4 gap-4'"
      >
        <!-- User Profile Card -->
        <VCard elevation="0" color="background" class="rounded-lg w-100 overflow-hidden" :class="hideTitleAndIcon ? 'pa-2' : 'pa-4'">
          <div class="d-flex align-center" :class="hideTitleAndIcon ? 'justify-center' : ''">
            <VBadge dot color="success" offset-x="3" offset-y="3">
              <VAvatar size="40" image="/images/avatars/avatar-1.png" />
            </VBadge>
            
            <div v-if="!hideTitleAndIcon" class="ms-3 d-flex flex-column overflow-hidden" style="white-space: nowrap;">
              <span class="text-subtitle-2 text-high-emphasis text-truncate">John Doe</span>
              <span class="text-caption text-truncate" style="font-size: 0.75rem;">Admin</span>
            </div>
            <VSpacer v-if="!hideTitleAndIcon" />
            <IconBtn v-if="!hideTitleAndIcon" variant="tonal" color="secondary" size="small" class="rounded bg-surface">
              <VIcon icon="tabler-logout" size="18" />
            </IconBtn>
          </div>
          
          <div v-if="!hideTitleAndIcon" class="mt-4" style="white-space: nowrap;">
            <div class="text-caption text-disabled mb-1" style="font-size: 0.7rem;">Email</div>
            <div class="text-body-2 text-high-emphasis text-truncate">johndoe@gmail.com</div>
          </div>
        </VCard>

        <!-- Theme Switcher -->
        <VCard elevation="0" color="background" class="w-100 overflow-hidden" :class="hideTitleAndIcon ? 'p-0 rounded-lg' : 'p-1 rounded-pill mb-2'">
          <VBtnToggle
            v-model="themeConfigStore.theme"
            mandatory
            class="w-100 border-0 d-flex bg-transparent"
            :class="hideTitleAndIcon ? 'flex-column rounded-lg' : 'rounded-pill'"
          >
            <VBtn value="light" class="text-none bg-transparent" active-color="primary" :class="hideTitleAndIcon ? 'px-0 py-2 rounded-lg flex-grow-0' : 'flex-grow-1 px-2 rounded-pill'" :height="hideTitleAndIcon ? 'auto' : '36'">
              <VIcon :class="!hideTitleAndIcon ? 'me-1' : ''" icon="tabler-sun" :size="hideTitleAndIcon ? '20' : '16'" />
              <span v-if="!hideTitleAndIcon">Light</span>
            </VBtn>
            <VBtn value="dark" class="text-none" style="background: transparent;" active-color="primary" :class="hideTitleAndIcon ? 'px-0 py-2 rounded-lg flex-grow-0 mt-1' : 'flex-grow-1 px-2 rounded-pill'" :height="hideTitleAndIcon ? 'auto' : '36'">
              <VIcon :class="!hideTitleAndIcon ? 'me-1' : ''" icon="tabler-moon" :size="hideTitleAndIcon ? '20' : '16'" />
              <span v-if="!hideTitleAndIcon">Dark</span>
            </VBtn>
          </VBtnToggle>
        </VCard>
      </div>
    </slot>
  </Component>
</template>

<style lang="scss" scoped>
.app-logo {
  display: flex;
  align-items: center;
  column-gap: 0.75rem;

  .app-logo-title {
    font-size: 1.375rem;
    font-weight: 700;
    letter-spacing: 0.25px;
    line-height: 1.5rem;
    text-transform: capitalize;
  }
}
</style>

<style lang="scss">
@use "@configured-variables" as variables;
@use "@layouts/styles/mixins";

// 👉 Vertical Nav
.layout-vertical-nav {
  position: fixed;
  z-index: variables.$layout-vertical-nav-z-index;
  display: flex;
  flex-direction: column;
  block-size: 100%;
  inline-size: variables.$layout-vertical-nav-width;
  inset-block-start: 0;
  inset-inline-start: 0;
  transition: inline-size 0.25s ease-in-out, box-shadow 0.25s ease-in-out;
  will-change: transform, inline-size;

  .nav-header {
    display: flex;
    align-items: center;

    .header-action {
      cursor: pointer;

      @at-root {
        #{variables.$selector-vertical-nav-mini} .nav-header .header-action {
          &.nav-pin,
          &.nav-unpin {
            display: none !important;
          }
        }
      }
    }
  }

  .app-title-wrapper {
    margin-inline-end: auto;
  }

  .nav-items {
    block-size: 100%;

    // ℹ️ We no loner needs this overflow styles as perfect scrollbar applies it
    // overflow-x: hidden;

    // // ℹ️ We used `overflow-y` instead of `overflow` to mitigate overflow x. Revert back if any issue found.
    // overflow-y: auto;
  }

  .nav-item-title {
    overflow: hidden;
    margin-inline-end: auto;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  // 👉 Collapsed
  .layout-vertical-nav-collapsed & {
    &:not(.hovered) {
      inline-size: variables.$layout-vertical-nav-collapsed-width;
    }
  }
}

// Small screen vertical nav transition
@media (max-width: 1279px) {
  .layout-vertical-nav {
    &:not(.visible) {
      transform: translateX(-#{variables.$layout-vertical-nav-width});

      @include mixins.rtl {
        transform: translateX(variables.$layout-vertical-nav-width);
      }
    }

    transition: transform 0.25s ease-in-out;
  }
}
</style>
