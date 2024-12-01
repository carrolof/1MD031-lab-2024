
<template>       

<div>
  <header class="header-content">
        <img src="https://img.freepik.com/premium-photo/top-view-fast-food-restaurant_198067-860129.jpg?w=900" alt = "bild" class="header-img">
        <h1>Welcome to Carrociofini Burgers</h1>
    
    </header>
    <nav>


    </nav>

    <main>
        <section id="burgers">
            
            <h2> Select Burger</h2>
            <h5> Pick one of our delicious burgers!</h5>

        <div class = "burger-grid">
          <Burger v-for="burger in burgers"
            v-bind:burger="burger" 
            v-bind:key="burger.name"
            v-on:orderedBurger="addToOrder($event)"/>
                
    </div>

        </section>


        <section id="contact">
            <form>
            <h2>
                Customer Information
            </h2>
            <h4>
                Please fill in the following information in order to get them burgers!
            </h4>
            <p>
                <label for="firstname">Firsname</label><br>
                <input type="text" id="firstname" v-model ="formValues.name" autocomplete="given-name" required="required" placeholder="First name">
            </p>
            <p>
                <label for="lastname">Lastname</label><br>
                <input type="text" id="lastname" v-model="formValues.lastname" autocomplete="family-name" placeholder="Last name">
            </p>
            
            <p>
                <label for="email">Email</label><br>
                <input type="email" id="email" v-model="formValues.email" autocomplete="email" required="required" placeholder="arne.kaiser@mail.com">
            </p>

            <p>
                <label for="Paymethod">Paymethod</label><br>
                <select id="Paymethod" v-model="formValues.payMethod">
                    <option>Banköverföring</option>
                    <option>Klarna</option>
                    <option>Swish</option>
                    <option>PayPal</option>
                    <option>Cash</option>
                </select>
             </p>
            
             <head>
                <title>HTML input type="radio"</title>
            </head>
            
          
            
                <p>Gender:</p>
                <div>
                    <input type="radio" 
                           id="Female" 
                           name="gender"
                           v-model="formValues.gender"  
                           value="Female">
                    <label for="Female">Female</label>
                </div>
            
                <div>
                    <input type="radio" 
                           id="Male" 
                           name="gender"
                           v-model="formValues.gender"  
                           value="Male">
                    <label for="Male">Male</label>
                </div>
            
                <div>
                    <input type="radio" 
                           id="nonbinary" 
                           name = "nonbinary"
                           v-model="formValues.gender" 
                           value="nonbinary" checked>
                    <label for="nonbinary">Nonbinary</label>
                </div>
              </form>

            <div id="map-container" v-on:click="setLocation">
              <div id="map-content">
                <div id="dot" v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">
                  T
               </div>
              </div>
            </div>


            <button v-on:click="submitOrder" type="submit" style="padding: 5px;">
                <img src="https://img.freepik.com/premium-vector/food-delivery-logo-eating-icon_685330-407.jpg" alt="Icon" style="width: 100%; height: 100%; max-width: 30px; max-height: 30px;">
                Send Info
            </button>
            </section>
                 
    </main>
    <footer>
        <hr>
        <p>
            &copy; 2024 carrociofini inc
        </p>

    </footer>
</div>
</template>

<script>
import Burger from '../components/OneBurger.vue';
import io from 'socket.io-client'
import menu from '../assets/menu.json';




const socket = io("localhost:3000");

function MenuItem(name, imageUrl, smakprofil, containsGluten, containsLactose){
  this.name = name;
  this.imageUrl = imageUrl;
  this.smakprofil = smakprofil;
  this.containsGluten = containsGluten;
  this.containsLactose = containsLactose;
}

/*const burgers = [
  new MenuItem("Carciofi Burger","/img/burger_real_green.png" , "vår signaturburgare", true, false),
  new MenuItem("The Cheesy One", "/img/burgers-grand-chicken-buffalo.png","en riktigt ostig jävel", true, false),
  new MenuItem("Skinny Bitch", "/img/product_burgare.png", "för figuren", false, true),
];

*/



export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      formValues:{
        name: "",
        lastname: "",
        email:"",
        gender:"",
        payMethod:"",

      },
      orderedBurgers:{},
      location : {x: "", 
                  y: ""} 

    };
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
   

    setLocation: function(event){
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

      var scrollOffset = {x: event.currentTarget.scrollLeft,
                          y: event.currentTarget.scrollTop}
      
      this.location = { x: event.clientX -10 - offset.x + scrollOffset.x,
                        y: event.clientY - 10 - offset.y + scrollOffset.y}
      console.log(this.location)
    },



    submitOrder: function(){
    console.log("Submitted values:", this.formValues);

    console.log("ordered burgers", this.orderedBurgers)

    socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                           y: this.location.y },
                                orderItems: this.orderedBurgers,
                                customerName: this.formValues.name,
                                customerLastname: this.formValues.lastname,
                                customerEmail: this.formValues.email,
                                customerGender: this.formValues.gender,
                                customerPayment: this.formValues.payMethod

                              }
                 );

    this.formValues.name = "";
    this.formValues.lastname = "";
    this.formValues.email ="";
    this.formValues.gender = "";
    this.formValues.payMethod= "";

    console.log("location", this.location) 

    },

    

    addToOrder: function (event) {
    this.orderedBurgers[event.name] = event.amount;
    console.log("Current order: ", this.orderedBurgers)
}

  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Danfo&family=Fontdiner+Swanky&family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Slackey&display=swap');

body {
    font-family: 'josefin sans' ;
    font-size: 12pt;
}

/*p {
    color: rgb(25, 0, 255);
}
*/

h1 {
    font-family: 'slackey';
    font-size: 36pt;
    text-shadow: 5px 5px 5px rgba(0, 0, 0, 0.5);
    color:rgb(246, 220, 104);
}
main, header, footer, nav ul {
    max-width: 100rem;
    margin: 0 auto 0 auto;
}
main {
    background-color: white;
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */

/*header {
    background-image: url("https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSB9IPTG_K5PmzO5Xg7_VHuOPdZnfu3hNL7jw&s");
    background-size: cover;
    overflow: hidden;
    width: 100%;
    height: 200px;
    opacity: 0.5;
}*/

.header-content h1 {
    width:100%;
    margin: 0 auto;
    text-align: center;
    position: absolute;
    bottom: 45%;

}

.header-content{
    height: 300px;
    overflow: hidden;
    max-width: 100rem;
    margin: 0 auto;
    padding-left: 20px;
    padding-right: 20px;
    position: relative;
}

.header-img {
    width: 100%;
    height: auto;
    display: block;
    opacity: 0.9;
    
    
}

nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: grey;
    padding: 1em;
}

.Very-good {
    color:  rgb(56, 255, 56);
}

.Master {
    color: rgb(56, 255, 56);
    font-weight: bold;
}


.glutenfri{
    color: green
}

#contact{
    background-color: rgb(216, 187, 57);
    color:ghostwhite;
    text-shadow: 2px 2px 5px rgba(0, 0, 0, 0.5);
    
}

#contact p {
    columns: ghostwhite;
    text-shadow: 2px 2px 5px rgba(0, 0, 0, 0.5);

}

#burgers{
    background-color:white;
}

button:hover{
    background-color: rgb(33, 214, 33);
    cursor: pointer;

}

section, header{
    margin: 20px
}


button{
    margin-top: 10px;
    margin-bottom: 15px;
    margin-left: 10px;
    
}


#contact {
    border: 2px dashed white;
    padding-left: 20px;
    padding-right: 20px;
}

.burger-grid {
    display: grid; 
    grid-template-columns: repeat(auto-fit, calc((100% - 40px)/3)); 
    gap: 20px;
}


#burgers {
    border: 2px dashed #333;
    background-color:white;
    padding: 20px

}

/*#map {
    width: 600px;
    height: 600px;
    background: url("/img/polacks.jpg");
    background-size:  1920px 1078px;
    background-position: top left; 
    overflow: scroll;
    cursor: crosshair;
}
*/

#map-container {
  position: relative;
  width: 600px; /* Synlig bredd */
  height: 600px; /* Synlig höjd */
  overflow: scroll; /* Möjliggör scrollning */
  border: 1px solid black; /* För att visa gränser */
}

#map-content {
  width: 1920px; /* Faktisk bildbredd */
  height: 1078px; /* Faktisk bildhöjd */
  background: url("/img/polacks.jpg") no-repeat;
  background-size: contain; /* Anpassa storlek vid behov */
}


#burgers p {
    font-size: 12pt;
    text-align: center;
    
}

#burgers h5 {
    font-size: 14pt;
    
}

#dot {
    position: absolute;
    background: black;
    color:white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
    line-height: 20px;
  }

@media (max-width: 775px) {
    .burger-grid {
        grid-template-columns: 1fr; 
    }
}






</style>