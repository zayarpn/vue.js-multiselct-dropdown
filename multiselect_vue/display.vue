<template>
  <div class="display">
    <label>Multiselect Dropdown</label>
    <multi-select
      :options="dropdown_items"
      :limit="5"
      defaultText="select options here"
      v-model="user.selected_items"
    ></multi-select>
    <textarea rows="5" readonly v-model="showSelectedItems"></textarea>
  </div>
</template>

<script>
import MultiselectDropDown from "../../MultiselectDropDown";
export default {
  components: {
    "multi-select": MultiselectDropDown,
  },
  data() {
    return {
      user: {
        selected_items: "",
      },
      dropdown_items: [
        { id: 1, name: "Saleman" },
        { id: 2, name: "System Analyst" },
        { id: 3, name: "Software Developer" },
        { id: 4, name: "Project Manager" },
        { id: 5, name: "Technical Expert" },
        { id: 6, name: "Network Engineer" },
      ],
    };
  },

  computed: {
    showSelectedItems: function () {
      // show selected data in textarea
      return this.user.selected_items
        ? this.dropdown_items
            .filter((item) => this.user.selected_items.find((i) => i == item.id))
            .map((ele) => ele.name)
            .join("\n")
        : "";
    },
  },
};
</script>

<style scoped>
</style>