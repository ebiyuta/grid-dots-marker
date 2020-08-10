<template>
  <div class="preview">
    <p class="preview_lead">
      CSS Gridでドット絵をお手軽に描くためのツールです！<br />
      みんなCSSでドット絵を描きたいので<br />
      まずは描くドット絵のサイズを決めましょう
    </p>
    <div class="preview_inputs">
      <TextField label="縦のドット数" :initDots="5" @input="changeVertical" />
      <TextField label="横のドット数" :initDots="5" @input="changeHorizon" />
    </div>
    <div
      class="preview_body"
      :style="
        `gridTemplateColumns: repeat(${state.horizon}, ${300 /
          state.horizon}px);`
      "
    >
      <div
        v-for="(n, index) in total"
        :key="index"
        :style="`height: ${300 / state.horizon}px;`"
      ></div>
    </div>
    <button class="button" @click="createDots">
      このドット数で始める！
    </button>
  </div>
</template>

<script lang="ts">
/**
 * ドット数の設定、プレビューを表示するコンポーネントです。
 * ドット数が決定したら非表示になります。
 */
import TextField from "@/components/TextField.vue";
import { computed, defineComponent, reactive } from "@vue/composition-api";

type Props = {
  verticalDots: number;
  horizonDots: number;
};

type State = {
  vertical: number;
  horizon: number;
};

export default defineComponent({
  props: {
    verticalDots: {
      type: Number
    },
    horizonDots: {
      type: Number
    }
  },
  components: {
    TextField
  },
  setup(props: Props, { emit }) {
    const state: State = reactive({
      vertical: 5,
      horizon: 5
    });
    /**
     * ドットの縦掛ける横の合計数を計算します
     * @type {ComputedRef<unknown>}
     */
    const total = computed((): number => {
      if (state.vertical < 0 || state.horizon < 0) {
        return 0;
      }
      return state.vertical * state.horizon;
    });
    /**
     * ドットの縦横を元に初期のグリッドを作成します。
     */
    const createDots = (): void => {
      if (
        state.horizon &&
        state.vertical &&
        state.horizon > 0 &&
        state.vertical > 0
      ) {
        emit("create-dots", state.vertical, state.horizon);
      } else {
        window.alert("ドット数には1以上の数字を入力してください");
      }
    };

    const changeVertical = (value: number): void => {
      state.vertical = value;
    };

    const changeHorizon = (value: number): void => {
      state.horizon = value;
    };
    return { state, total, changeVertical, changeHorizon, createDots };
  }
});
</script>

<style lang="scss" scoped>
.preview {
  .preview_lead {
    margin-bottom: 30px;
  }
  .preview_inputs {
    width: 500px;
    display: flex;
    justify-content: space-around;
    margin-right: auto;
    margin-left: auto;
    margin-bottom: 30px;
  }
  .preview_body {
    display: grid;
    width: 300px;
    margin-right: auto;
    margin-left: auto;
    margin-bottom: 30px;
    border-left: 1px solid #000000;
    border-top: 1px solid #000000;
    > div {
      background-color: #eee;
      border-right: 1px solid #000000;
      border-bottom: 1px solid #000000;
    }
  }
}
</style>
