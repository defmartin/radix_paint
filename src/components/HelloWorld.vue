<template>
  <div class="hello">
    <h1>{{ msg }}</h1>

    <div class="canvas">
      <div v-for="(mat, index) in dataElem" v-bind:key="`fruit-${index}`">
        <Pixel :key="mat.id" v-bind:value="dataElem[index]" v-on:changePixel="flipColor" />
      </div>

    </div>
    <!-- end .canvas -->
  </div>
</template> 


<script>
var _ = require("lodash");
const uuidv4 = require("uuid/v4");
import Pixel from "./Pixel.vue";

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  components: {
    Pixel
  },
  data() {
    return {
      myAccount: "",
      friendAccount: "",
      identityManager: "",
      sizeOfMatrix: 60,
      dataElem: []
    };
  },
  methods: {
    genBoard() {
      for (var i = 0; i < this.sizeOfMatrix; i++) {
        for (var j = 0; j < this.sizeOfMatrix; j++) {
          this.dataElem.push({ id: uuidv4(), color: false, x: j, y: i });
        }
      }
    },
    updateBoard(response) {
      const x = parseInt(response["x"]);
      const y = parseInt(response["y"]);
      let elem = _.find(this.dataElem, { x: x, y: y });
      elem.color = response["color"];
    },
    flipColor(pixel) {
      let elem = _.find(this.dataElem, { x: pixel.x, y: pixel.y });
      elem.color = !elem.color;
    //   this.sendAtom(pixel.x, pixel.y, elem.color);
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

        const transactionStatus3 = RadixTransactionBuilder.createPayloadAtom(
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
    },
    sendAtom(x, y, color) {
      const applicationId = "my-test-app";

      const RadixTransactionBuilder = require("radixdlt")
        .RadixTransactionBuilder;

      const payload = JSON.stringify({
        color: color,
        x: x,
        y: y
      });

      const transactionStatus3 = RadixTransactionBuilder.createPayloadAtom(
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
  },
  created() {
    this.genBoard(), this.createRadix();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
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
.canvas {
  /* Perfectly square */
  width: 600px;
  height: 600px;
}

.pixel {
  /* We'll need 256 total pixels in our HTML */
  width: 10px;
  height: 10px;
  float: left;
}
</style>
