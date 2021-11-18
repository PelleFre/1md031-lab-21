<template>
  <div id ="burg1">
    <h2>{{burger.name}}</h2>
    <img v-bind:src= burger.picture id ="burgpic">
    <span id="burgerThings">
        <ul>
            <li>kalories: {{burger.calories}}</li>
            <li>{{burger.line2}}</li>
            <li>{{burger.line3}}</li>
        </ul>
    </span>

        <button v-on:click="decrease" type="idecrease">
          -
        </button>
        {{ amountOrdered }}
        <button v-on:click="increase" type="increase">
          +
        </button>
             

  </div>
</template>

<script>
export default {
  name: 'Burger',
  props: {
    burger: Object
  },
  data: function () {
  return {
    amountOrdered: 0,
  }
  
},
  methods: {
   decrease: function (){
     if(this.amountOrdered<1) {
       null
     }
     else{
      this.amountOrdered --
    }
  },
   increase: function (){
      this.amountOrdered ++
      this.$emit('orderedBurger', {name: this.name,
      amount: this.amountOrdered});
   }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#burg1{
   float: left;
}
#burgpic {
   width: 97%;
   height: 60%;
   border: 5px solid rgb(2, 2, 2);
}
#burgpic:hover{
   box-shadow: 14px 14px 9px rgb(88, 97, 74);
   transform : scale(1.06, 1.06);
   transition: all 0.2s ease-in-out;
}
#burgerThings{
   color:honeydew ;
   font-weight: bold;
}
</style>
