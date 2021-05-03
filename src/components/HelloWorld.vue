<template>
  <div>
    <v-row>
        <v-col style="border: 1px solid grey" class="py-0">
          <v-layout row my-1 px-1 justify-space-between>
            <v-flex>
              <v-select outlined dense :items="font_Size" label="Select font" v-model="font.fontSize" @change="getSelection();insertHtml('FONT_SIZE')" class="pa-0"></v-select>
            </v-flex>
            <v-flex>
              <v-select outlined dense :items="dropdown_font" label="Select font" v-model="font.selectedFont" @change="format('fontName', font.selectedFont)" class="pa-0"></v-select>
            </v-flex>
             <!-- <v-flex lg1 md1>
              <v-select outlined dense :items="dropdown_edit" v-model="font.fontSize" @change="format('fontSize', font.fontSize)" label="Font Size" hide-details class="pa-0"></v-select>
            </v-flex> -->
            <v-flex :class="$vuetify.breakpoint.mdAndUp ? 'text-center' : 'text-center'">
              <v-btn-toggle dense group multiple>
                <v-btn text @click="format('bold')"><v-icon>mdi-format-bold</v-icon></v-btn>
                <v-btn text @click="format('italic')"><v-icon>mdi-format-italic</v-icon></v-btn>
                <v-btn text><v-icon>mdi-format-underline</v-icon></v-btn>
              </v-btn-toggle>
            </v-flex>
            <v-flex :class="$vuetify.breakpoint.mdAndUp ? 'text-center' : 'text-center'">
              <v-btn-toggle dense group>
                <v-btn text @click="format('justifyLeft')"><v-icon>mdi-format-align-left</v-icon></v-btn>
                <v-btn text @click="format('justifyCenter')"><v-icon>mdi-format-align-center</v-icon></v-btn>
                <v-btn text @click="format('justifyRight')"><v-icon>mdi-format-align-right</v-icon></v-btn>
                <v-btn text @click="format('justifyFull')"><v-icon>mdi-format-align-justify</v-icon></v-btn>
              </v-btn-toggle>
            </v-flex>
            <v-flex :class="$vuetify.breakpoint.mdAndUp ? 'text-center' : 'text-center'">
              <v-btn-toggle dense group multiple>
                <input class="color-apply" type="color" @change="format('foreColor', selectedColor)" v-model="selectedColor">
                <v-btn text><v-icon>mdi-format-color-fill</v-icon></v-btn>
              </v-btn-toggle>
            </v-flex>
            <v-flex :class="$vuetify.breakpoint.mdAndUp ? 'text-center' : 'text-center'">
              <v-btn-toggle dense group>
                <v-btn text @click="format('insertUnorderedList')"><v-icon>mdi-format-list-bulleted</v-icon></v-btn>
                <v-btn text @click="format('insertOrderedList')"><v-icon>mdi-format-list-numbered</v-icon></v-btn>
                <v-btn text @click="openform('HREF')"><v-icon>mdi-link</v-icon></v-btn>
                <v-btn text @click="openform('HTML')"><v-icon>mdi-xml</v-icon></v-btn>
                <v-btn text @click="openform('IMAGE')"><v-icon>mdi-image-outline</v-icon></v-btn>
                <v-btn text @click="openform('VIDEO')"><v-icon>mdi-video</v-icon></v-btn>
                <v-btn text @click="show($event);openform('TABLE')"><v-icon>mdi-table</v-icon></v-btn>
                <v-btn text @click="insertHtml('DIVIDER')"><v-icon>mdi-minus</v-icon></v-btn>
                  <v-menu v-model="menu" absolute :position-x="x" :position-y="y">
                    <v-list>
                      <v-list-item >
                        <v-list-item-title>asdasd</v-list-item-title>
                      </v-list-item>
                    </v-list>
                  </v-menu>
              </v-btn-toggle>
            </v-flex>
            <v-flex lg2 md3 :class="$vuetify.breakpoint.mdAndUp ? 'text-center' : 'text-center'">
              <v-btn-toggle dense group>
                <!-- <v-btn text><v-icon >mdi-format-font-size-decrease</v-icon></v-btn>
                <v-btn text><v-icon>mdi-format-font-size-increase</v-icon></v-btn> -->
                <v-btn text @click="superscript = !superscript; superscript ? format('superscript') : data.focus()"><v-icon>mdi-format-superscript</v-icon></v-btn>
                <v-btn text @click="subscript = !subscript; subscript ? format('subscript') : format('bold')"><v-icon>mdi-format-subscript</v-icon></v-btn>
              </v-btn-toggle>
            </v-flex>
            <v-flex :class="$vuetify.breakpoint.mdAndUp ? '' : 'text-center'">
              <v-btn-toggle dense group>
                <v-btn text @click="format('undo')"><v-icon>mdi-undo</v-icon></v-btn>
                <v-btn text @click="format('redo')"><v-icon>mdi-redo</v-icon></v-btn>
              </v-btn-toggle>
              <v-btn-toggle dense group>
                <v-btn text  @click="format('copy')"><v-icon>mdi-content-copy</v-icon></v-btn>
                <v-btn text @click="format('cut')"><v-icon >mdi-content-cut</v-icon></v-btn>
                <v-btn text  @click="format('paste')"><v-icon>mdi-content-paste</v-icon></v-btn>
              </v-btn-toggle>
            </v-flex>
          </v-layout>
        </v-col>
      </v-row>
      <v-row>
        <iframe id="doc" class="pt-5" width="100%"></iframe>
      </v-row>
    <v-row justify="center">
    <v-dialog v-model="dialog" persistent max-width="400">
      <v-card>
        <v-card-title class="headline"> Add {{injectionContentType}} </v-card-title>
        <v-card-text>
          <v-form ref="addMeta">
            <v-textarea label="HTML Content" v-model="html" :rules="htmlRules" v-if="injectionContentType === 'HTML'"></v-textarea>
            <v-text-field v-model="url" :rules="urlRules" label="Link" v-if="injectionContentType === 'HREF'"></v-text-field>
            <v-text-field v-model="video.link" :rules="urlRules" label="Link" v-if="injectionContentType === 'VIDEO'"></v-text-field>
            <v-row v-if="injectionContentType === 'IMAGE'">
              <v-text-field v-model="file.height" label="Height" class="mx-2"></v-text-field>
              <v-text-field v-model="file.width" label="Width" class="mx-2"></v-text-field>
              <v-text-field v-model="file.msg" label="Alternate" class="mx-2"></v-text-field>
              <v-select :items="['left', 'right', 'center']" v-model="file.align" label="Posittion" class="mx-2"></v-select>
              <v-file-input label="Image" prepend-icon="mdi-camera" v-model="file.image" @change="createBase64ImageFormat(file.image)" class="mx-2"></v-file-input>
              <v-img v-if="file.converted" :src="file.converted" width="200" height="200" contain/>
            </v-row>
            <v-row v-if="injectionContentType === 'TABLE'">
              <v-text-field v-model="table.rows" label="Row Count" type="Number" class="mx-2"></v-text-field>
              <v-text-field v-model="table.cols" label="Column Count" type="Number" class="mx-2"></v-text-field>
            </v-row>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" text @click="dialog = false"> cancel </v-btn>
          <v-btn color="green darken-1" text @click="$refs.addMeta.validate() ? insertHtml (injectionContentType) : false"> Save </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
  </div>
</template>

<script>
export default {
  data () {
    return {
      data: null,
      renderArea: null,
      dropdown_font: ['Arial', 'Calibri', 'Courier', 'Verdana', 'Times New Roman', 'Sans serif'],
      font_Size: [ 'h1', 'h2', 'h3', 'h4', 'h5', 'h6'],
      dropdown_edit: ['8', '9', '10', '11', '12', '14', '16', '18', '20', '24', '28', '36', '48', '72'],
      font: {},
      content: null,
      injectionContentType: null,
      colorpicker: false,
      selectedColor: null,
      selectedText: null,
      superscript: false,
      subscript: false,
      urlRules: [
        v => !!v || 'HREF is required',
        v => /^(?:(?:https?|ftp):\/\/)(?:\S+(?::\S*)?@)?(?:(?!(?:10|127)(?:\.\d{1,3}){3})(?!(?:169\.254|192\.168)(?:\.\d{1,3}){2})(?!172\.(?:1[6-9]|2\d|3[0-1])(?:\.\d{1,3}){2})(?:[1-9]\d?|1\d\d|2[01]\d|22[0-3])(?:\.(?:1?\d{1,2}|2[0-4]\d|25[0-5])){2}(?:\.(?:[1-9]\d?|1\d\d|2[0-4]\d|25[0-4]))|(?:(?:[a-z\u00a1-\uffff0-9]-*)*[a-z\u00a1-\uffff0-9]+)(?:\.(?:[a-z\u00a1-\uffff0-9]-*)*[a-z\u00a1-\uffff0-9]+)*(?:\.(?:[a-z\u00a1-\uffff]{2,}))\.?)(?::\d{2,5})?(?:[/?#]\S*)?$/i.test(v) || 'HREF must be valid'
      ],
      dialog: false,
      url: null,
      html: null,
      file: {},
      video: {},
      table: {},
      htmlRules: [v => !!v || 'HTML is required'],
      menu: false,
      x: 0,
      y: 0,
      imgId: 0
    }
  },
  methods: {
    openform (Type) {
      this.getSelection()
      this.injectionContentType = Type
      this.dialog = true
    },
    show (e) {
      e.preventDefault()
      this.x = e.clientX
      this.y = e.clientY
      this.menu = true
    },
    format (command, value) {
      this.data.focus()
      this.renderArea.execCommand(command, true, value)
    },
    getSelection () {
      this.selectedText = this.renderArea.getSelection()
    },
    getRange () {
      let sel = this.getSelection()
      let range
      if (sel && sel.rangeCount !== 0) range = sel.getRangeAt(0)
      return range
    },
    setRange (range) {
      let sel = this.getSelection()
      if (sel) {
        sel.removeAllRanges()
        sel.addRange(range)
      }
    },
    removeRange () {
      let sel = this.getSelection()
      sel && sel.removeAllRanges()
    },
    rangeValid () {
      let range = this.getRange()
      return (range && !range.collapsed)
    },
    insertHtml (type) {
      this.dialog = false
      switch (type) {
        case 'HTML' : this.renderArea.execCommand('insertHTML', false, this.html)
          break
        case 'IMAGE' : {
          this.imgId += 1
          const html = `<figure><img  id='${this.file.msg}' id='${this.imgId}' src='${this.file.converted}' width="${this.file.width}" height="${this.file.height}" alt="${this.file.msg}" /><figcaption contenteditable='false'>Fig.1 - Trulli, Puglia, Italy.</figcaption></figure>`
          this.renderArea.execCommand('insertHTML', false, html)
          break
        }
        case 'VIDEO' : {
          const link = `https://www.youtube.com/embed/${this.youtube_parser(this.video.link)}`
          const html = `<iframe src='${link}' width="500" height="500" >`
          this.renderArea.execCommand('insertHTML', false, html)
          break
        }
        case 'HREF' : {
          const html = `<a href='${this.url}' style="float:right" " target="_blank">${this.selectedText}</a>`
          this.renderArea.execCommand('insertHTML', false, html)
          this.selectedText = null
          break
        }
        case 'FONT_SIZE' : {
          const html = `<${this.font.fontSize}>${this.selectedText}</${this.font.fontSize}>`
          this.renderArea.execCommand('insertHTML', false, html)
          this.selectedText = null
          break
        }
        case 'DIVIDER' : {
          const html = '<br><hr></hr>'
          this.renderArea.execCommand('insertHTML', false, html)
          this.selectedText = null
          break
        }
        case 'TABLE' : {
          let html = '<table border="1" width="100%">'
          for (let i = 0; i < this.table.rows; i++) {
            html += '<tr>'
            for (let j = 0; j < this.table.cols; j++) {
              html += '<td>1</td>'
            }
            html += '</tr>'
          }
          html += '</table>'
          this.renderArea.execCommand('insertHTML', false, html)
          this.selectedText = null
          break
        }
      }
    },
    createBase64ImageFormat (file) {
      if (file) {
        var reader = new FileReader()
        reader.onload = (e) => { this.file.converted = e.target.result; console.log('coverted successfully') }
        reader.readAsDataURL(file)
      }
    },
    youtube_parser (url) {
      var regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#&?]*).*/
      var match = url.match(regExp)
      return (match && match[7].length === 11) ? match[7] : false
    },
    imageSelcted () {
      console.log('Image selected')
    }
  },
  mounted () {
    this.data = document.getElementById('doc')
    this.renderArea = this.data.contentWindow.document
    this.renderArea.contentEditable = true
    // this.renderArea.enableObjectResizing = true
    this.renderArea.designMode = 'on'
    this.data.focus()
    window.addEventListner('click', (event) => console.log(event))
  }
}
</script>

<style>
  #doc {
    height: 500px;
    min-width: 500px;
    outline: none;
     overflow-y: auto;
  }
  .color-apply {
    margin-top : 12px;
    width: 22px;
    height: 22px;
  }
  .mystyle {
    border: solid;
  }
</style>
