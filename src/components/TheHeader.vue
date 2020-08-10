<template>
  <header class="header">
    <img
      @click="reset"
      src="../assets/logo.svg"
      alt="CSS Grid ドット絵メーカー"
    />
  </header>
</template>

<script lang="ts">
/**
 * ヘッダーを表示するコンポーネントです。全てをresetする機能も備えています。
 */
import { defineComponent } from "@vue/composition-api";
export default defineComponent({
  setup(_, { emit }) {
    const reset = () => {
      emit("reset");
    };
    return { reset };
  }
});
</script>

<style scoped>
.header {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 70px;
  color: #fff;
  background-color: #2c3e50;
}
</style>
