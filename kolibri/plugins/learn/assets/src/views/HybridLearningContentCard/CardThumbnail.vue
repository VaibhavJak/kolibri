<template>

  <ContentNodeThumbnail
    :contentNode="contentNode"
  >
    <template #labels>
      <LearningActivityDuration
        v-if="displayDurationChip"
        :contentNode="contentNode"
        appearance="chip"
        class="duration"
      />
    </template>
  </ContentNodeThumbnail>

</template>


<script>

  import commonCoreStrings from 'kolibri.coreVue.mixins.commonCoreStrings';
  import ContentNodeThumbnail from '../thumbnails/ContentNodeThumbnail.vue';
  import LearningActivityDuration from '../LearningActivityDuration';

  export default {
    name: 'CardThumbnail',
    components: {
      LearningActivityDuration,
      ContentNodeThumbnail,
    },
    mixins: [commonCoreStrings],
    props: {
      isMobile: {
        type: Boolean,
        default: false,
      },
      contentNode: {
        type: Object,
        required: true,
      },
      // Override to hide the tag used based on thumbnail proportions
      // should be TRUE when medium sized screen and list view of cards
      hideDuration: {
        type: Boolean,
        default: false,
      },
    },
    computed: {
      displayDurationChip() {
        if (this.hideDuration) {
          return false;
        } else if (this.isMobile) {
          return false;
        }
        return true;
      },
    },
  };

</script>


<style lang="scss" scoped>

  .duration {
    position: absolute;
    bottom: 16px;
    left: 10px;
    z-index: 2;
  }

</style>
