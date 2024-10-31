<template>
  <div class="modal">
    <div class="modal-content">
      <slot name="header"></slot>
      <slot name="body"></slot>
      <slot name="footer"></slot>
    </div>
  </div>
</template>

<script>
export default {
  methods: {
    close() {
      this.$emit('close');
    }
  }
};
</script>

<style scoped>
/* 模态框样式 */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.7); /* 修改透明度 */
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999; /* 确保模态框在最上层 */
}

.modal-content {
  background: white;
  padding: 30px; /* 增加内边距 */
  border-radius: 10px; /* 圆角 */
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3); /* 添加阴影 */
  width: 400px; /* 设置模态框宽度 */
}
</style>

