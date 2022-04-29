<template>
  <div :id="$style.layout" >

    <SearchBar v-model="searchQuery" :exactMatch="exactMatch"
    @clearQuery="clearQuery()" @addItem="addItem()" @esc="clearQuery()" @enter="addItem()" />

    <div :id="$style.list">
      <div v-for="item in filteredItems" :key="item.id">
        <ListItem :item="item" :exactMatch="exactMatch" @deleteItem="deleteItem(item.id)" />
      </div>
    </div>

    <button @click="alphabeticalSort=true" :class="[$style.sortButton1, alphabeticalSort ? $style.selectedSort: '']">
      Sort by Value</button>

    <button @click="alphabeticalSort=false" :class="[$style.sortButton2, alphabeticalSort ? '': $style.selectedSort]">
      Sort by Added Date</button>
    
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

import SearchBar from "./components/SearchBar.vue";
import ListItem from "./components/ListItem.vue";

export default defineComponent({
  name: 'App',
  components: {
    SearchBar,
    ListItem,    
  },
  setup() {
    // Setup
  },
  data() {
    return {
      searchQuery: '',
      items: [
        {id: 1, text: 'Robert Keřlík', timeStamp: 'Fri Apr 29 2022 11:09:30 GMT+0200 (Central European Summer Time)'},
      ],
      filteredItems: [
        {id: 1, text: 'Robert Keřlík', timeStamp: 'Fri Apr 29 2022 11:09:30 GMT+0200 (Central European Summer Time)'},
      ],      
      exactMatch: '',
      numberOfItems: 1,
      alphabeticalSort: false,
    }
  },
  methods: {
    clearQuery() {
      this.searchQuery = ''
    },
    addItem() {      
      this.items.push(
        {id: parseInt(this.numberOfItems as any) + 1, text: this.searchQuery, timeStamp: String(new Date())}
      )
      this.filteredItems = this.items
      localStorage.setItem('savedList', JSON.stringify(this.items))
      this.searchQuery = ''
      this.numberOfItems = parseInt(this.numberOfItems as any) + 1
      localStorage.setItem('numberOfItems', String(this.numberOfItems))
      if (this.alphabeticalSort) { this.filteredItems = this.items.sort((a, b) => a.text.localeCompare(b.text)) }
    },
    deleteItem(itemId: number) {     
      const itemsAfterDelete = this.items.filter(item => item.id !== itemId)
      this.items = itemsAfterDelete
      localStorage.setItem('savedList', JSON.stringify(this.items))
      this.filteredItems = itemsAfterDelete
    },
  },
  watch: {
    searchQuery(to, from) {
      this.filteredItems = []      
      this.exactMatch = ''
      for (let i=0; i<this.items.length; i++) {
        if ( this.items[i].text.toLowerCase().includes(to.toLowerCase()) ) {
          this.filteredItems.push(this.items[i])

          if ( this.items[i].text.toLowerCase() === to.toLowerCase() ) {
            this.exactMatch = this.items[i].text
          }
        }        
      }      
    },
    alphabeticalSort(to, from) {
      if (to===true) {
        this.filteredItems = this.items.sort((a, b) => a.text.localeCompare(b.text))
        localStorage.setItem('alphabeticalSort', 'yes')
      }
      else {
        this.filteredItems = this.items.sort((a, b) => (a.id > b.id) ? 1 : -1)
        localStorage.removeItem('alphabeticalSort') 
      }
    }
  },
  mounted() {
    const retrievedList = localStorage.getItem('savedList') as string
    if (retrievedList!==null) { 
      this.items = JSON.parse(retrievedList)
      this.filteredItems = JSON.parse(retrievedList)
    }
    const retrievedCount = localStorage.getItem('numberOfItems') as unknown as number
    if (retrievedCount!==null) { 
      this.numberOfItems = retrievedCount
    }
    if (localStorage.getItem('alphabeticalSort')==='yes') {
      this.alphabeticalSort = true
    }
    

    if (this.alphabeticalSort) {
      this.filteredItems = this.items.sort((a, b) => a.text.localeCompare(b.text))
    }
    
  }

});
</script>

<style lang="scss" module>
/*@import "./sass/_color.scss";*/
@use "sass/color";

#layout {
  @include color.layout;
}
#list {
  @include color.list-section;
}
.sortButton1 {
   @include color.sort-button1;   
}
.sortButton2 {
   @include color.sort-button2;
}
.selectedSort {
   @include color.selected-sort;
}
</style>
