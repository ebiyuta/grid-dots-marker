<template>
  <div class="drawing">
    <div class="canvasWrapper">
      <div>
        <h2>CANVAS</h2>
        <div
          class="canvas"
          :style="
            `gridTemplateColumns: repeat(${horizonDots}, ${state.bodyWidth /
              horizonDots}px); width: ${state.bodyWidth}px;`
          "
        >
          <button
            class="canvas_dots"
            v-for="(n, index) in styleList"
            :key="index"
            @click.left="setColor(state.currentColor, index)"
            @click.right.prevent="setColor(state.currentSubColor, index)"
            :style="n"
          ></button>
        </div>
      </div>
      <div class="property">
        <h2>COLOR</h2>
        <p>現在の色<br><small>（右クリックでサブカラー）</small></p>
        <div class="property_currentColor">
          <input
            class="property_currentColorButton"
            type="color"
            v-model="state.currentColor"
          />
          <input
            class="property_currentSubColorButton"
            type="color"
            v-model="state.currentSubColor"
          />
        </div>
        <div>
          <p>今まで使った色</p>
          <div class="property_historyColor">
            <button
              class="property_changeColorButton"
              v-for="(color, index) in state.historyColor"
              :key="index"
              @click.left="changeColor(color)"
              @click.right.prevent="changeSubColor(color)"
              :style="`backgroundColor: ${color}`"
            >
              クリックするとこの色に変更
            </button>
          </div>
        </div>
      </div>
    </div>
    <div class="code">
      <h2>CODE</h2>
      <textarea class="code_textarea" v-model="createCode" readonly></textarea>
    </div>
    <div class="copy">
      <button class="button" @click="copy()">
        コードをコピーする
      </button>
    </div>
    <button class="button button__reset" @click="reset">
      最初から始める
    </button>
  </div>
</template>

<script lang="ts">
/**
 * 実際にドット絵をお絵かきする画面用のコンポーネントです。
 * ドット数が決定したら表示されます。
 * お絵かきから、コードの生成までを行います。
 */
import { computed, defineComponent, reactive, set } from "@vue/composition-api";

type Props = {
  verticalDots: number;
  horizonDots: number;
  styleList: string[];
  bodyWidth: number;
};

type State = {
  currentColor: string;
  currentSubColor: string;
  historyColor: string[];
  styleList: string[];
  bodyWidth: number;
};

export default defineComponent({
  props: {
    verticalDots: {
      type: Number
    },
    horizonDots: {
      type: Number
    },
    styleList: {
      type: Array
    },
    bodyWidth: {
      type: Number
    }
  },
  setup(props: Props, { emit }) {
    const state: State = reactive({
      currentColor: "#6B8CFF",
      currentSubColor: "#ffffff",
      historyColor: ["#ffffff", "#000000"],
      styleList: props.styleList,
      bodyWidth: props.bodyWidth
    });

    /**
     * コードをいい感じに整形します。
     * @type {ComputedRef<unknown>}
     */
    const createCode = computed((): string => {
      const regex = /backgroundColor/gi;
      const html = `<style>\n.wrapper {\n\tdisplay: grid;\n\tgrid-template-columns: repeat(${
        props.horizonDots
      }, ${state.bodyWidth / props.horizonDots}px);\n\twidth: ${
        state.bodyWidth
      }px;\n} \n</style>\n<div class=wrapper>\n\t<div style="${state.styleList
        .join('"></div>\n\t<div style="')
        .replace(regex, "background-color")}"></div>\n</div>`;
      return html;
    });
    /**
     * 履歴の色をクリックした時に現在の色を変更します
     * @param color
     */
    const changeColor = (color: string): void => {
      state.currentColor = color;
    };
    /**
     * 履歴の色をクリックした時に現在の色を変更します
     * @param color
     */
    const changeSubColor = (color: string): void => {
      state.currentSubColor = color;
    };
    /**
     * ドットをクリックした時に色を変更する処理です。同時にヒストリーに色を追加します。
     * @param currentColor
     * @param n
     */
    const setColor = (currentColor: string, n: number): void => {
      set(
        state.styleList,
        n,
        `backgroundColor: ${currentColor} ; height: ${state.bodyWidth /
          props.horizonDots}px;`
      );
      // 現在の色が既に履歴にあれば追加しない
      if (!state.historyColor.includes(currentColor)) {
        state.historyColor.push(currentColor);
      }
      // 履歴が10件以上であれば1個目を削除
      if (state.historyColor.length > 10) {
        state.historyColor.shift();
      }
    };

    /**
     * コードをクリップボードにコピーします。
     */
    const copy = (): void => {
      const text = document.getElementsByTagName("textarea")[0];

      text.select();
      document.execCommand("copy");

      window.alert("コピーが完了しました！");
    };

    /**
     * 全ての値をリセットして初期状態に戻します。
     */
    const reset = (): void => {
      emit("reset");
    };

    return {
      state,
      createCode,
      changeColor,
      changeSubColor,
      setColor,
      copy,
      reset
    };
  }
});
</script>

<style lang="scss" scoped>
.canvasWrapper {
  user-select: none;
  display: grid;
  width: 700px;
  grid-template-columns: 500px 200px;
  gap: 10px;
  margin-right: auto;
  margin-left: auto;
  margin-bottom: 60px;
}

.canvas {
  display: grid;
  border-top: 1px solid #000;
  border-left: 1px solid #000;
  .canvas_dots {
    border: 1px solid #000;
    border-top: none;
    border-left: none;
    outline: none;
  }
}

.property {
  .property_currentColor {
    position: relative;
    margin-bottom: 60px;
  }
  .property_historyColor {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    justify-items: center;
  }
  .property_currentColorButton {
    position: relative;
    z-index: 1;
    width: 80px;
    height: 80px;
  }
  .property_currentSubColorButton {
    position: absolute;
    right: 0;
    top: 30px;
    width: 80px;
    height: 80px;
  }
  .property_changeColorButton {
    width: 80px;
    height: 80px;
    border: 1px solid #000;
    text-indent: 100%;
    white-space: nowrap;
    padding: 0;
    overflow: hidden;
  }
}

.code {
  width: 700px;
  margin-right: auto;
  margin-left: auto;
  margin-bottom: 20px;
  .code_textarea {
    width: 100%;
    height: 300px;
    resize: none;
    padding: 10px;
  }
}

.copy {
  margin-bottom: 60px;
}
</style>
