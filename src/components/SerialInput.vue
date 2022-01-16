<template>
  <div class="input">
    <font-awesome-icon :icon="icon" />
    <label :for="label">{{ name }}: </label>
    <input
      :id="label"
      :value="value"
      ref="newVal"
      @input="update()"
      type="text"
      autocorrect="off"
      autocapitalize="off"
      spellcheck="false"
    />
  </div>
</template>

<script>
import { library } from "@fortawesome/fontawesome-svg-core";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import {
  faBolt,
  faFire,
  faKey,
  faStar,
} from "@fortawesome/free-solid-svg-icons";

library.add(faBolt, faFire, faKey, faStar);

export default {
  name: "SerialInput",
  components: {
    "font-awesome-icon": FontAwesomeIcon,
  },
  props: {
    label: String,
    name: String,
    value: String,
    icon: String,
  },
  methods: {
    update() {
      this.$emit("input", this.$refs.newVal.value);
    },
  },
};
</script>

<style scoped lang="scss">
.input {
  display: grid;
  grid-template-columns: 1em 1fr 4fr;
  gap: 0.25em;
  align-items: center;
  justify-items: space-between;

  width: 100%;
  border: 1px solid;
  border-radius: 10em;
  box-sizing: border-box;

  padding: 0 0.5em;

  &:hover {
    color: orange;
  }
}

input {
  border: none;
  background: transparent;
  font: inherit;
  color: inherit;
  padding: 0;
  line-height: 1;
}

label:hover {
  cursor: pointer;
}
</style>
