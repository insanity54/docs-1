<template>
  <main class="page">
    <slot name="top" />

    <Content class="theme-default-content" />

    <div class="content-footer" v-if="!isContentStatus">
      <Feedback
        class="content-feedback"
        evtYes="information_helpful"
        evtNo="information_not_helpful"
      />
      <PageEdit />
      <PageNav v-bind="{ sidebarItems }" />
    </div>

    <BackToTop />
    <ScrollPatch />

    <slot name="bottom" />
  </main>
</template>

<script>
import PageEdit from '@theme/components/PageEdit.vue'
import PageNav from '@parent-theme/components/PageNav.vue'

import Feedback from './Feedback.vue'
import BackToTop from './BackToTop.vue'
import ScrollPatch from './ScrollPatch.vue'

export default {
  name: 'Page',
  components: {
    PageEdit,
    PageNav,
    Feedback,
    BackToTop,
    ScrollPatch
  },
  props: ['sidebarItems'],
  computed: {
    isContentStatus: function () {
      return !!(this.$frontmatter && this.$frontmatter.issueUrl)
    }
  },
  methods: {
    smoothScroll: function () {
      var root = document.getElementsByTagName('html')[0]
      // only enable smooth-scrolling on pages shorter that 15000 px
      return root.scrollHeight < 15000
        ? root.classList.add('smooth-scroll')
        : root.classList.remove('smooth-scroll')
    }
  },
  mounted: function () {
    this.smoothScroll()
  },
  updated: function () {
    this.smoothScroll()
  }
}
</script>

<style lang="stylus" scoped>
.page {
  background-color: $contentBgColor;

  top: 3.6rem;
  margin-top: calc(-1 * (100vh - 2.5rem));
  min-height: calc(100vh - 2.5rem);
}

.content-footer {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  
  padding-top: 0;
}

.page-edit {
	font-size: 1.2rem;
  max-width: 100%;
  padding: 2rem 2rem 0 2rem;
  margin: unset;
}

@media (min-width: $MQMobile) {
  .page {
    box-shadow: inset -16px 0 0 0 $layoutBgColor;
  }
  .content-footer {
    padding: 0 2.5rem;
    padding-top: 0;
  }

  .page-edit {
    padding: 2.5rem 0 0 0;
  }

  section {
    display: flex;

    .block {
      flex: 1;
    }
  }
}
</style>
