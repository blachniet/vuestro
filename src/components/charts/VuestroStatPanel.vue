<template>
  <vuestro-panel class="vuestro-stat-panel" :style="style" frame>
    <div class="vuestro-stat-panel-title drag">{{ title }}</div>
    <vuestro-container content="flex-end"
                       :class="{ 'vuestro-stat-panel-clickable': options.clickRoute }"
                       @click="onClick">
      <vuestro-icon class="vuestro-stat-panel-icon" v-if="icon" :name="icon"></vuestro-icon>
      <div class="vuestro-stat-panel-value">
        {{ data[data.length-1].stat | vuestroCommas }}
      </div>
    </vuestro-container>
  </vuestro-panel>
</template>

<script>

/* globals _ */

export default {
  name: 'VuestroStatPanel',
  props: {
    data: { type: Array, required: true }, // needs to contain object(s) with "stat" field
    options: { type: Object, default: () => ({}) },
  },
  data() {
    return {
      title: 'Default Title',
      color: 'var(--vuestro-primary)',
      icon: null,
    };
  },
  computed: {
    style() {
      return {
        '--vuestro-panel-bg': this.color,
      };
    },
  },
  beforeMount() {
    _.merge(this, this.options);
  },
  methods: {
    onClick() {
      if (this.options.clickRoute) {
        this.$router.push({ name: this.options.clickRoute });
      }
    },
  },
};

</script>

<style>

.vuestro-app {
  --vuestro-stat-panel-font-size: 24px;
  --vuestro-stat-panel-font-weight: 300;
  --vuestro-stat-panel-value-font-size: 36px;
}
.vuestro-app.mobile {
  --vuestro-stat-panel-font-size: 32px;
}

</style>
<style scoped>

.vuestro-stat-panel {
  overflow: hidden;
}
.vuestro-stat-panel-title {
  color: var(--vuestro-white);
  font-size: var(--vuestro-stat-panel-font-size);
  padding-top: 5px;
  padding-left: 8px;
  font-weight: var(--vuestro-stat-panel-font-weight);
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
  text-shadow: 1px 1px 0px rgba(0,0,0,.1);
  cursor: default;
}
.vuestro-stat-panel-value {
  margin-left: auto;
  font-size: var(--vuestro-stat-panel-value-font-size);
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
  text-shadow: 1px 1px 0px rgba(0,0,0,.1);
}
.vuestro-stat-panel-clickable {
  cursor: pointer;
}

.vuestro-stat-panel-icon {
  margin-left: 0.4em;
}
.vuestro-stat-panel-icon >>> svg {
  width: 36px;
  height: 36px;
}

</style>