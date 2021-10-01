<template id="MultiSelect">
  <div v-clickoutside="hideDropDown">
    <span hidden>{{ total_cnt }}</span>
    <div class="dropdown" @click="showDropdown">
      <div class="overselect"></div>
      <select class="c-form-input form-control">
        <option>{{ selected_items_list || defaultText }}</option>
      </select>
    </div>
    <div class="multiselect" v-if="show">
      <ul>
        <li
          v-for="(option, index) in options"
          :key="index"
          :class="{ active: isActive(option.id) }"
        >
          <input
            type="checkbox"
            :id="index"
            :value="option.id"
            v-model="selected"
            :disabled="limit ? limit_reached(option.id) : false"
          />
          <label :for="index">{{ option.name }}</label>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "Multiselect",
  directives: {
    clickoutside: {
      inserted: (el, binding, vnode) => {
        // assign event to the element
        el.clickOutsideEvent = function (event) {
          // here we check if the click event is outside the element and it's children
          if (!(el == event.target || el.contains(event.target))) {
            // if clicked outside, call the provided method
            vnode.context[binding.expression](event);
          }
        };
        // register click and touch events
        document.body.addEventListener("click", el.clickOutsideEvent);
        document.body.addEventListener("touchstart", el.clickOutsideEvent);
      },
      unbind: function (el) {
        // unregister click and touch events before the element is unmounted
        document.body.removeEventListener("click", el.clickOutsideEvent);
        document.body.removeEventListener("touchstart", el.clickOutsideEvent);
      },
      stopProp(event) {
        event.stopPropagation();
      },
    },
  },
  props: {
    options: {
      type: Array,
      required: true,
      // options array must contain id & name in object pair
    },
    limit: {
      type: Number,
      required: false,
      default: null,
    },
    value: {
      type: Array,
      required: true,
      default: () => [],
    },
    defaultText: {
      type: String,
      default: "Multi-Select",
    },
  },
  data() {
    return {
      show: false,
      selected: [],
      selected_items_list: "",
    };
  },

  computed: {
    total_cnt: function () {
      let options = this.options;
      if (options.length > 0) this.showSelectedItems();
      return this.selected.length;
    },
  },

  watch: {
    selected(val) {
      this.$emit("input", val);
      this.showSelectedItems();
    },
  },

  beforeUpdate() {
    this.selected = this.value;
  },

  methods: {
    showDropdown() {
      this.show = !this.show;
    },
    showSelectedItems() {
      this.selected_items_list = this.options
        .filter((opt) => this.value.find((v) => v == opt.id))
        .map((ele) => ele.name)
        .join();
    },
    limit_reached(item) {
      // parameter must be input value
      return (
        this.selected.length == this.limit && this.selected.indexOf(item) === -1
      );
    },
    hideDropDown() {
      this.show = false; // hide dropdown if clicked outside of the component
    },
    isActive(id) {
      return this.selected.includes(id);
    },
  },
};
</script>

<style lang="scss" scoped>
.active {
  background: #bbd8bb;
}

.col {
  flex: 0 0 50%;
  max-width: 50%;
  padding-left: 1rem;
  padding-right: 1rem;
}

/*****
- MultiSelect 
*****/

.dropdown {
  position: relative;
  // cursor: pointer;
}

.multiselect {
  margin-top: 15px;
  position: relative;
  ul {
    // max-width: auto;
    // max-height: auto;
    background-color: white;
    border: 1px solid rgb(26, 26, 26);
    // border-top: 0;
    border-radius: 0 0 3px 3px;
    left: 0px;
    padding: 8px 8px;
    position: absolute;
    top: -1rem;
    width: 100%;
    list-style: none;
    max-height: 150px;
    overflow: auto;
  }
}

.overselect {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
}
</style>