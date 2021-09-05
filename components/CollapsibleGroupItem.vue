<template>
  <div class="group-item" :data-is-open="toggler">
    <div class="group-item-title" @click="toggle">
      <span v-if="title">{{ title }}</span>
      <span v-else>{{ defaultTitle || 'Item' }} #{{ index }}</span>
    </div>
    <div v-show="toggler" class="group-item-body">
      <slot />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    index: {
      type: Number,
      required: true,
    },
    title: {
      type: String,
      default: '',
      required: false,
    },
    defaultTitle: {
      type: String,
      default: '',
      required: false,
    },
  },
  data() {
    return {
      toggler: false,
    }
  },
  methods: {
    toggle() {
      this.toggler = !this.toggler
    },
  },
}
</script>

<style scoped>
.group-item-title {
  cursor: pointer;
}

.group-item-title::after {
  content: '';
  display: inline-block;
  border: 5px solid transparent;
  border-left-color: black;
  margin-left: 0.5em;
  position: relative;
  top: 0;
}

[data-is-open] .group-item-title {
  margin-bottom: 12px;
}

[data-is-open] .group-item-title::after {
  transform: rotate(90deg);
  top: 2px;
}
</style>
