<template>
  <button :class="{
    't-button': true,
    't-button--default': type === 'default',
    't-button--primary': type === 'primary',
    't-button--success': type === 'success',
    't-button--info': type === 'info',
    't-button--warning': type === 'warning',
    't-button--danger': type === 'danger',
  }">
    <slot />
  </button>
</template>

<script>
export default {
  name: 'TButton',
  props: {
    type: {
      type: String,
      default: 'default'
    }
  }
}
</script>

<style>
.t-button {
  position: relative;
  padding: 1px 7px;
  line-height: 22px;
  font-size: 14px;
  display: inline-block;
  border: #eee;
  color: rgba(0, 0, 0, 0.85);
}
.t-button--primary {
  background-color: #1890ff;
  border-color: #1890ff;
  color: #fff;
}
.t-button--danger {
  background-color: #df5e4d;
  border-color: #df5e4d;
  color: #fff;
}
</style>