<template>
  <div class="textField">
    <input
      class="textField__input"
      type="number"
      v-model="state.newValue"
      @input="changeDots"
      required
    />
    <label class="textField__label">{{ label }}</label>
  </div>
</template>

<script lang="ts">
/**
 * テキストフィールド用のコンポーネントです。
 * 数値の初期値とラベルを受け取って表示。changeDotsで変更を親に伝えます。
 */
import { defineComponent, reactive } from "@vue/composition-api";

type Props = {
  label: string;
  initDots: number;
};

type State = {
  newValue: number;
};

export default defineComponent({
  props: {
    label: {
      type: String,
      default: "Label"
    },
    initDots: {
      type: Number,
      required: true
    }
  },
  setup(props: Props, { emit }) {
    const state: State = reactive({
      newValue: props.initDots
    });
    const changeDots = (): void => {
      emit("input", Number(state.newValue));
    };
    return {
      state,
      changeDots
    };
  }
});
</script>

<style lang="scss" scoped>
.textField {
  position: relative;
  box-sizing: border-box;
  display: inline-flex;
  height: 56px;
  overflow: hidden;
  background-color: #f5f5f5;
  border-radius: 4px 4px 0 0;

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
    &:focus {
      outline: none;
    }
  }
  .textField__input:focus ~ .textField__label {
    color: #2c3e50;
    transform: translateY(-106%) scale(0.75);
  }
  .textField__input:required:valid ~ .textField__label {
    transform: translateY(-106%) scale(0.75);
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
}
</style>
