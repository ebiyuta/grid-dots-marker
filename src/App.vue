<template>
  <div id="app">
    <header class="header">
      <img
        @click="reset"
        src="./assets/logo.svg"
        alt="CSS Grid ドット絵メーカー"
      />
    </header>
    <main class="main">
      <div class="start" v-if="!state.show">
        <p class="lead">
          CSS Gridでドット絵をお手軽に描くためのツールです！<br />
          みんなCSSでドット絵を描きたいので<br />
          まずは描くドット絵のサイズを決めましょう
        </p>
        <div class="textFieldWrapper">
          <div class="textField">
            <input
              class="textField__input"
              type="number"
              v-model="state.vertical"
              required
            />
            <label class="textField__label">
              縦のドット数
            </label>
          </div>
          <div class="textField">
            <input
              class="textField__input"
              type="number"
              v-model="state.horizon"
              required
            />
            <label class="textField__label">
              横のドット数
            </label>
          </div>
        </div>
        <div
          class="preview"
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
        <button class="button" @click="createDots()">
          このドット数で始める！
        </button>
      </div>
      <template v-else>
        <div class="wrapper">
          <div>
            <h2>CANVAS</h2>
            <div
              class="body"
              :style="
                `gridTemplateColumns: repeat(${
                  state.horizon
                }, ${state.bodyWidth / state.horizon}px); width: ${
                  state.bodyWidth
                }px;`
              "
            >
              <button
                v-for="(n, index) in state.styleList"
                :key="index"
                @click="setColor(state.currentColor, index)"
                :style="n"
              ></button>
            </div>
          </div>
          <div class="property">
            <h2>COLOR</h2>
            <div class="property_currentColor">
              <p>現在の色</p>
              <div>
                <input
                  class="currentColorButton"
                  type="color"
                  v-model="state.currentColor"
                />
              </div>
            </div>
            <div class="property_historyColor">
              <p>今まで使った色</p>
              <div class="changeColorButtonWrapper">
                <button
                  class="changeColorButton"
                  v-for="(color, index) in state.historyColor"
                  :key="index"
                  @click="changeColor(color)"
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
          <textarea v-model="createCode" readonly></textarea>
        </div>
        <div class="copy">
          <button class="button" @click="copy()">
            コードをコピーする
          </button>
        </div>
        <button class="button button__reset" @click="reset">
          最初から始める
        </button>
      </template>
    </main>
    <footer class="footer">
      <p>
        Copyright © 2020
        <a href="https://twitter.com/pino_ebiebi" target="_blank"
          >@pino_ebiebi</a
        >
        All Rights Reserved.
      </p>
    </footer>
  </div>
</template>

<script>
import { defineComponent, computed, reactive, set } from "@vue/composition-api";

export default defineComponent({
  setup() {
    const state = reactive({
      show: false,
      currentColor: "#6B8CFF",
      historyColor: ["#ffffff", "#000000"],
      styleList: [],
      bodyWidth: 500,
      vertical: 5,
      horizon: 5
    });

    /**
     * ドットの縦掛ける横の合計数を計算します
     * @type {ComputedRef<unknown>}
     */
    const total = computed(() => {
      return state.vertical * state.horizon;
    });

    /**
     * ドットの縦横を元に初期のグリッドを作成します。
     */
    const createDots = () => {
      if (
        state.horizon &&
        state.vertical &&
        state.horizon != 0 &&
        state.vertical != 0
      ) {
        state.show = true;
        for (let i = 0; i < total.value; i++) {
          state.styleList.push(
            `backgroundColor: #fff; height: ${state.bodyWidth /
            state.horizon}px;`
          );
        }
      }
    };

    /**
     * ドットをクリックした時に色を変更する処理です。同時にヒストリーに色を追加します。
     * @param currentColor
     * @param n
     */
    const setColor = (currentColor, n) => {
      set(
        state.styleList,
        n,
        `backgroundColor: ${currentColor} ; height: ${state.bodyWidth /
          state.horizon}px;`
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
     * 全ての値をリセットして初期状態に戻します。
     */
    const reset = () => {
      state.show = false;
      state.styleList = [];
      state.vertical = 5;
      state.horizon = 5;
      state.historyColor = ["#ffffff", "#000000"];
    };

    /**
     * コードをクリップボードにコピーします。
     */
    const copy = () => {
      const text = document.getElementsByTagName("textarea")[0];

      text.select();
      document.execCommand("copy");
    };

    /**
     * 履歴の色をクリックした時に現在の色を変更します
     * @param color
     */
    const changeColor = color => {
      state.currentColor = color;
    };

    /**
     * コードをいい感じに整形します。
     * @type {ComputedRef<unknown>}
     */
    const createCode = computed(() => {
      const regex = /backgroundColor/gi;
      const html = `<style>\n.wrapper {\n\tdisplay: grid;\n\tgrid-template-columns: repeat(${
        state.horizon
      }, ${state.bodyWidth / state.horizon}px);\n\twidth: ${
        state.bodyWidth
      }px;\n} \n</style>\n<div class=wrapper>\n\t<div style="${state.styleList
        .join('"></div>\n\t<div style="')
        .replace(regex, "background-color")}"></div>\n</div>`;
      return html;
    });

    return {
      state,
      total,
      createCode,
      setColor,
      createDots,
      changeColor,
      reset,
      copy
    };
  }
});
</script>

<style>
/* CSSはめちゃのくちゃ */
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

.lead {
  margin-bottom: 30px;
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

.textFieldWrapper {
  width: 500px;
  display: flex;
  justify-content: space-around;
  margin-right: auto;
  margin-left: auto;
  margin-bottom: 30px;
}

.textField {
  position: relative;
  box-sizing: border-box;
  display: inline-flex;
  height: 56px;
  overflow: hidden;
  background-color: #f5f5f5;
  border-radius: 4px 4px 0 0;
}

.textField__input:focus {
  outline: none;
}

.textField__input:focus ~ .textField__label {
  color: #2c3e50;
  transform: translateY(-106%) scale(0.75);
}
.textField__input:required:valid ~ .textField__label {
  transform: translateY(-106%) scale(0.75);
}

.textField__input {
  box-sizing: border-box;
  align-self: flex-end;
  width: 100%;
  height: 100%;
  padding: 20px 16px 6px;
  font-size: 1rem;
  background: none;
  border: none;
  border-bottom: 1px solid;
  transition: opacity 150ms cubic-bezier(0.4, 0, 0.2, 1);
  appearance: none;
}

.textField__label {
  position: absolute;
  top: 50%;
  left: 16px;
  overflow: hidden;
  line-height: 1.15rem;
  color: rgba(0, 0, 0, 0.6);
  text-align: left;
  transition: transform 150ms cubic-bezier(0.4, 0, 0.2, 1),
    color 150ms cubic-bezier(0.4, 0, 0.2, 1);
  transform: translateY(-50%);
  transform-origin: left top;
}

.preview {
  display: grid;
  width: 300px;
  margin-right: auto;
  margin-left: auto;
  gap: 5px;
  margin-bottom: 30px;
}

.preview > div {
  background-color: #eee;
}

.wrapper {
  display: grid;
  width: 700px;
  grid-template-columns: 500px 200px;
  gap: 10px;
  margin-right: auto;
  margin-left: auto;
  margin-bottom: 60px;
}

.body {
  display: grid;
  border-top: 1px solid #000;
  border-left: 1px solid #000;
}

.property_currentColor {
  margin-bottom: 30px;
}

.body > button {
  border: 1px solid #000;
  border-top: none;
  border-left: none;
  outline: none;
}

.currentColorButton {
  width: 80px;
  height: 80px;
}

.changeColorButtonWrapper {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  justify-items: center;
}

.changeColorButton {
  width: 80px;
  height: 80px;
  border: 1px solid #000;
  text-indent: 100%;
  white-space: nowrap;
  padding: 0;
  overflow: hidden;
}

.code {
  width: 700px;
  margin-right: auto;
  margin-left: auto;
  margin-bottom: 20px;
}

.code > textarea {
  width: 100%;
  height: 300px;
  resize: none;
  padding: 10px;
}

.copy {
  margin-bottom: 60px;
}

.header {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 70px;
  color: #fff;
  background-color: #2c3e50;
}

.main {
  min-height: calc(100vh - 260px);
  padding-top: 60px;
  padding-bottom: 60px;
}

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

/* こんな汚いCSSを書く子に育てた覚えはありません！ */
</style>
