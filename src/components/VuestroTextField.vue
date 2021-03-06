<template>
  <div class="vuestro-text-field"
       :class="[ `vuestro-text-field-${variant}`,
                 size,
                 { dark,
                   focused,
                   center,
                   noMargin,
                   readonly,
                   invalid,
                   stretch,
                  }
               ]">
    <!--the main input, input-el-wrapper provides border for outline variant-->
    <div class="vuestro-text-field-input-el-wrapper" :style="style">
      <input ref="inputEl"
             class="vuestro-text-field-input-el"
             :value="value"
             :type="showPassword ? 'text':type"
             :autocomplete="autocomplete"
             @focus="onFocus"
             @focusout="onFocusOut"
             @input="updateValue"
             @keyup="onKeyUp">
      </input>
      <!--unit-->
      <div v-if="$slots.unit" class="vuestro-text-field-unit-slot">
        <slot name="unit"></slot>
      </div>
      <!--clear button-->
      <vuestro-button v-if="!invalid && clearable && value" size="sm" round no-border @click.stop="onClear">
        <vuestro-icon name="times"></vuestro-icon>
      </vuestro-button>
      <!--validation-->
      <div v-if="invalid" class="vuestro-text-field-invalid-msg">{{ invalid }}</div>
      <!--editing buttons-->
      <div v-else-if="editingButtons" class="vuestro-text-field-editing-buttons">
        <vuestro-button v-if="!invalid" round no-border size="sm" variant="success" @click="onSaveButton">
          <vuestro-icon name="save"></vuestro-icon>
        </vuestro-button>
        <vuestro-button round no-border size="sm" variant="danger" @click="onCancelButton">
          <vuestro-icon name="trash"></vuestro-icon>
        </vuestro-button>
      </div>
      <!--show password button-->
      <span class="vuestro-text-field-show-password"
            v-if="type === 'password' || type === 'hidden'"
            @click="showPassword = !showPassword">
        <vuestro-icon v-if="!showPassword" name="eye-slash"></vuestro-icon>
        <vuestro-icon v-if="showPassword" name="eye"></vuestro-icon>
      </span>
    </div>
    <!--presets dropdown menu-->
    <div v-if="presets.length > 0" class="vuestro-text-field-preset-dropdown-wrapper">
      <vuestro-dropdown right click-to-open close-on-content-click>
        <template #button>
          <vuestro-button no-border round size="sm">
            <vuestro-icon name="chevron-down"></vuestro-icon>
          </vuestro-button>
        </template>
        <vuestro-list-button v-for="p in presets" :key="p" @click="onPreset(p)">{{ p }}</vuestro-list-button>
      </vuestro-dropdown>
    </div>
    <!--templatized dropdown menu-->
    <div v-if="$slots.dropdown" class="vuestro-text-field-preset-dropdown-wrapper">
      <vuestro-dropdown right click-to-open close-on-content-click>
        <template #button>
          <vuestro-button no-border round size="sm">
            <vuestro-icon name="chevron-down"></vuestro-icon>
          </vuestro-button>
        </template>
        <slot name="dropdown"></slot>
      </vuestro-dropdown>
    </div>
    <!--placeholder-->
    <div v-if="placeholder" ref="placeholder" class="vuestro-text-field-placeholder"
         :class="{ active: raisedPlaceholder }">
      {{ placeholder }}
    </div>
    <!--hint-->
    <div v-if="hint && raisedPlaceholder && value.length === 0" class="vuestro-text-field-hint">
      {{ hint }}
    </div>
  </div>
</template>

<script>

/* global _ */
import draggable from 'vuedraggable';

export default {
  name: 'VuestroTextField',
  components: {
    draggable,
  },
  props: {
    value: { type: null, required: true },
    placeholder: { type: String, default: null },
    variant: { type: String, default: 'regular' }, // { 'regular', 'outline', 'shaded' }
    radius: { type: String, default: 'var(--vuestro-control-border-radius)' },
    type: { type: String, default: 'text' },
    dark: { type: Boolean, default: false },
    hint: { type: String, default: null },
    center: { type: Boolean, default: false },
    noMargin: { type: Boolean, default: false },
    presets: { type: Array, default: () => [] },
    clearable: { type: Boolean, default: false },
    size: { type: String, default: 'md' },
    editingButtons: { type: Boolean, default: false },
    selected: { type: Boolean, default: false },      // true for all text selected by default
    readonly: { type: Boolean, default: false },      // true for readonly
    validate: { type: Function, default: () => { return false; } }, // return true or string to set invalid state
    autocomplete: { type: String, default: null },
    stretch: { type: Boolean, default: false },
  },
  data() {
    return {
      focused: false,
      raisedPlaceholder: false,
      showPassword: false,
      editingButtonsBuffer: this.value,
      style: {},
      beginValidation: false,
    };
  },
  computed: {
    invalid() {
      if (this.beginValidation) {
        let validation = this.validate(this.value);
        if (_.isString(validation)) {
          return validation;
        }
      }
      return false;
    },
  },
  watch: {
    value(newVal) {
      if (newVal || this.focused) {
        this.raisedPlaceholder = true;
      } else {
        this.raisedPlaceholder = false;
      }
      this.updateStyle();
    }
  },
  mounted() {
    // move placeholder for initial value
    // check for zero explicitly, since it's falsy
    if (this.value || this.value === 0) {
      this.raisedPlaceholder = true;
    }
    // special sauce to see if browser autofilled in this text field, if so,
    // move the placeholder out of the way
    setTimeout(this.checkPlaceholder, 300);
    setTimeout(this.checkPlaceholder, 600);
    setTimeout(this.checkPlaceholder, 900);
    setTimeout(this.checkPlaceholder, 1200);
    if (this.selected) {
      this.$nextTick(() => {
        this.$refs.inputEl.focus();
        this.$refs.inputEl.setSelectionRange(0, 999);
      });
    }
    this.$nextTick(() => {
      this.updateStyle();
    });
  },
  methods: {
    updateStyle() {
      if (this.variant === 'regular') {
        this.style = {};
      }
      this.$set(this.style, 'border-radius', this.radius);
      setTimeout(() => {
        if (this.placeholder && this.raisedPlaceholder) {
          let placeholderWidth = window.getComputedStyle(this.$refs.placeholder, null).getPropertyValue('width');
          this.$set(this.style, 'clip-path', `polygon(0 -10%, 0px 110%, 110% 110%, 110% -10%, calc(${placeholderWidth} - var(--vuestro-control-border-radius)*2) -10%, calc(${placeholderWidth} - var(--vuestro-control-border-radius)*2) 4px, calc(var(--vuestro-control-border-radius)*2) 4px, calc(var(--vuestro-control-border-radius)*2) -10%)`);
        } else {
          this.$delete(this.style, 'clip-path');
        }
      }, 100);
    },
    checkPlaceholder() {
      if (this.$refs.inputEl && window.getComputedStyle(this.$refs.inputEl).content === `"${String.fromCharCode(0xFEFF)}"`) {
        this.raisedPlaceholder = true;
      }
      this.updateStyle();
    },
    focus() { // proxy the focus() call
      if (!this.readonly) {
        this.$nextTick(() => {
          this.$refs.inputEl.focus();
        });
      }
    },
    updateValue() {
      // fine-grained, called with every keystroke so parent
      // can update value prop according to v-model convention
      if ( this.$refs.inputEl) {
        this.$emit('input', this.$refs.inputEl.value);
        this.editingButtonsBuffer = this.$refs.inputEl.value;
      }
    },
    onKeyUp(e) { // passthrough for 'keyup.enter'-type binding
      this.beginValidation = true; // begin validation
      this.$emit('keyup', e);
    },
    onFocus() {
      this.focused = true;
      this.raisedPlaceholder = true;
      this.$forceUpdate();
      this.updateStyle();
    },
    onFocusOut() {
      this.updateStyle();
      this.focused = false;
      // only lower placeholder if no text in value
      if (!this.value || this.value.length == 0) {
        this.raisedPlaceholder = false;
      }
    },
    onSaveButton() {
      let retVal = null;
      if (_.isBoolean(this.value)) {
        retVal = this.editingButtonsBuffer === 'true';
      } else if (_.isNumber(this.value)) {
        retVal = parseFloat(this.editingButtonsBuffer);
      } else if (_.isDate(this.value)) {
        retVal = new Date(this.editingButtonsBuffer);
      } else {
        retVal = this.editingButtonsBuffer;
      }
      this.$emit('save', retVal);
    },
    onCancelButton() {
      this.$emit('cancel');
    },
    onClear() {
      this.$emit('input', '');
      this.focus();
    },
    onPreset(p) {
      this.$emit('input', p);
      this.$emit('preset', p);
    },
  },
};

</script>

<style scoped>

.vuestro-text-field.sm {
  font-size: calc(var(--vuestro-control-sm-height) * 0.5);
  --vuestro-text-field-local-height: var(--vuestro-control-sm-height);
}
.vuestro-text-field-shaded.sm {
  --vuestro-text-field-local-height: calc(var(--vuestro-control-sm-height)*1.2);
}
.vuestro-text-field.md {
  font-size: calc(var(--vuestro-control-md-height) * 0.5);
  --vuestro-text-field-local-height: var(--vuestro-control-md-height);
}
.vuestro-text-field-shaded.md {
  --vuestro-text-field-local-height: calc(var(--vuestro-control-md-height)*1.2);
}
.vuestro-text-field.lg {
  font-size: calc(var(--vuestro-control-lg-height) * 0.5);
  --vuestro-text-field-local-height: var(--vuestro-control-lg-height);
}
.vuestro-text-field-shaded.lg {
  --vuestro-text-field-local-height: calc(var(--vuestro-control-lg-height)*1.2);
}
.vuestro-text-field.xl {
  font-size: calc(var(--vuestro-control-xl-height) * 0.5);
  --vuestro-text-field-local-height: var(--vuestro-control-xl-height);
}
.vuestro-text-field-shaded.xl {
  --vuestro-text-field-local-height: calc(var(--vuestro-control-xl-height)*1.2);
}
.vuestro-text-field {
  position: relative;
  margin: var(--vuestro-control-margin-v) var(--vuestro-control-margin-h);
  display: flex;
  height: var(--vuestro-text-field-local-height);
}
.vuestro-text-field.noMargin {
  margin: 0;
}
.vuestro-text-field.stretch {
  flex-grow: 1;
}
.vuestro-text-field.dark .vuestro-text-field-input-el {
  color: var(--vuestro-text-color-inverse);
}
.vuestro-text-field.focused,
.vuestro-text-field.focused .vuestro-text-field-input-el-wrapper {
  border-color: var(--vuestro-primary);
}
.vuestro-text-field.readonly {
  pointer-events: none;
}
.vuestro-text-field.invalid {
  border-color: var(--vuestro-danger);
}

.vuestro-text-field-outline .vuestro-text-field-input-el-wrapper {
  border: var(--vuestro-control-border-width) solid var(--vuestro-outline);
}
.vuestro-text-field-regular {
  border-bottom: var(--vuestro-control-border-width) solid var(--vuestro-outline);
}

.vuestro-text-field-shaded {
  border: none;
  background-color: var(--vuestro-light-med);
}
.vuestro-dark .vuestro-text-field-shaded,
.vuestro-text-field-shaded.dark {
  background-color: var(--vuestro-darker);
}

.vuestro-text-field-placeholder {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  font-size: 1em;
  font-weight: 300;
  top: 50%;
  left: 0px;
  max-width: 100%;
  transform: translate(0, -50%);
  transition: all 0.15s;
  position: absolute;
  color: var(--vuestro-secondary);
  pointer-events: none;
}
.vuestro-text-field.center .vuestro-text-field-placeholder {
  left: 50%;
  transform: translate(-50%, -50%);
}
.vuestro-text-field-placeholder.active {
  top: -1px;
  font-size: 0.8em;
  padding-left: 3px;
  padding-right: 3px;
}
.vuestro-text-field-outline .vuestro-text-field-placeholder {
  padding: 0 calc(var(--vuestro-control-border-radius)*2);
}
.vuestro-text-field-outline .vuestro-text-field-placeholder.active {
  padding: 0 calc(var(--vuestro-control-border-radius)*3);
}

.vuestro-text-field-shaded .vuestro-text-field-placeholder {
  padding: 0 8px;
}
.vuestro-text-field-shaded .vuestro-text-field-placeholder.active {
  top: 5px;
}

.vuestro-text-field-input-el-wrapper {
  flex: 1 1 auto;
  display: flex;
  padding-left: 0.4em;
  padding-right: 0.4em;
  transition: all 0.15s;
}

.vuestro-text-field-input-el {
  font-size: inherit;
  font-weight: 300;
  align-self: center;
  width: 100%;
  background-color: transparent;
  border: none;
  outline: none;
  color: var(--vuestro-text-color);
  padding: 0;
}
.vuestro-text-field-shaded .vuestro-text-field-input-el {
  align-self: flex-end;
  padding-left: 0.4em;
  padding-bottom: 0.4em;
}
.vuestro-text-field.center .vuestro-text-field-input-el {
  text-align: center;
}

.vuestro-text-field-show-password {
  margin-left: 4px;
  color: var(--vuestro-outline);
  cursor: pointer;
  display: flex;
  align-items: center;
}

.vuestro-text-field-hint {
  position: absolute;
  top: 50%;
  left: 1em;
  transform: translate(0, -50%);
  pointer-events: none;
  filter: invert(50%);
}
.vuestro-text-field.center .vuestro-text-field-hint {
  left: 50%;
  transform: translate(-50%, -50%);
}
.vuestro-text-field-input-el-wrapper > input:-webkit-autofill,
.vuestro-text-field-input-el-wrapper > input:-webkit-autofill:hover,
.vuestro-text-field-input-el-wrapper > input:-webkit-autofill:focus {
  content: "\feff"; /* magic value to detect browser autofill */
  border: var(--vuestro-control-border-width) solid var(--vuestro-gray);
  -webkit-text-fill-color: var(--vuestro-black);
  -webkit-box-shadow: 0 0 0px 1000px var(--vuestro-gray) inset;
}

.vuestro-text-field-editing-buttons {
  display: flex;
}

.vuestro-text-field-invalid-msg {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  background-color: var(--vuestro-danger);
  display: flex;
  align-items: center;
  white-space: nowrap;
  padding-left: 2px;
  padding-right: 5px;
  border-top-right-radius: var(--vuestro-control-border-radius);
  border-bottom-right-radius: var(--vuestro-control-border-radius);
}
/* render the left-pointing angle on the invalid msg */
.vuestro-text-field-invalid-msg:before {
  content: '';
  position: absolute;
	right: calc(100%);
  width: 0;
  height: 0;
  --vuestro-text-field-invalid-msg-px: calc(var(--vuestro-text-field-local-height) / 2);
  border-top: var(--vuestro-text-field-invalid-msg-px) solid transparent;
	border-right: calc(var(--vuestro-text-field-invalid-msg-px)*0.7) solid var(--vuestro-danger);
	border-bottom: var(--vuestro-text-field-invalid-msg-px) solid transparent;
}

.vuestro-text-field-preset-dropdown-wrapper {
  display: flex;
  align-items: center;
}

.vuestro-text-field-unit-slot {
  margin-right: 0.25em;
  font-size: 1.2em;
  color: var(--vuestro-secondary);
  display: flex;
  align-items: center;
}
</style>