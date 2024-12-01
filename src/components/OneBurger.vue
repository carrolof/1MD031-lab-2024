<template>



<div class="burger-item">       
    <img v-bind:src="burger.imageUrl">
    <h3>
      {{ burger.name }}
    </h3>
      <div class="smakprofil">
        <h4>
            {{ burger.smakprofil }}
        </h4>
        
        <p> Amount  {{ amountOrdered }}</p>

        <div class="button-container">

        <button class="decreaseButton" v-on:click ="decreaseOrder"> - </button>
        <button class="increaseButton" v-on:click ="increaseOrder"> + </button>


        </div>

      </div>
        <dl class="allergener">
            <dt>
                <h4>
                Allergener
                </h4>
            </dt>
            <dd v-if="burger.containsLactose" class="laktos">
                Lactose
            </dd>
            <dd v-if="burger.containsGluten" class="gluten">
                Gluten
            </dd>
        </dl>
</div>      
</template>

<script>

export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },

  data: function () {
  return {
    amountOrdered: 0,
  }
},
  methods: {
    increaseOrder() {
      this.amountOrdered++;
      this.$emit('orderedBurger', { name:   this.burger.name, 
                                amount: this.amountOrdered 
                              }
  );
    },
    decreaseOrder() {
      if (this.amountOrdered > 0) {
        this.amountOrdered--;
        this.$emit('orderedBurger', { name:   this.burger.name, 
                                amount: this.amountOrdered 
                              }
  );
      }
    }
  }
}



</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.gluten{
    color: #ff5500
}

.laktos{
    color: blue
}


.burger-item{
    display: inline-flex;
    align-items: center;
    justify-content: center;
    
    height: 450px;
    padding: 15px;
    background-color:white;
    
    border: 1px solid black;
    border-radius: 5px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);


}

.burger-item img{
    width: 100%;
    height: 150px;
    object-fit: contain;
    border-radius: 5px;

}

.burger-item h3{
    font-size: 22px;
    margin: 10px 0;
    color: #333;
}

.burger-item p{
    font-size: 8px;
    color: #333;
}

.burger-item dl{
    margin: 10px 0;
    height: 60px;
  
}


.allergener{
    font-weight: bold;
    font-size: 17px;
}

.smakprofil h4{
  font-weight: bold;
  color: #333;
  font-style: italic;
  font-size: 14pt;

}

.burger-item{
  display: grid;
}

button {
  padding: 5px 10px;
  margin: 5px;
  font-size: 16px;
  background-color: #dcc321;
  color: #333;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

button:hover {
  background-color: #f17d1e;
}

.increaseButton:hover {
  background-color: green;
}

.decreaseButton:hover{
  background-color: red;
}

.button-container{
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-top: 10px;

}




</style>