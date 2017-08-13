<template>
  <div id="wrapper">
    <!-- <img id="logo" src="~@/assets/logo.png" alt="electron-vue"> -->
    <main>
      <md-table>
        <md-table-header>
          <md-table-row>
            <md-table-head>快捷键(Shortcut)</md-table-head>
            <md-table-head>输入(Input)</md-table-head>
          </md-table-row>
        </md-table-header>

        <md-table-body>
          <md-table-row v-for="(item, index) in list" :key="index" @dblclick.native="editShortcut">
            <md-table-cell >{{ item.shortcut }}</md-table-cell>
            <md-table-cell>{{ item.content }}</md-table-cell>
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
    <md-button class="md-fab md-fab-bottom-right" @click="openDialog('shortcutDialog')">
      <md-icon>add</md-icon>
    </md-button>
    <md-dialog ref="shortcutDialog">
      <md-dialog-title>新建</md-dialog-title>

      <md-dialog-content>
        <form>
          <div class="field-group">
            <md-input-container>
              <label>功能按键</label>
              <md-select multiple v-model="funcKeysSelected" @change="selectFuncKeys">
                <md-option v-for="(funcKey, index) in funcKeys"
                  :value="funcKey" :key="index" :disabled="
                  ((funcKeysSelected.indexOf(funcKey) == -1) && isFuncDisabled) 
                  || (funcKeysSelected.indexOf('Cmd') != -1 && (funcKey == 'CmdOrCtrl' || funcKey == 'Super') ) 
                  || (funcKeysSelected.indexOf('Ctrl') != -1 && (funcKey == 'CmdOrCtrl') ) 
                  || (funcKeysSelected.indexOf('CmdOrCtrl') != -1 && (funcKey == 'Cmd' || funcKey == 'Ctrl' || funcKey == 'Super'))
                  || (funcKeysSelected.indexOf('Super') != -1 && (funcKey == 'Cmd' || funcKey == 'CmdOrCtrl')) ">
                  {{ funcKey }}
                </md-option>
              </md-select>
            </md-input-container>
            <md-input-container>
              <label>数字</label>
              <md-select multiple v-model="numberSelected" >
                <md-option v-for="(number, index) in normalKeys['numbers']" :key="index"
                  :value="number" :disabled="(number !== numberSelected[0]) && isDisabled">
                  {{ number }}
                </md-option>
              </md-select>
            </md-input-container>
            <md-input-container>
              <label>字母</label>
              <md-select multiple v-model="letterSelected">
                <md-option v-for="(letter, index) in normalKeys['letters']" :key="index"
                  :value="letter" :disabled="(letter !== letterSelected[0]) && isDisabled">
                  {{ letter }}
                </md-option>
              </md-select>
            </md-input-container>
            <md-input-container>
              <label>F1~F12</label>
              <md-select multiple v-model="letterSelected">
                <md-option v-for="(letter, index) in normalKeys['letters']" :key="index"
                  :value="letter" :disabled="(letter !== letterSelected[0]) && isDisabled">
                  {{ letter }}
                </md-option>
              </md-select>
            </md-input-container>
            <md-input-container>
              <label>其他</label>
              <md-select multiple v-model="otherSelected">
                <md-option v-for="(other, index) in normalKeys['others']" :key="index"
                  :value="other" :disabled="(other !== otherSelected[0]) && isDisabled">
                  {{ other }}
                </md-option>
              </md-select>
            </md-input-container>
          </div>
          <md-button class="md-raised">{{ shortcut }}</md-button>
          <md-input-container>
            <label>内容</label>
            <md-textarea v-model="content"></md-textarea>
          </md-input-container>
        </form>
      </md-dialog-content>

      <md-dialog-actions>
        <md-button class="md-primary" @click="closeDialog('shortcutDialog')">取消</md-button>
        <md-button class="md-primary" :disabled="isCreateDisabled" @click="saveShortcut">创建</md-button>
      </md-dialog-actions>
    </md-dialog>

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
        // 可用的功能按键
        funcKeys: ['Cmd','Ctrl','CmdOrCtrl','Alt','Shift','Super'],
        normalKeys: {
          'numbers': ['0','1','2','3','4','5','6','7','8','9'],
          'letters': ['A','B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z'],
          'functionKeys': ['F1','F2','F3','F4','F5','F6','F7','F8','F9','F10','F11','F12'],
          'others': ['Plus', 'Space', 'Backspace', 'Delete', 'Insert', 'Return', 'Enter', 'Up', 'Down', 'Left', 'Right', 'Home', 'End', 'PageUp', 'PageDown', 'Esc', 'VolumeUp', 'VolumeDown', 'VolumeMute', 'MediaNextTrack', 'MediaPreviousTrack', 'MediaStop', 'MediaPlayPause']
        },
        list: [
          // {
          //   shortcut: '',
          //   selections: [[],[],[],[]],
          //   content: ''
          // }
        ],
        funcKeysSelected: [],
        numberSelected: [],
        functionKeySelected: [],
        letterSelected: [],
        otherSelected: [],
        content: '',
        isModalShown: false,
        movie: 'godfather',
            country: '',
            font: ''
      }
    },
     computed: {
      isFuncDisabled() {
        if(this.funcKeysSelected.length > 2) {
          return true;
        } else {
          return false;
        }
      },
      isDisabled() {
        if ((this.numberSelected.length > 0) || (this.letterSelected.length > 0) || (this.otherSelected.length > 0)) {
          return true;
        } else {
          return false;
        }
      },
      shortcut() {
        return [...this.funcKeysSelected,...this.numberSelected,...this.letterSelected,...this.otherSelected].join('+');
      },
      isCreateDisabled() {
        if (this.shortcut == "" || this.content == "") {
          return true;
        } else {
          return false;
        }
      }

  },
    created() { 
      let globalShortcut = this.$electron.remote.globalShortcut,
      clipboard = this.$electron.clipboard;
      // console.log(globalShortcut.isRegistered('j'));
      // console.log(globalShortcut.isRegistered('q'));
      // console.log('Ctrl+Alt+A',globalShortcut.isRegistered('Ctrl+Alt+A'));
      // console.log(globalShortcut.isRegistered('ctrl+x'));
      var a = globalShortcut.register('ctrl+alt+x+c', function() {
          console.log('ctrl+alt+x+c is pressed');
          clipboard.writeText('Example String ctrl+alt+x+c');
        })
      // console.log(globalShortcut.isRegistered('q'));
      var b = globalShortcut.register('ctrl+alt+shift+1', function() {
          console.log('ctrl+alt+shift+1 is pressed');
          clipboard.writeText('Example String ctrl+alt+shift+1');
        })
      var c = globalShortcut.register('0+Ctrl+Alt', function() {
          console.log('0+Ctrl+Alt is pressed');
          clipboard.writeText('Example String 0+Ctrl+Alt');
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
      openDialog(ref) {
        this.$refs[ref].open();
      },
      closeDialog(ref) {
        this.$refs[ref].close();
        this.resetDialog();
      },
      editShortcut() {
        console.log('editShortcut')
      },
      selectFuncKeys(value) {
        console.log(value)
      },
      saveShortcut() {
        let vm = this;
        console.log(`${vm.shortcut}, ${vm.content}`);
        if (vm.isCreateDisabled) { return; }

        vm.closeDialog('shortcutDialog');

        let globalShortcut = vm.$electron.remote.globalShortcut,
        clipboard = vm.$electron.clipboard;

        if (globalShortcut.isRegistered(vm.shortcut)) {
          globalShortcut.unregister(vm.shortcut);
        }

        let idx = vm.list.length;
        let temp = globalShortcut.register(vm.shortcut, function() {
          console.log(`${vm.list[idx].shortcut} is pressed`);
          clipboard.writeText(vm.list[idx].content);
        })
        if (!temp) {
          console.log('registration failed');
        }

        vm.list.push({
            shortcut: vm.shortcut,
            selections: [vm.funcKeysSelected,vm.numberSelected,vm.letterSelected,vm.otherSelected],
            content: vm.content
        })
        vm.resetDialog();
      },
      resetDialog() {
        let vm = this;
        vm.funcKeysSelected = [];
        vm.numberSelected = [];
        vm.letterSelected = [];
        vm.functionKeySelected = [];
        vm.otherSelected = [];
        vm.content = '';
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

  /*select*/
  .field-group {
    display: -ms-flexbox;
    display: flex;
}
  .md-input-container {
    width: 100%;
    min-height: 48px;
    margin: 4px 0 24px;
    padding-top: 16px;
    display: -ms-flexbox;
    display: flex;
    position: relative;
}
.md-input-container+.md-input-container {
    margin-left: 4px;
}
</style>
