<template>
  <div id="app">
    <TheHeader @reset="reset" />
    <main class="main">
      <PreviewStage v-if="!state.show" @create-dots="createDots" />
      <DrawingStage
        v-else
        @reset="reset"
        :verticalDots="state.vertical"
        :horizonDots="state.horizon"
        :styleList="state.styleList"
        :bodyWidth="state.bodyWidth"
      />
    </main>
    <TheFooter />
  </div>
</template>

<script lang="ts">
/**
 * PreviewStageでの設定をDrawingStageに伝えたりなどします。
 */
import TheHeader from "@/components/TheHeader.vue";
import TheFooter from "@/components/TheFooter.vue";
import PreviewStage from "@/components/PreviewStage.vue";
import DrawingStage from "@/components/DrawingStage.vue";
import { defineComponent, reactive } from "@vue/composition-api";

type State = {
  show: boolean;
  styleList: string[];
  vertical: number;
  horizon: number;
  bodyWidth: number;
};

export default defineComponent({
  components: {
    TheHeader,
    TheFooter,
    PreviewStage,
    DrawingStage
  },
  setup() {
    const state: State = reactive({
      show: false,
      styleList: [],
      vertical: 5,
      horizon: 5,
      bodyWidth: 500
    });

    /**
     * ドットの縦横を元に初期のグリッドを作成します。
     */
    const createDots = (horizon: number, vertical: number): void => {
      state.vertical = vertical;
      state.horizon = horizon;
      state.show = true;
      for (let i = 0; i < horizon * vertical; i++) {
        state.styleList.push(
          `backgroundColor: #fff; height: ${state.bodyWidth / horizon}px;`
        );
      }
    };

    /**
     * 全ての値をリセットして初期状態に戻します。
     */
    const reset = () => {
      state.show = false;
      state.styleList = [];
      state.vertical = 5;
      state.horizon = 5;
    };

    return {
      state,
      createDots,
      reset
    };
  }
});
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.button {
  border: none;
  color: #fff;
  background-color: #2c3e50;
  padding: 10px;
  border-radius: 5px;
}

.button__reset {
  background-color: #999;
}

.main {
  min-height: calc(100vh - 260px);
  padding-top: 60px;
  padding-bottom: 60px;
}
</style>
