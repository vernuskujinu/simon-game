<template>
  <div  data-index='0'> </div>
</template>

<script>
export default {
  name: 'part',
  data() {
    return {
      itemRefs: [],
      selectedItem: 0,
      index: 0
    }
  },
  props: ['title'],
  methods: {
    setItemRef(el) {
      if (el) {
        console.log('el: ', el.dataset.index);
        this.itemRefs.push(el);
        console.log(this.itemRefs)
      }
    },
    // selectItem() {
    //   this.selectedItem = this.selectedItem === index ? null : index;
    // }
  },
  beforeUpdate() {
    this.itemRefs = []
  },
  updated() {
    console.log(this.itemRefs)
  },
  
  // props: {
      
  //   // msg: String
  // },
  // methods: {
  //   showRef() {
  //     console.log(this.$refs.part);
  //   }
  // }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* // h3 {
//   margin: 40px 0 0;
// }
// ul {
//   list-style-type: none;
//   padding: 0;
// }
// li {
//   display: inline-block;
//   margin: 0 10px;
// }
// a {
//   color: #42b983;
// } */
</style>
