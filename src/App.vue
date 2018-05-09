<template>
<div id="app">
  <div class="containers">
    <div class="" style="margin-top:20px">
      <img class="pageBanner" src="http://gearmedia.ign.com/gear/image/article/819/819595/ag-riff-master-guitar-hero-controller-20070912005554936.jpg">
    </div>
    <div>
      <br>
      <br>
      <button class="btn btn-primary" @click="statusadd = !statusadd" v-if="!statusadd">click for add product</button>
      <button class="btn btn-primary" @click="statusadd = !statusadd" v-if="statusadd">click for hide</button>
      <br>
      <br>
    </div>
    <div class="row" style="margin-bottom: 30px" v-if="statusadd">
      <div class="col">
        <input type="text" class="form-control" placeholder="Name Item" v-model="n">
      </div>
      <div class="col">
        <input type="number" class="form-control" min="1" placeholder="Price: 1ea" v-model="p">
      </div>
      <div class="col">
        <input type="number" class="form-control" min="1" placeholder="Amount" v-model="a">
      </div>
      <div class="col">
        <input type="text" class="form-control" placeholder="link Product Image" v-model="l">
      </div>
      <button class="btn btn-primary" @click="addProduct">Add</button>
    </div>
    <div class="row" style="margin-top:30px">
      <div class="col-9" v-if="productFire">
        <ul>
          <li class="col-3" v-for="product in productFire" :key="product['.key']">
            <a><img :src="product.imgLink" width="300" height="255"></a><br/> {{product.productName}}
            <br/>
            Now {{product.amount}} left <br>
            <p class="price"><h5>{{product.price}}Bath</h5></p>
            <button type="button" class="btn btn-primary" @click="addToCart(product.productName,product.price,product['.key'],product.amount,product.imgLink)">Add to cart</button>
          </li>
        </ul>
        <hr>
      </div>
      <div class="col-3" style="text-align: center">
        <h5>Item in your cart</h5> {{itemCounter}} items<br>
        <div v-for="(item , index) in items">
          {{ countItem(index) }}
          <ul>
            <li class="list-group-item" style="width:320px; margin-bottom: 10px; "><img :src="item.imgLink" width="50" height="50"> {{item.productName}} : {{item.price}} ฿ <br><button class="btn btn-danger" style="margin-bottom: 20px; margin-top: 20px" @click="pickOff(item.productName,item.price,item.amount,item.imgLink,item.key,index)">cancle</button></li>
          </ul>
        </div>
        All of these : <strong>{{cost}} Bath </strong><br>
        <button type="button" class="btn btn-warning" @click="checkOut">Check out</button>
      </div>
    </div>
  </div>
</div>
</template>
<script>
import firebase from 'firebase'
let config = {
  apiKey: 'AIzaSyB9K-i0ZiFB7HISJtGFVadC9pdO9nc5qX8',
  authDomain: 'todo-list-e337a.firebaseapp.com',
  databaseURL: 'https://todo-list-e337a.firebaseio.com',
  projectId: 'todo-list-e337a',
  storageBucket: 'todo-list-e337a.appspot.com',
  messagingSenderId: '78015838709'
}
var firebaseApp = firebase.initializeApp(config)
var db = firebaseApp.database()
var todosRef = db.ref('todos')
var productRef = db.ref('products')
export default {
  name: 'App',
  firebase: {
    todosFire: todosRef,
    productFire: productRef
  },
  data () {
    return {
      statusadd: false,
      text: '',
      productName: '',
      price: '',
      addProductButtonState: false,
      addProductPassword: '',
      passwordForAdd: '123456',
      onAdd: false,
      p: null,
      n: null,
      l: null,
      a: null,
      itemCounter: 0,
      cost: 0,
      items: []
    }
  },
  methods: {
    pickOff (name, price, amount, link, key, index) {
      db.ref('products/' + key).set({
        productName: name,
        price: price,
        imgLink: link,
        amount: amount + 1
      })
      this.cost -= price
      this.itemCounter -= 1
      this.items.splice(index, 1)
    },
    checkOut () {
      this.items = []
      this.itemCounter = 0
      this.cost = 0
    },
    countItem (index) {
      this.itemCounter = index + 1
    },
    addToCart (name, price, key, amount, link) {
      this.items.push({
        productName: name,
        price: price,
        key: key,
        amount: amount - 1,
        imgLink: link
      })

      db.ref('products/' + key).set({
        productName: name,
        price: price,
        imgLink: link,
        amount: amount - 1
      })
      this.cost += price * 1
    },
    addProduct () {
      console.log(this.p)
      console.log(this.n)
      console.log(this.a)
      console.log(this.l)
      if (this.n !== null || this.p !== null || this.a !== null || this.l !== null) {
        if (this.n !== null) {
          if (this.p !== null && this.p >= 0) {
            if (this.a !== null && this.a > 0) {
              if (this.l !== null) {
                productRef.push({
                  productName: this.n,
                  price: this.p,
                  imgLink: this.l,
                  amount: this.a
                })
                this.n = null
                this.l = null
                this.p = null
                this.a = null
              }
            }
          }
        }
      }
    },
    addTodoFire () {
      todosRef.push({
        text: 'wsdsd',
        productName: 'Coca col',
        price: 125

      })
    },
    updateTodoFire (key, text) {
      // todosRef.child(key).child('text').set(text)
      db.ref('todos/' + key).set({
        text: text
      })
    },
    removeTodoFire (key) {
      // todosRef.child(key).remove()
      db.ref('todos/' + key).remove()
    },
    changeStateButton () {
      this.addProductButtonState = true
    },
    changeStatePassword () {
      this.onAdd = true
    },
    updateTodo (key, text) {
      // const index = this.todos.findIndex((todo) => todo.key === key)
      // if (index > -1) {
      //   this.todos[index].text = text
      // }
      const todo = this.todos.find((todo) => todo.key === key)
      if (todo) {
        todo.text = text
      }
    },
    removeTodo (key) {
      // const index = this.todos.findIndex(function (todo) {
      //   return todo.key === key
      // })
      const index = this.todos.findIndex((todo) => todo.key === key)
      if (index > -1) {
        this.todos.splice(index, 1)
      }
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: -5px;
}
.containers {
  width: 100%;
  padding-left: 25px;
  padding-right: 25px;
  margin-right: 0px;
  margin-left: 0px;
}
ul {
  margin: 0 auto;
  text-align: center;
}

li {
  display: inline-block;
  vertical-align: top;
  margin-bottom: 10px
}

.imgEdit {
  border-radius: 50%;
  width: 300px;
  height: 300px;
}

h1 {
  font-size: 50px;
  color: black;
  text-align: center;
  text-shadow: 0px 1px 0px #f2f2f2;
  border: 1px solid transparent;
}

h2 {
  font-size: 35px;
  color: black;
  text-align: center;
  margin: 0 0 35px 0;
  text-shadow: 0px 1px 0px #f2f2f2;
}

h5 {
  font-size: 20px;
  color: black;
  text-align: center;
  font-weight: bold;
  text-shadow: 0px 1px 0px #f2f2f2;
}

.borderlist {
  list-style-position: inside;
  border: 1px solid black;
}
</style>
