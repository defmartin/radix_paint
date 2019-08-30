<template>
  <div class="hello">
    <h1>{{ msg }}</h1>

    <!-- <div v-bind:key="mat.id" v-for="mat in this.matrix"> -->
    <!-- <div v-for="(mat, index) in this.matrix" v-bind:key="`fruit-${index}`">
      <span v-bind:key="m.id" v-for="m in mat">
          <Pixel v-bind:value="m" v-on:changePixel='flipColor'/>
        </span>
    </div> -->

    <span v-for="(mat, index) in sizeOfMatrix*sizeOfMatrix" v-bind:key="`fruit-${index}`">
        
        <span v-if="index % sizeOfMatrix == 0" ><br /></span>
        <Pixel :key="mat.id" v-bind:value="dataElem[index]" v-on:changePixel='flipColor'/>

    </span>
    
  </div>
</template> 


<script>
const uuidv4 = require("uuid/v4");  
import Pixel from "./Pixel.vue";

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  components:{
      Pixel
  },
  data() {
    return {
      matrix: [],
      myAccount: "",
      friendAccount: "",
      identityManager: "",
      sizeOfMatrix: 20,
      dataElem: []
    };
  },
  methods: {
    genBoard() { 

    for (var i = 0; i < this.sizeOfMatrix; i++) {
        for (var j = 0; j < this.sizeOfMatrix; j++) {
          this.dataElem.push({ id: uuidv4(), color: false, x:j, y:i});
        }
      }  

    },
    updateBoard(response) {
     
        const color = response["color"];
        const x = parseInt(response["x"]);
        const y = parseInt(response["y"]);

        for (var i = 0; i < this.dataElem.length; i++) {
            var elem = this.dataElem[i]

            if(elem.x == x && elem.y == y){
                this.dataElem.color = color
            }

        }  

    },
    flipColor(pixel){

        const color = !pixel.color;
        const x = parseInt(pixel.x);
        const y = parseInt(pixel.y);

        // this.matrix[y][x]["color"] = color;
        // this.$set(this.matrix, y, this.matrix[y])

        for (var i = 0; i < this.dataElem.length; i++) {

            if(this.dataElem[i].x == x && this.dataElem[i].y == y){
                this.dataElem[i].color = color
            }

        }  
    },
    createRadix() {
      const radixUniverse = require("radixdlt").radixUniverse;
      const LOCALHOST_SINGLENODE = require("radixdlt").RadixUniverse
        .LOCALHOST_SINGLENODE;
      const RadixIdentityManager = require("radixdlt").RadixIdentityManager;
      // const RadixAccount = require('radixdlt').RadixAccount
      const RadixTransactionBuilder = require("radixdlt")
        .RadixTransactionBuilder;

      radixUniverse.bootstrap(LOCALHOST_SINGLENODE);

      this.identityManager = new RadixIdentityManager();
      this.myIdentity = this.identityManager.generateSimpleIdentity();
      this.myAccount = this.myIdentity.account;
      this.myAccount.openNodeConnection();
      //   console.log("My address: ", this.myAccount.getAddress());

      this.friendIdentity = this.identityManager.generateSimpleIdentity();
      this.friendAccount = this.friendIdentity.account;
      this.friendAccount.openNodeConnection();
      //   console.log("Friend address: ", this.friendAccount.getAddress());

      this.friendAccount.dataSystem
        .getApplicationData("my-test-app")
        .subscribe(appData => {
            
          this.updateBoard(JSON.parse(appData.data.payload.data));
        });
    },
    sendTestAtoms() {
      
      const applicationId = "my-test-app";

      const RadixTransactionBuilder = require("radixdlt")
        .RadixTransactionBuilder;
      
      for (let index = 0; index < 2; index++) {

            const payload = JSON.stringify({
                color: true,
                x: Math.floor(Math.random() * this.sizeOfMatrix),
                y: Math.floor(Math.random() * this.sizeOfMatrix)
            });

            const transactionStatus3 =  RadixTransactionBuilder.createPayloadAtom(
                this.myAccount,
                [this.friendAccount],
                applicationId,
                payload
            ).signAndSubmit(this.myIdentity);

            transactionStatus3.subscribe({
                next: status => {
                console.log(status);
                // For a valid transaction, this will print, 'FINDING_NOD   E', 'GENERATING_POW', 'SIGNING', 'STORE', 'STORED'
                },
                complete: () => {
                console.log("Transaction complete");
                },
                error: error => {
                console.error("Error submitting transaction", error);
                }
            });
          
      }



    }
  },
  created() {
    this.genBoard(), this.createRadix(), this.sendTestAtoms();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
