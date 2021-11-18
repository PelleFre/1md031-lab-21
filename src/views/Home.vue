<template>
  <link rel="stylesheet" type="text/css" href="style.css">
  <html lang="en">
  </html>
   <header>
      
       <img src="headerpic.jpg" alt="id">
       <h6><b>Burgers Online</b></h6>
      
      
   </header>
   <nav> <h2>Menu items </h2></nav>
   <main>
      <section class="burgersection">
            <Burger v-for="burger in burgers"
            v-bind:burger="burger" 
            v-bind:key="burger.name"
            v-on:orderedBurger="addToOrder($event)"
            />
               <h2> {{ Burger }} </h2>
      <!--   <div id ="burg1">
           <h2>The Good Burger</h2>
            <img src="burger1.jpeg" alt="Span" title="The Good Burger" id ="burgpic">
            <span id="burgerThings">
               <ul>
                   <li>This burger is made of beef</li>
                   <li>Very good</li>
                   <li>The bestest</li>
               </ul>
           </span>
         </div>
         <div id = "burg1">
           <h2>The Bad Burger</h2>
          
           <img src="burger2.jpeg" alt="Span" title="The Bad Burger" id ="burgpic">
           <span id="burgerThings">
           <ul>
               <li>Dont belive in climate change</li>
               <li>Good'nt</li>
               <li>Speaks russian fluently</li>
           </ul>
       </span>
       </div>
       <div id ="burg1">
           <h2> The Ugly Burger  </h2>
           <img src="burger3.jpeg" alt="Span" title="The Ugly Burger" id ="burgpic">
           <span id="burgerThings">
           <ul>
               <li>Fish</li>
               <li>Disgusting</li>
               <li>Soggy bread, dont eat</li>
           </ul>
       </span>
       </div>
       
     --> 

     </section>
      <div id="MenuBurg">
       <div id="MenuInner">
           <h2>Customer information</h2>
           <br>
           <div id="CustInfo">
           <p>
               <label for="firstname">Full name</label><br>
               <input type="text" id="firstname" v-model="na" required="required" placeholder="First and last name">
           </p>
         
           <p>
               <label for="Email">Email adress</label><br>
               <input type="email" id="email" v-model="mail" required="required" placeholder="E-mail address">
           </p>
           <p>
               <label for="Street">Street</label><br>
               <input type="text" id="Street" v-model="street" placeholder="Street name">
           </p>
           <p>
               <label for="House">House</label><br>
               <input type="number" id="House" v-model="ho" required="required" placeholder="House number">
           </p>
           <p>
               <label for="Burger">Payment of choice</label>
               <select v-model="selected" id="pay">
                  <option disabled value="">Please select one</option>
                   <option>Cash</option>
                   <option>Card</option>
                   <option>Paypal</option>
                   <option>Swish</option>
               </select>
            </p>
            <br>
            <p>
               <label for="Gender">Gender</label><br>
              
               <input type="radio" id="male" value="male" v-model="gender">
               <label for="male">Male</label><br>
              
               <input type="radio" id="female"  value="female" v-model="gender">
               <label for="female">Female</label><br>
              
               <input type="radio" id="other"  value="other" v-model="gender" >
               <label for="other">Other</label><br>
             
             
           </p>
            <button v-on:click="sendInfoClick" type="submit">
                  Send Info
             </button>
       </div>
       <div id="mapbox">
          
          <div id="map" v-on:click="setLocation">{{location}}
             <h3>Select address</h3>
            <div
              v-bind:style="{
                //left: location.x + 'px',
                //top: location.y + 'px',
              }" 
            >
            </div>
          </div>
          </div>
       </div>
   </div>
    <footer>
   <div>
       <p> © PellePri Produktions</p>
   </div>
</footer>
   </main>
 
</template>

<script>
import Burger from '../components/Burger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'
const socket = io();

//Here i stop for the night! GL PELLE

//Object Constructor Function

/*function MenuItem(prod_name, kcal, pict, glut, lact) {
    this.name = prod_name; 
    this.calories = kcal;
    this.picture = pict;
    this.gluten = glut;
    this.lactose = lact;
    this.line2 = "This burger is glutenfree";
    if(glut){
       this.line2 = "This burger contains Gluten"
    }
    this.line3 = "This burger is lactosefree";
    if(lact){
       this.line3 = "This burger contains lactose"
    }

}
const burgarray = [
new MenuItem("Burgir","432",
 "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAoHCBUVFRgVFRESFRgVFRISGBISFRISGBgSGBgZGRgVGBgcIS4lHB4rHxgYJjgmKy8xNTU1GiQ7QDs0Py40NTEBDAwMEA8QGhISHjEkISExNDQ0NDQ0NDQ0NDQ0NDQ0NDE0NDQ0MTQ0NDQxNDQxNDE0NDQ/NDQ/MTQ0PzQ0NDQ0P//AABEIAKkBKgMBIgACEQEDEQH/xAAcAAACAgMBAQAAAAAAAAAAAAAABAEDAgUGBwj/xAA9EAACAQIDBQUGAwcDBQAAAAABAgADEQQhMQUSQVFhBjJxgZEHEyJSobFCwdEURGJykuHxFYLwIyRDU1T/xAAZAQEAAwEBAAAAAAAAAAAAAAAAAQIDBAX/xAAkEQADAAICAgMBAAMBAAAAAAAAAQIDESExElEEE0FhInGhMv/aAAwDAQACEQMRAD8A9mhCEAIQhACEIQAhCEAIQhACEJF4BEmYM4GpA8TNfiNt0E1qKbcF+L7SHSX6Spb6Rs4TmK/a1B3abt1JCj9YjW7W1D3aaL43b9JR5ZX6aLDb/DtYTzyp2lxDaOF/lRfzvFam2sQczXqDwKr9hK/dJP0UemQnlb7Vrf8A0Vf62mH+q1f/AHVf62j7kT9D9nrEJ5P/AKxXGmJqj/eT95kNv4kaYl/MK33En7UQ8FHq14TzCn2sxa/+RW/nRfytHaHbqqO/SpsP4WZD6WMlZEyrxUj0KRORwvbug2TpUpnmLOPpn9JvMFt3DVe5Xpk/KW3W9DnLKkyrlrtG0hIBkyxUIQhACEIQAhCEAJAkyBAJhCEAIQhACEIQAhCEAIQkGARIJAieO2gtPU3bgo/Oc1j9ps9wTYfKunnMbzTP+zWMNV/o3uM21TTIHfbkunmdJpsVt+o1wpVB0zPrpNM7n/EXcnnOSvkt/wAOuPjyu+S/E4pn7zs3Utf6aRN3gRK2ExeVs3UJdEMZizySJU5lHbLeBi9WLvUMsdJWySVbIcoqLzBnPOZshlZEsqZHijAuZj7yDLMGEsrZDlE/tBEDXvKXErYS6tlXKGDUkF7xctMS80VlHJvNn9oMRQ7lZ7fI5319Dp5TsNk9vkay4hNw/OnxL4kaj6zzL3kyV5rNv2ZVjTPfsLi0qKGpurqfxKQYxeeF7K2tVoPv0nKnK63urDqunnPTOzfaxMRZHtTqfKe6/VT+U2mkznrG5OphIvJlzMIQhACQJMgQCYQhACEIQAhCEAJEmRACazaW0Ny6r3ufKM42vurlqdJz9Vbm5z4znzZfHhdm+HH5cvoRruSbk3J4mLssealMTRnnVtvZ3rSWka1xKmSbJ6Epaj0mTTLqka9klRSPtStK2SNFtoRdJWyR10lLrIAmyTFkjW7MGWSg0KMkpdI4yyl1l0yrE3SU1BG3WUuslMhixEqYRl1lRWXTKsXZZgwjDLKmWXTIZQRIvLiJjaXTZVohGjFOqQQQdDcHSx5iVCnM1WaKtFXOz1TsX2k9+vuqjf8AUUZMfxrz/mnXieE4Gu9N1dDZlIIPhPaNj49a9JKi/iGY5MNR6zox35cHJljxezYQhCamISBJkCATCEIAQhCAEIQgBIMmYnSAavFneY9MvKKNTj7JKyk8+0222dcUkkhE0YCjHTTh7uU8S/ma96MqajNk6SlkhwWVGqq0Yu9KbWokVdMplUmis1b0pQ1ObR6coenM3JdUa56cpNOPvTlBTKEi2xV0i9SOVFizrJAmwlbJGXErtJSKtijLKWSPOkodZdENirLMSkZKSVS8sirYp7vpMxRja0ZclKaJlWxFMPMxh5s1w0n9ngjyEsPh75Tv+wDsEqITkrBh/uGf2nKYahnO27H4Yqrt8xAHl/mbYU/LZhmpa0dNCEJ1nKEgSZAgEwhCAEIQgBCEIATFtJlIJgCrJMCkorbRQX3XRt0gGxvaWUcQGy0PI8pyPxbNltGRSYsJewlbSGiUyl1lW7GSJW4lWi6Yk41EqanlGWQ3EmoBpM3JZUaqsthKGSP4lJSyTNzyaKuBHcvMKlDhG6FO9z5QrLr0jw4J8+TUVaESqrmZva9PTqJq66ZyrllpvZq6yytRHKqSgraSpZZ0V1ElXu40RnIsJdSZuhcJ0mdKlfOXbvCWIOU0mSjowSlL0w8zpgS9XF5ooM3TIVJIpdJN/pF8ftFaVMvus1rZLYfeW1JXbYwAFuSQBrc2yHOZ9k+25NYYeotMU2YpTqLcHe4b19b85xe2NuGuNxU3UBBuSQxPI24TV0WCspNwEZXuutlIPw9cpHn4vg2WHafkfSQMmIbKxyV6SVEYMrKCMwbHiDbiOMenUns4WtPRlIEmQJIJhCEAIQhACEIQDEzVbaxwRd2xLNcCxtbxM2xnN9pqLEqwItYrnz5zD5FOcbaNcMqrSZz2JxSo28p3SBpkR5idBh9tJUQNbdYGxHX5vCcZj6ZJyNiLxbD13pm6ZcDe5Dfwn9Z5WHM03vpnp5fjqpWu0eoYKoXXe3sj9pNSpblboZwuG7Q1FXcKZHP4GINvO8rxnaEgj3YcZZhyBc8sp2PPOkca+Pezthj0BO84XTvWEFxKNfddGtwVlY/SeZ4vHPXFnYKNbIxufEyFpKN243bZXUbp8SZm8+vw1XxvbPTwJUBdjPPd6rv7wrVl3dBvvnyFidLRvD7brKwHvCcwSCFbLlKr5CfGg/jUuUzscQl5Syx/DhatMOrkqy7w/T1mpx2JSmCW3rD5QSZ0qX37OZ1/wzVbXlZtYi3nM6jqKIrs3wsgYZAWubZ+svq4NAtyxOXzHyMeLHkhPFp8I0ymlrHK82NaorEgOcsiA2k1e1Xo00LMeHF7fSUcmksTquLa6xWtWA46TV4DbrorKKIf5ajGx3TwtaZu71AS9gCDYIOPC5Mo6mUbTFMcGKXW8WxG0VXMsAOZsM5q0R72VWtxtcZeJMaOEDi2vK+g6kyPtSL/AEkttymNWv4Supt62QUg8CwMq/ZUUktwJAIVT9eUWxAHH01+svOTfRV4fY/g9slxZqu6TlYZZ8hNrhUYDeNR+dt684vEAAZCx6c+cfwm1C4ALbrABTc5HqJtL2YXDXR1yMDrvNfiTaaTbm0daKWte7EZ5j8M2JxCpTLlsgtr8zbQCciv165ytMnDG3tkquecsMrdxG9jKj1U98WFMMpfdFyVBzFuXPpeZnQz1j2W0yMECQw3qlRhfQi9t5ehnaRTZ9Sm1NTSKlN0bu5bdtyFo3O+eJR5lPdNkyBJkCWKkwhCAEIQgBCEIBjNH2jYWXPO97dOc3k1u1tm+9AINmW9r6HoZjnl1DSNMNKbTfRxOKsza/3ERejfW1hc8v8AM2uJ2e6tZkYa5gEjwB4xJkzIJz03TPE8HL5R7U2mlpiDg2yAvz42mNbDk2+EeNhkfzMbSh8Vhn0PCTVQ2A16Dh0k745H6aV6RRmI56EZeJEbSv8AAoK3430zjH7P+LIZa8/1i70reIzjyLcMrDk5An5rRWsrX3rG9/tH6CW5g8D/AM4Qc3Iy5i5+8jX6N8mx2B2gbDt8dzSf4iMyUPzAcRzE6vaGLo1KZdGRgRwN/UcJ57UUEjeysLdOAGQmIpAA7uR5DnzvwnTj+RUrXZzZPjTb8umdXSromFZKzAJvMVU67pPdA4zlMT2mr1CRSY00TJRugs1uJvoJXiUJvvXN9Bcn6mU1aS8BmeHLkRLVndfwicEz/RTDb53irurlixIJFxqby6vgFNixLEm9yb/4l1FAptnpnn5mX0yb8u4LnPMZZTN2zVQl0hYYMgA5Doczbx5Rz3A3QpPEkHoRpaWqdRrY97mJbUXP+EDLjn0mdU2WSFaNMW4XHE8BzkNSAtrbOxEYpUbG5Fxu3sNcuMqxNTK3AESm2X0a6tSysdORmkrtcn0nQYhbIWv8wt9pzNib+M6sPJlZU4mPuxy/KXkWlZqDn1nQjFozVrCxJsNM7+cparnzkqhOZIUH1ltMreyqWPOQ2QtIxo0GY3PoJucNhQtiSBbPnMcNRci5+HoI4Ft6ZzOoddk+aXR2fs9x5FV6W9dWXfAPB11t4g/Seizg/Z1s2kyNX3PjWo6K1zkpA0E72duBUp1R5+dqr2iZAkyBNzImEIQAhCEAIQhACQRJkQBTGNa3Wa+pg6bm7Kp8h943tLQHrEVqdZxZmlXKOrEn47TNdjtgDvUX3TybNbHWaQ7PrqxDKqgZZHe3uotOyR5RUJH4AbnhxnLmianc8M3jLUvnk4qvhHyzuBw0ylOJLD8ByA0zvOzqJTbJhuk8TE8Rs0jTMc5wVOWf6jpnNL74OMfFDeAKlB1B9YzdQMuPG3EzcvgukofBDlK/Y/1GvlJqqyAjW5GXMW6xcaXJyJ4TbHB2uNL9BKP9PA0AlllQ2hfE0Wbu2sAM4tVpi6hNRa7cMps1pkaX0tkYsmDKtvXORvJWSSRFx8R5Dj14yx6barnxvln1+kur0CdGA8RfOVMXUWBB+HdNxJVprsNDOFQ3uzEk3sPv9JatM31yJE1FXbAV1JWwWwNhfe6zZUMcjCwPfFxcWtne2c0c01sp0+RpqHxg3FunDpFcbRAY9fpGxiwFN2B56axQ4pWvplwuM5Hi2SqEcTbc3fEnxnNFLTe7QxN8h9JrhQvrn0nTilpclLtGqrMd6ygnrwmeGwLnO2vEzbU6aroB4f3lwAnT5I5W22I09ljVjc8hHqVFVGQGctvMHaQ6I0ZkytqmUh3yitV5HZOtHqfsxH/bOfmrP9ABO0nK+zyhuYKnc2Ll6tujm4+lp1QndC/xRwX/AOmTIEmRLlSYQhACEIQAhCEAJEmRAE9opdDbhnNGZ0tRbgjmDOZxKlGKkHoek4/lQ3qkdXx6XMstWr5S1KvOIB7i94K9tSLnrOJWdTnY6zg5EecwpuV6qeB4RM1je3SQ9aVpp89MjwGqqjUcYsyjlD9oA10+vlJaxFxmJjXBdJoXdByi9RFl1QxZ2mDpGk7F2UReqZbWeKOZg2n0aJC9Z5rMXirfCNZftDEbvwg58egmrXPPWdmDB+siq10XYccSBeP1CHAvz18Jr0y+8sNWdqWuDnbbeycQoOXL6xT3YEuNSV1GkpIbZimQPgZF5iGzmLPnlLoqWAzIPKC8wNSSBln6ytqsWatKWrxoDNSt1mFKzuAdL5+EQaqWNgLmbTZmDYm5vN8eLnbMMmVLhHpWw9qWVVGQAAA5AD+06/CYveE8/wBj4Rhadls+kQBOo4zeK0zlVMSwQCYQhACEIQAhCEAIQhAIiuMwi1Fsw8+I8I1CQ1vsJ66OPx+z6tK5AaovMd4eI4xGnjBfqMrEWN/Od7Nbjti0aveQX+ZfhPqJy5PizT2uGdMfJqeH0csmKAucrkWzmLVbg2OuV5sMX2Vb8FS/JXz+s0+J2XiU1p7wHGmdfIzlr42RdHVOfHX8LDW9SAPDwkJiylz5kdOU1NeuwyZHQi2TAr9ZU2MF7XHqNecxrFS7RsqilwzpEqq4FsiRfdP5Sqqlpo0xduPC334x9NondANnv9vGc14H2idejGtNbjsUEUsdeAj2IxdOxJYiw7p4nkCJyeNxDO5Y6cByEjDhp1/ki3lwVs5YljqTMgRFzUt+hgaw1vwynoJa4RlXYyryN+JviZW+ItLKGUG2qCVmrEXxQHEeF5S2LHDPwuZZQyrpex81OswNWIiu7d1HPlGKWz8S+lNvO80WKmUeWV+kmvaUPius3GF7IYl9VYX4Wm9wHs8c95fWazh9mVfI9I4b3rN3VJMbw2yqj6gjoJ6rgOwirrb0nQYPszTT8ImsxKMay1R5fsvsyxt8M7PZfZm1ridnRwCLoojIQDhLmZqcHspVGk2VOiBLpMAgCAkyBAJhCEAIQhACEIQAhCEAIQhACEIQAkESYQBerhUbvIp8QJrMT2bwz96kvkLTcwkNInbRydfsPhz3S6fysQIq3YcjuYioByNmnbSZDxy/wsstezzqr2Ac/vB9BFX9mzn94b+lf0np0JH1T6JeW/Z5Y3sxc/vTZ6/CuclfZcT3sS/kBPUhJkqJ9Fftr2eZp7Laf4q1Q+downsvww1LnxaeiyJPjPojyr2cPR9nGFX8F/HObCj2Jwq6Ul9BOohJ0iNs01Hs3h10pr6CO09m010RfSOwgFK0FGiiWBZlCARaTCEAIQhACEIQAkCTIEAmEIQD/9k=",false,true),

new MenuItem("Bargar","976",
"data:image/jpeg;base64,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",true,true),

new MenuItem("Burgar","1080",
"https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRrDQR-Pka_yO_k9n_T_Tsds7p1k2oa4PTUuw&usqp=CAU",false,false)
]
console.log(burgarray);
*/
export default {
  name: 'Home',
  components: {
    Burger
  },
  data: function () {
    return {
      //burgers: burgerArray
      burgers:menu,
      na: "",
      mail:"",
      street:"",
      ho:"",
      pay:"",
      gender:"",
      orderedBurgers:{},
              
    }
  },
  
  methods: {

     addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
      console.log("hej");
      console.log(event.name + event.amount)
  },

    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },

    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    },
    sendInfoClick: function (){
      console.log(
        this.na,
        this.mail, 
        this.street, 
        this.ho,
        this.pay, 
        this.gender,
        this.orderedBurgers);

        this.na ="",
        this.mail="", 
        this.street="", 
        this.ho="",
        this.pay="", 
        this.gender="",
        this.amountOrdered = 0
    }
  }
}
</script>

<style>
  #map {
    width: 500px;
    height: 500px;
    background: url("/img/polacks.jpg");
  }
  @import 'https://fonts.googleapis.com/css?family=Pacifico|Dosis';
 
body {
   font-family: Helvetica;
   margin:0%;
}
nav{
   border-bottom: 1px dashed rgb(2, 2, 2);
   background-color: rgba(139, 139, 139, 0.726);
   padding: 30px 0px 30px 0px;
}
footer{
   padding: 10px;
}
header{
   border-bottom: 1px dashed rgb(2, 2, 2);
   height: 200px;
   text-align: center;
   overflow: hidden;
}
 
header > img{
   width: 100%;
   height: 200px;
   opacity: 0.7;
   position: relative;
}
.burgersection{
   display: grid;
   grid-template-columns: 26% 26% 26%;
   grid-gap: 4%;
   background-color: rgb(182, 199, 152);
   height: 60%;
   margin-top: 0%;
   padding: 30px 0px 30px 7%;
   min-height: fit-content;
}
 
h6{
   position: absolute;
   top: 60px;
   left: 100px;
   font-size: 80px;
   margin-top: 0%;
   color: rgb(0, 212, 240);
   text-shadow: -8px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
}
h2{
   margin: 0px;
   text-align: center;
   font-size: 30px;
}
h3{
   margin: 0px;
}
/*#burgerThings{
   color:honeydew ;
   font-weight: bold;
}*/
#MenuBurg{
   clear: left;
   padding-top: 3%;
   background-color:rgba(139, 139, 139, 0.726);
   border: 1px dashed rgb(0, 0, 0);
   height: 40%;
   min-height: fit-content;
}
#MenuInner{
   /*grid-template-columns: 50% 50%;*/
   /*text-align: center;*/
   display:flex;

}
#CustInfo{
   width: 40%;
}
#mapbox{
   width: 40%;
   float: left;
}


/*
#burg1{
   float: left;
}
 
#burgpic:hover{
   box-shadow: 14px 14px 9px rgb(88, 97, 74);
   transform : scale(1.06, 1.06);
   transition: all 0.2s ease-in-out;
 
}

#burgpic {
   width: 97%;
   height: 60%;
   border: 5px solid rgb(2, 2, 2);
}*/
button:hover {
   background-color: dimgray;
   cursor: pointer;
}
button{
    margin: 10px;
    padding: 4px;
}

</style>
