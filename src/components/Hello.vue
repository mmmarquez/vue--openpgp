<template>
  <div class="hello">
    <!-- <h1>{{ msg }}</h1>
    <h2>Essential Links</h2>
    <ul>
      <li><a href="https://vuejs.org" target="_blank">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank">Twitter</a></li>
      <br>
      <li><a href="http://vuejs-templates.github.io/webpack/" target="_blank">Docs for This Template</a></li>
    </ul>
    <h2>Ecosystem</h2>
    <ul>
      <li><a href="http://router.vuejs.org/" target="_blank">vue-router</a></li>
      <li><a href="http://vuex.vuejs.org/" target="_blank">vuex</a></li>
      <li><a href="http://vue-loader.vuejs.org/" target="_blank">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank">awesome-vue</a></li>
    </ul> -->
    <form-wizard title="PGP Encryption" subtitle="openPGP.js + Vue.js Case Study" color="#2196F3" @on-loading="setLoading" @on-validate="handleValidation">
      <tab-content title="Generate PGP Keys" :before-change="validateAsync">
        <div class="container is-fullhd">
          <section class="hero">
            <div class="hero-body">
              <div class="container">
                <h1 class="title">
                  Generate a PGP Key
                </h1>
                <h2 class="subtitle">
                  Enter your name, email and a <strong>very secure</strong> passphrase
                </h2>
              </div>
            </div>
          </section>
          <div class="form--container">

            <div v-if="keyStatus" class="loader--container">
              <div class="loader"></div>
            </div>
            <div class="form--inner">
              <div class="field">
                <label class="label">Name</label>
                <div class="control">
                  <input class="input" type="text" placeholder="Name..." v-model="userName">
                </div>
              </div>
              <div class="field">
                <label class="label">Email</label>
                <div class="control">
                  <input class="input" type="email" placeholder="Email..." v-model="userEmail">
                </div>
              </div>
              <div class="field">
                <label class="label">Passphrase</label>
                <div class="control">
                  <input class="input" type="text" placeholder="Passphrase..." v-model="userPass">
                </div>
              </div>
              <!-- <div class="field is-grouped">
                <div class="control">
                  <button class="button is-primary" @click.prevent="generateKeys()">Generate</button>
                </div>
                <div class="control">
                  <button class="button is-link">Cancel</button>
                </div>
              </div> -->
            </div>


          </div>
        </div>
      </tab-content>
      <tab-content title="Encrypt Message">
        <div class="container is-fullhd">
          <section class="hero">
            <div class="hero-body">
              <div class="container">
                <h1 class="title">
                  Encrypt Message
                </h1>
                <h2 class="subtitle">
                  Create and <strong>encrypt</strong> your message.
                </h2>
              </div>
            </div>
          </section>
          <div class="form--container">
            <div v-if="encryptStatus" class="loader--container">
              <div class="loader"></div>
            </div>
            <div class="field">
              <label class="label">Message</label>
              <div class="control">
                <textarea class="textarea" placeholder="Textarea" v-model="textData"></textarea>
              </div>
            </div>
            <div class="field">
              <label class="label">Private Key</label>
              <div class="control">
                <textarea class="textarea" placeholder="Textarea" :value="privateKey"></textarea>
              </div>
            </div>
            <div class="field">
              <label class="label">Public Key</label>
              <div class="control">
                <textarea class="textarea" placeholder="Textarea" v-model="publicKey"></textarea>
              </div>
            </div>
            <div class="field">
              <label class="label">Passphrase</label>
              <div class="control">
                <input class="input" type="text" placeholder="Text input" v-model="userPass">
              </div>
            </div>
            <!-- <div class="field is-grouped">
              <div class="control">
                <button class="button is-primary" @click.prevent="encryptMessage()">Encrypt!</button>
              </div>
              <div class="control">
                <button class="button is-link">Cancel</button>
              </div>
            </div> -->
          </div>
        </div>
       </tab-content>
       <tab-content title="Decrypt Message">
         <div class="container is-fullhd">
           <section class="hero">
             <div class="hero-body">
               <div class="container">
                 <h1 class="title">
                   Decrypt Message
                 </h1>
                 <h2 class="subtitle">
                   Decrypt your very <strong>secure</strong> message.
                 </h2>
               </div>
             </div>
           </section>
           <div class="form--container">
             <div v-if="decryptStatus" class="loader--container">
               <div class="loader"></div>
             </div>
             <div class="decrypted--msg" v-if="decryptedMessage">
               <div class="message" v-html="decryptedMessage"></div>
             </div>
             <div class="form--inner" v-if="!decryptedMessage">
               <div class="field">
                 <label class="label">Private Key</label>
                 <div class="control">
                   <textarea class="textarea" placeholder="Private Key..." :value="privateKey"></textarea>
                 </div>
               </div>
               <div class="field">
                 <label class="label">Public Key</label>
                 <div class="control">
                   <textarea class="textarea" placeholder="Public Key..." v-model="publicKey"></textarea>
                 </div>
               </div>
               <div class="field">
                 <label class="label">Passphrase</label>
                 <div class="control">
                   <input class="input" type="text" placeholder="Text input" v-model="userPass">
                 </div>
               </div>
               <!-- <div class="field is-grouped">
                 <div class="control">
                   <button class="button is-primary" @click.prevent="decryptMessage()">Decrypt!</button>
                 </div>
                 <div class="control">
                   <button class="button is-link">Cancel</button>
                 </div>
               </div> -->
             </div>
           </div>
         </div>
       </tab-content>

       <template slot="footer" scope="props">
        <div class="wizard-footer-left">
            <wizard-button  v-if="props.activeTabIndex > 0 && !props.isLastStep" :style="props.fillButtonStyle">Previous</wizard-button>
         </div>
         <div class="wizard-footer-right">
           <wizard-button v-if="!props.isLastStep && props.activeTabIndex === 0" @click.native="props.nextTab()" class="wizard-footer-right" :style="props.fillButtonStyle">Generate</wizard-button>
           <wizard-button v-if="!props.isLastStep && props.activeTabIndex === 1" @click.native="props.nextTab(); encryptMessage()" class="wizard-footer-right" :style="props.fillButtonStyle">Encrypt</wizard-button>
           <wizard-button v-if="props.isLastStep" @click.native="decryptMessage()" class="wizard-footer-right" :style="props.fillButtonStyle">Decrypt</wizard-button>
         </div>
       </template>


    </form-wizard>

  </div>
</template>

<script>
var openpgp = require('openpgp'); // use as CommonJS, AMD, ES6 module or via window.openpgp
// openpgp.initWorker({ path: '../src/utilities/openpgp.worker.js' }); // set the relative web worker path
openpgp.config.aead_protect = true; // activate fast AES-GCM mode (not yet OpenPGP standard)

export default {
  name: 'hello',
  data() {
    return {
      msg: 'Welcome to Your Vue.js App',
      userName: '',
      userEmail: '',
      userPass: '',
      privateKey: '',
      publicKey: '',
      keyStatus: false,
      textData: '',
      encryptStatus: false,
      decryptStatus: false,
      decryptedMessage: '',
      encryptedMessage: '',
      privateObject: '',
      loadingWizard: false
    };
  },
  methods: {
    setLoading: function(value) {
      this.loadingWizard = value;
    },
    handleValidation: function(isValid, tabIndex) {
      console.log('Tab: ' + tabIndex + ' valid: ' + isValid);
    },
    validateAsync() {
      return new Promise((resolve, reject) => {
        resolve(this.generateKeys());
      });
    },
    generateKeys() {
      console.log('hmm');
      var options = {
        userIds: [{ name: this.userName, email: this.userEmail }], // multiple user IDs
        numBits: 4096, // RSA key size
        passphrase: this.userPass // protects the private key
      };
      this.keyStatus = true;
      return openpgp.generateKey(options).then(key => {
        console.log(key);
        var privkey = key.privateKeyArmored; // '-----BEGIN PGP PRIVATE KEY BLOCK ... '
        var pubkey = key.publicKeyArmored; // '-----BEGIN PGP PUBLIC KEY BLOCK ... '
        this.privateKey = privkey;
        this.publicKey = pubkey;
        this.keyStatus = false;
        return true;
      });
    },
    decryptMessage() {
      var options;

      options = {
        message: openpgp.message.readArmored(this.encryptedMessage), // parse armored message
        publicKeys: openpgp.key.readArmored(this.publicKey).keys, // for verification (optional)
        privateKey: this.privateObject // for decryption
      };

      openpgp.decrypt(options).then(plaintext => {
        this.decryptedMessage = plaintext.data;
        return plaintext.data; // 'Hello, World!'
      });
    },
    encryptMessage() {
      var options, encrypted;
      var pubkey = this.publicKey;
      var privkey = this.privateKey;
      var passphrase = this.userPass;
      this.encryptStatus = true;

      var privKeyObj = openpgp.key.readArmored(privkey).keys[0];
      privKeyObj.decrypt(passphrase);
      this.privateObject = privKeyObj;

      options = {
        data: this.textData, // input as String (or Uint8Array)
        publicKeys: openpgp.key.readArmored(pubkey).keys, // for encryption
        privateKeys: privKeyObj // for signing (optional)
      };

      openpgp.encrypt(options).then(ciphertext => {
        this.encryptStatus = false;
        encrypted = ciphertext.data; // '-----BEGIN PGP MESSAGE ... END PGP MESSAGE-----'
        this.encryptedMessage = encrypted;
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

.form--container {
  max-width: 600px;
  margin: 0 auto;
  position: relative;
}
h1, h2 {
  font-weight: normal;
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


.loader--container {
  position: absolute;
  opacity: 0.8;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  height: 100%;
  width: 100%;
  z-index: 1;
  background: white;
}


.loader {
  font-size: 10px;
  margin: 50px auto;
  text-indent: -9999em;
  width: 11em;
  height: 11em;
  border-radius: 50%;
  background: white;
  /*background: linear-gradient(to right, #2196F3 10%, rgba(33, 150, 243, 0) 42%);*/
  position: relative;
  -webkit-animation: load3 1.4s infinite linear;
  animation: load3 1.4s infinite linear;
  -webkit-transform: translateZ(0);
  -ms-transform: translateZ(0);
  transform: translateZ(0);
}
.loader:before {
  width: 50%;
  height: 50%;
  background: #2196F3;
  border-radius: 100% 0 0 0;
  position: absolute;
  top: 0;
  left: 0;
  content: '';
}
.loader:after {
  background: white;
  width: 75%;
  height: 75%;
  border-radius: 50%;
  content: '';
  margin: auto;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
}
@-webkit-keyframes load3 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
@keyframes load3 {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}


.decrypted--msg {
  padding: 20px;
}

.message {
  padding: 20px;
  background: rgba(255,0,0,0.5);
}

.label {
  text-align: left;
}

.field {
  max-width: 320px;
  margin: 0 auto;
}

.wizard-card-footer {
    max-width: 600px;
    margin: 0 auto;
    margin-top: 4%;
}



</style>
