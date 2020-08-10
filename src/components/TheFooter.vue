<template>
  <footer class="footer">
    <p>
      Copyright © 2020
      <a href="https://twitter.com/pino_ebiebi" target="_blank">@pino_ebiebi</a>
      All Rights Reserved.
    </p>
  </footer>
</template>

<script>
/**
 * フッターを表示するコンポーネントです。それ以外何もしない。
 */
export default {
  name: "TheFooter"
};
</script>

<style lang="scss" scoped>
.footer {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 70px;
}

.footer a {
  text-decoration: none;
  color: #2c3e50;
}

.footer a:hover {
  text-decoration: underline;
}
</style>
