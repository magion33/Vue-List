<template>      
      
    <input :value="modelValue" @input="$emit('update:modelValue', $event.target.value)" 
    @keydown.esc="$emit('esc')" @keydown.enter="enter()"
    :id="$style.searchBar" type="text" placeholder="Search or Add..." >

    <button v-show="modelValue!==''" @click="clearQuery()" :id="$style.clear" ><Cancel/></button>

    <button v-if="exactMatch===''" v-show="modelValue!==''" @click="$emit('addItem')" :id="$style.add" ><Add/></button>

    <button v-else v-show="modelValue!==''" disabled :id="$style.addDisabled" ><Add/></button>
   
  
</template>

<script lang="ts">
import { defineComponent } from 'vue';

import Cancel from "../components/Cancel.vue";
import Add from "../components/Add.vue";

export default defineComponent({  
  props: ['modelValue', 'exactMatch'],
  components: {
    Cancel,
    Add,
  },
  setup() {
    // Setup
  },
  methods: {
    clearQuery() {      
      this.$emit('clearQuery')
    },
    enter() {
      if (this.exactMatch=='' && this.modelValue!='') {
        this.$emit('enter')
      }      
    }
  }
});
</script>

<style lang="scss" module>
@use "sass/color";

#searchBar {
  @include color.search-bar-style;
}
#clear {
  @include color.clear-button;
}
#add {
  @include color.add-button;
}
#addDisabled {
  @include color.add-button-disabled;
}
</style>