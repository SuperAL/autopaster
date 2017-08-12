<template>
  <div id="wrapper">
    <!-- <img id="logo" src="~@/assets/logo.png" alt="electron-vue"> -->
    <main>
      <md-table v-once>
        <md-table-header>
          <md-table-row>
            <md-table-head>快捷键(Shortcut)</md-table-head>
            <md-table-head>输入(Input)</md-table-head>
          </md-table-row>
        </md-table-header>

        <md-table-body>
          <md-table-row v-for="(row, index) in 5" :key="index">
            <md-table-cell @dblclick.native="editShortcut">Dessert Name</md-table-cell>
            <md-table-cell>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
            tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
            quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
            consequat.</md-table-cell>
          </md-table-row>
        </md-table-body>
      </md-table>

      <!-- <div class="left-side">
        <span class="title">
          Welcome to your new project!
        </span>
        <system-information></system-information>
      </div> -->

      <!-- <div class="right-side">
        <div class="doc">
          <div class="title">Getting Started</div>
          <p>
            electron-vue comes packed with detailed documentation that covers everything from
            internal configurations, using the project structure, building your application,
            and so much more.
          </p>
          <button @click="open('https://simulatedgreg.gitbooks.io/electron-vue/content/')">Read the Docs</button><br><br>
        </div>
        <div class="doc">
          <div class="title alt">Other Documentation</div>
          <button class="alt" @click="open('https://electron.atom.io/docs/')">Electron</button>
          <button class="alt" @click="open('https://vuejs.org/v2/guide/')">Vue.js</button>
        </div>
      </div> -->
    </main>
    <md-button class="md-fab md-fab-bottom-right">
      <md-icon>add</md-icon>
    </md-button>
    <!-- <button type="button" class="circle fixed right bottom" @click="showModal">+</button>
    
    <input-modal :isShown="isModalShown" @close="hideModal"></input-modal> -->
  </div>
</template>

<script>
  import SystemInformation from './LandingPage/SystemInformation'
  import InputModal from './LandingPage/InputModal'
  import { remote } from 'electron'
  export default {
    name: 'landing-page',
    data(){
      return {
        isModalShown: false
      }
    },
    created() { 
      let globalShortcut = this.$electron.remote.globalShortcut,
      clipboard = this.$electron.clipboard;
      console.log(globalShortcut.isRegistered('j'));
      console.log(globalShortcut.isRegistered('q'));
      console.log('Ctrl+Alt+A',globalShortcut.isRegistered('Ctrl+Alt+A'));
      console.log(globalShortcut.isRegistered('ctrl+x'));
      var a = globalShortcut.register('ctrl+x', function() {
          console.log('ctrl+x is pressed');
          clipboard.writeText('Example String ctrl+x');
        })
      console.log(globalShortcut.isRegistered('q'));
      var b = globalShortcut.register('ctrl+w+1', function() {
          console.log('ctrl+w+1 is pressed');
          clipboard.writeText('Example String ctrl+w+1');
        })
      var c = globalShortcut.register('Ctrl+Alt+0', function() {
          console.log('Ctrl+Alt+0 is pressed');
          clipboard.writeText('Example String Ctrl+Alt+0');
        })
    },
    components: { SystemInformation, InputModal },
    methods: {
      open (link) {
        this.$electron.shell.openExternal(link)
      },
      showModal() {
        this.isModalShown = true;
      },
      hideModal() {
        this.isModalShown = false;
      },
      editShortcut() {
        console.log('editShortcut')
      }
    }
  }
</script>

<style>
  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');

  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }

  body { font-family: 'Source Sans Pro', sans-serif; }

  #wrapper {
    background:
      radial-gradient(
        ellipse at top left,
        rgba(255, 255, 255, 1) 40%,
        rgba(229, 229, 229, .9) 100%
      );
    height: 100vh;
    padding: 60px 80px;
    width: 100vw;
  }

  #logo {
    height: auto;
    margin-bottom: 20px;
    width: 420px;
  }

  /*main {
    display: flex;
    justify-content: space-between;
  }*/

  main > div { flex-basis: 50%; }

  .left-side {
    display: flex;
    flex-direction: column;
  }

  .welcome {
    color: #555;
    font-size: 23px;
    margin-bottom: 10px;
  }

  .title {
    color: #2c3e50;
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 6px;
  }

  .title.alt {
    font-size: 18px;
    margin-bottom: 10px;
  }

  .doc p {
    color: black;
    margin-bottom: 10px;
  }

  .doc button {
    font-size: .8em;
    cursor: pointer;
    outline: none;
    padding: 0.75em 2em;
    border-radius: 2em;
    display: inline-block;
    color: #fff;
    background-color: #4fc08d;
    transition: all 0.15s ease;
    box-sizing: border-box;
    border: 1px solid #4fc08d;
  }

  .doc button.alt {
    color: #42b983;
    background-color: transparent;
  }

  /*autopaster*/
  button.circle {
    font-size: 1.8em;
    cursor: pointer;
    outline: none;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    display: inline-block;
    color: #fff;
    background-color: #4fc08d;
    transition: all 0.15s ease;
    box-sizing: border-box;
    border: 1px solid #4fc08d;
  }
  .fixed {
    position: fixed;
  }
  .right {
    right: 50px;
  }
  .bottom {
    bottom: 50px;
  }
</style>
