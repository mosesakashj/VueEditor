<template>
  <div style="height:600px">
    <!-- Top bar -->
    <div>
      <vue-file-toolbar-menu :content="menu"/>
      <v-card v-if="headerForm" outlined tile>
        <!-- <v-card-text class="pa-0"> Header </v-card-text> -->
        <v-card-text>
          <v-row>
            <v-col cols="1">Header</v-col>
            <v-col cols="3" class="pa-0">
              <v-checkbox class="caption pa-0" v-model="isHeaderEnabled" label="Show Header"></v-checkbox>
            </v-col>
            <v-col cols="3" class="pa-0">
              <v-select class="caption pt-0" :items="['For All', 'Diff First Page', 'Diff Odd & Even', 'Diff First, Odd & Even']" v-model="headerOptions"></v-select>
            </v-col>
            <v-col></v-col>
          </v-row>
        </v-card-text>
        <v-card-text class="py-0" v-if="isHeaderEnabled">
          <v-row>
            <v-col cols="3" v-if="['For All', 'Diff First Page', 'Diff Odd & Even', 'Diff First, Odd & Even'].includes(headerOptions)"><v-textarea v-model="init_Header[0]" label="First" class="caption"></v-textarea></v-col>
            <v-col cols="3" v-if="['Diff First Page', 'Diff Odd & Even', 'Diff First, Odd & Even'].includes(headerOptions)"><v-textarea v-model="init_Header[1]" label="Second" class="caption"></v-textarea></v-col>
            <v-col cols="3" v-if="['Diff First, Odd & Even'].includes(headerOptions)"><v-textarea v-model="init_Header[2]" label="Last" class="caption"></v-textarea></v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </div>
    <vue-document-editor class="editor pt-10" ref="editor" :content.sync="content" :overlay="overlay" :zoom="zoom" :page_format_mm="page_format_mm"
      :page_margins="page_margins" :display="display" />
    <!-- Document editor -->
    <v-bottom-sheet v-model="footerForm" inset>
      <v-card>
        <v-card-title class="pa-0"> Footer </v-card-title>
        <v-card-text class="py-0">
          <v-textarea v-model="init_Footer[0]" rows="1"></v-textarea>
        </v-card-text>
      </v-card>
    </v-bottom-sheet>
  </div>
</template>

<script>
import VueFileToolbarMenu from '@/components/vue-file-toolbar-menu.vue'
import VueDocumentEditor from '@/components/DocumentEditor.vue'
// import InvoiceTemplate from './InvoiceTemplate.vue';

export default {
  components: { VueDocumentEditor, VueFileToolbarMenu },

  data () {
    return {
      // This is where the pages content is stored and synced
      // content: [
      //   // Every item below produce a page break
      //   '<h1>Hello world!</h1><p>This is a rich-text editor built on top of <span contenteditable="false"><a href="https://vuejs.org/" target="_blank">Vue.js</a></span> using the native <span contenteditable="false"><a href="https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Editable_content" target="_blank"><i>contenteditable</i></a></span> browser implementation and some JavaScript trickery to spread content over paper-sized pages.</p><p>Built-in functionality includes:</p><ul><li>Using Vue.js components as interactive page templates (see next page)</li><li>Word-by-word page splitting with forward and backward propagation (<u>still experimental</u>)</li><li>Native Print compatible</li><li>Dynamic document format and margins in millimeters</li><li>Custom page overlays (headers, footers, page numbers)</li><li>Page breaks</li><li>Smart zoom and page display modes</li><li>Computes text style at caret position</li></ul><p>This library may be useful if you design an application that generate documents and you would let the user to modify them slightly before printing / saving, but with limited / interactive possibilities. It does not intend to replace a proper document editor with full functionality.<br>Make sure this project is suitable to your needs before using it.</p><p>This demo adds:</p><ul><li>The top bar (<span contenteditable="false"><a href="https://github.com/motla/vue-file-toolbar-menu" target="_blank">vue-file-toolbar-menu</a></span> component) and the functions associated with it</li><li>Rewritten history stack (undo/redo) compatible with native commands</li><li>Pinch and trackpad zooming</li></ul><p>Check out the <span contenteditable="false"><a href="https://github.com/motla/vue-document-editor/blob/master/src/Demo/Demo.vue" target="_blank">Demo.vue</a></span> file if you need to add these functionalities to your application.</p><p>The link below is an example of non-editable block set with <code>contenteditable="false"</code>:</p><p style="text-align:center" contenteditable="false"><a href="https://github.com/motla/vue-document-editor">View docs on Github</a>, you can\'t edit me.</p><p>But you can still edit this.</p>',
      //   // { template: InvoiceTemplate, props: { invoice_number: "AB38052985" } },
      //   '<br><br><h1>Headers / footers example</h1><br>Page numbers have been added on every page of this document.<br>Header and footer overlays will be added from page 3 to all subsequent ones.<br><br>Check out the <code>overlay</code> method of the <span contenteditable="false"><a href="https://github.com/motla/vue-document-editor/blob/master/src/Demo/Demo.vue" target="_blank">Demo.vue</a></span> file to customize this.',
      //   '<h1>«</h1><div style="width:80%; text-align:justify; margin:auto"><p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed non risus. Suspendisse lectus tortor, dignissim sit amet, adipiscing nec, ultricies sed, dolor. Cras elementum ultrices diam. Maecenas ligula massa, varius a, semper congue, euismod non, mi. Proin porttitor, orci nec nonummy molestie, enim est eleifend mi, non fermentum diam nisl sit amet erat. Duis semper. Duis arcu massa, scelerisque vitae, consequat in, pretium a, enim. Pellentesque congue. Ut in risus volutpat libero pharetra tempor. Cras vestibulum bibendum augue. Praesent egestas leo in pede. Praesent blandit odio eu enim. Pellentesque sed dui ut augue blandit sodales. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Aliquam nibh. Mauris ac mauris sed pede pellentesque fermentum. Maecenas adipiscing ante non diam sodales hendrerit.</p><p>Ut velit mauris, egestas sed, gravida nec, ornare ut, mi. Aenean ut orci vel massa suscipit pulvinar. Nulla sollicitudin. Fusce varius, ligula non tempus aliquam, nunc turpis ullamcorper nibh, in tempus sapien eros vitae ligula. Pellentesque rhoncus nunc et augue. Integer id felis. Curabitur aliquet pellentesque diam. Integer quis metus vitae elit lobortis egestas. Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Morbi vel erat non mauris convallis vehicula. Nulla et sapien. Integer tortor tellus, aliquam faucibus, convallis id, congue eu, quam. Mauris ullamcorper felis vitae erat. Proin feugiat, augue non elementum posuere, metus purus iaculis lectus, et tristique ligula justo vitae magna.</p><p>Aliquam convallis sollicitudin purus. Praesent aliquam, enim at fermentum mollis, ligula massa adipiscing nisl, ac euismod nibh nisl eu lectus. Fusce vulputate sem at sapien. Vivamus leo. Aliquam euismod libero eu enim. Nulla nec felis sed leo placerat imperdiet. Aenean suscipit nulla in justo. Suspendisse cursus rutrum augue. Nulla tincidunt tincidunt mi. Curabitur iaculis, lorem vel rhoncus faucibus, felis magna fermentum augue, et ultricies lacus lorem varius purus. Curabitur eu amet.</p><p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed non risus. Suspendisse lectus tortor, dignissim sit amet, adipiscing nec, ultricies sed, dolor. Cras elementum ultrices diam. Maecenas ligula massa, varius a, semper congue, euismod non, mi. Proin porttitor, orci nec nonummy molestie, enim est eleifend mi, non fermentum diam nisl sit amet erat. Duis semper. Duis arcu massa, scelerisque vitae, consequat in, pretium a, enim. Pellentesque congue. Ut in risus volutpat libero pharetra tempor. Cras vestibulum bibendum augue. Praesent egestas leo in pede. Praesent blandit odio eu enim. Pellentesque sed dui ut augue blandit sodales. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Aliquam nibh. Mauris ac mauris sed pede pellentesque fermentum. Maecenas adipiscing ante non diam sodales hendrerit.</p><p>Ut velit mauris, egestas sed, gravida nec, ornare ut, mi. Aenean ut orci vel massa suscipit pulvinar. Nulla sollicitudin. Fusce varius, ligula non tempus aliquam, nunc turpis ullamcorper nibh, in tempus sapien eros vitae ligula. Pellentesque rhoncus nunc et augue. Integer id felis. Curabitur aliquet pellentesque diam. Integer quis metus vitae elit lobortis egestas. Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Morbi vel erat non mauris convallis vehicula. Nulla et sapien. Integer tortor tellus, aliquam faucibus, convallis id, congue eu, quam. Mauris ullamcorper felis vitae erat. Proin feugiat, augue non elementum posuere, metus purus iaculis lectus, et tristique ligula justo vitae magna.</p><p>Aliquam convallis sollicitudin purus. Praesent aliquam, enim at fermentum mollis, ligula massa adipiscing nisl, ac euismod nibh nisl eu lectus. Fusce vulputate sem at sapien. Vivamus leo. Aliquam euismod libero eu enim. Nulla nec felis sed leo placerat imperdiet. Aenean suscipit nulla in justo. Suspendisse cursus rutrum augue. Nulla tincidunt tincidunt mi. Curabitur iaculis, lorem vel rhoncus faucibus, felis magna fermentum augue, et ultricies lacus lorem varius purus. Curabitur eu amet.</p></div><h1 style="text-align:right">»</h1>',
      //   '<h3 style="text-align:center">--- This is a page break. ---</h3>'
      // ],
      content: [],
      zoom: 1.0,
      zoom_min: 0.10,
      zoom_max: 5.0,
      page_format_mm: [210, 297],
      page_orientation: 'portrait',
      page_margins: '10mm 15mm',
      display: 'vertical', // ['grid', 'vertical', 'horizontal']
      mounted: false, // will be true after this component is mounted
      undo_count: -1, // contains the number of times user can undo (= current position in content_history)
      content_history: [], // contains the content states for undo/redo operations
      headerForm: false,
      footerForm: false,
      headerOptions: 'For All',
      isHeaderEnabled: true,
      init_Header: [ '<div style="position: absolute; left: 0; top: 0; right: 0; padding: 3mm 5mm; background: rgba(200, 220, 240, 0.5)"><strong>MYCOMPANY</strong> /// This is a custom header overlay</div>', '', '' ],
      init_Footer: [ '<div style="position: absolute; left: 10mm; right: 10mm; bottom: 5mm; text-align:center; font-size:10pt"> /// This is a custom footer Content</div>', '', '' ]
    }
  },

  created () {
    // Initialize gesture flags
    let start_zoom_gesture, start_dist_touch, start_zoom_touch = false

    // Manage ctrl+scroll zoom (or trackpad pinch)
    window.addEventListener('wheel', (e) => {
      if(e.ctrlKey){
        e.preventDefault()
        this.zoom = Math.min(Math.max(this.zoom - e.deltaY * 0.01, this.zoom_min), this.zoom_max)
      }
    }, { passive: false })

    // Manage trackpad pinch on Safari
    window.addEventListener('gesturestart', (e) => {
      e.preventDefault()
      start_zoom_gesture = this.zoom
    })
    window.addEventListener('gesturechange', (e) => {
      e.preventDefault()
      if(!start_zoom_touch) this.zoom = Math.min(Math.max(start_zoom_gesture * e.scale, this.zoom_min), this.zoom_max)
    })
    window.addEventListener('gestureend', () => {
      start_zoom_gesture = false
    })

    // Manage pinch to zoom for touch devices
    window.addEventListener('touchstart', (e) => {
      if (e.touches.length == 2) {
        e.preventDefault();
        start_dist_touch = Math.hypot(
          e.touches[0].pageX - e.touches[1].pageX,
          e.touches[0].pageY - e.touches[1].pageY
        );
        start_zoom_touch = this.zoom;
      }
    }, { passive: false });
    window.addEventListener('touchmove', (e) => {
      if (start_dist_touch && start_zoom_touch) {
        e.preventDefault();
        let zoom = start_zoom_touch * Math.hypot(
          e.touches[0].pageX - e.touches[1].pageX,
          e.touches[0].pageY - e.touches[1].pageY
        ) / start_dist_touch;
        this.zoom = Math.min(Math.max(zoom, this.zoom_min), this.zoom_max);
      }
    }, { passive: false });
    window.addEventListener('touchend', () => {
      start_dist_touch = false;
      start_zoom_touch = false;
    }, { passive: false });

    // Manage history undo/redo events
    const manage_undo_redo = (e) => {
      switch(e && e.inputType){
        case 'historyUndo': e.preventDefault(); e.stopPropagation(); this.undo(); break;
        case 'historyRedo': e.preventDefault(); e.stopPropagation(); this.redo(); break;
      }
    }
    window.addEventListener('beforeinput', manage_undo_redo)
    window.addEventListener('input', manage_undo_redo) // in case of beforeinput event is not implemented (Firefox)

    // If your component is susceptible to be destroyed, don't forget to
    // use window.removeEventListener in the Vue.js beforeDestroy handler
  },

  mounted () { this.mounted = true },

  computed: {
    // This is the menu content
    menu () {
      return [
        // Main commands
        { text: 'New', title: 'New', html: '<b>NEW</b>', click: () => { if(confirm('This will create an empty document. Are you sure?')){ this.content = ['']; this.resetContentHistory(); } } },
        { text: 'Print', title: 'Print', icon: 'mdi-printer', click: () => window.print() },
        { is: 'spacer' },
        { title: 'Undo', icon: 'mdi-undo', disabled: !this.can_undo, hotkey: this.isMacLike ? 'command+z' : 'ctrl+z', click: () => this.undo() },
        { title: 'Redo', icon: 'mdi-redo', disabled: !this.can_redo, hotkey: this.isMacLike ? 'shift+command+z' : 'ctrl+y', click: () => this.redo() },
        { is: 'spacer' },
        { icon: 'mdi-format-align-left', title: 'Align left', active: this.isLeftAligned, disabled: !this.current_text_style, hotkey: this.isMacLike ? 'shift+command+l' : 'ctrl+shift+l', click: () => document.execCommand('justifyLeft') },
        { icon: 'mdi-format-align-center', title: 'Align center', active: this.isCentered, disabled: !this.current_text_style, hotkey: this.isMacLike ? 'shift+command+e' : 'ctrl+shift+e', click: () => document.execCommand('justifyCenter') },
        { icon: 'mdi-format-align-right', title: 'Align right', active: this.isRightAligned, disabled: !this.current_text_style, hotkey: this.isMacLike ? 'shift+command+r' : 'ctrl+shift+r', click: () => document.execCommand('justifyRight') },
        { icon: 'mdi-format-align-justify', title: 'Justify content', active: this.isJustified, disabled: !this.current_text_style, hotkey: this.isMacLike ? 'shift+command+j' : 'ctrl+shift+j', click: () => document.execCommand('justifyFull') },
        { is: 'separator' },
        { icon: 'mdi-format-bold', title: 'Bold', active: this.isBold, disabled: !this.current_text_style, hotkey: this.isMacLike ? 'command+b' : 'ctrl+b', click: () => document.execCommand('bold') },
        { icon: 'mdi-format-italic', title: 'Italic', active: this.isItalic, disabled: !this.current_text_style, hotkey: this.isMacLike ? 'command+i' : 'ctrl+i', click: () => document.execCommand('italic') },
        { icon: 'mdi-format-underline', title: 'Underline', active: this.isUnderline, disabled: !this.current_text_style, hotkey: this.isMacLike ? 'command+u' : 'ctrl+u', click: () => document.execCommand('underline') },
        { icon: 'mdi-format-strikethrough', title: 'Strike through', active: this.isStrikeThrough, disabled: !this.current_text_style, click: () => document.execCommand('strikethrough') },
        { is: 'button-color', type: 'compact', menu_class: 'align-center', disabled: !this.current_text_style, color: this.curColor, update_color: (new_color) => document.execCommand('foreColor', false, new_color) },
        { is: 'separator' },
        { icon: 'mdi-format-indent-increase', title: 'Indent Increase', active: this.isNumberedList, disabled: !this.current_text_style, click: () => document.execCommand('indent', false, null) },
        { icon: 'mdi-format-indent-decrease', title: 'Indent Decrease', active: this.isNumberedList, disabled: !this.current_text_style, click: () => document.execCommand('outdent', false, null) },
        { icon: 'mdi-format-list-numbered', title: 'Numbered list', active: this.isNumberedList, disabled: !this.current_text_style, click: () => document.execCommand('insertOrderedList') },
        { icon: 'mdi-format-list-bulleted', title: 'Bulleted list', active: this.isBulletedList, disabled: !this.current_text_style, click: () => document.execCommand('insertUnorderedList') },
        { icon: 'mdi-format-header-1', title: 'Header 1', active: this.isH1, disabled: !this.current_text_style, click: () => document.execCommand('formatBlock', false, '<h1>') },
        { icon: 'mdi-format-header-2', title: 'Header 2', active: this.isH2, disabled: !this.current_text_style, click: () => document.execCommand('formatBlock', false, '<h2>') },
        { icon: 'mdi-format-header-3', title: 'Header 3', active: this.isH3, disabled: !this.current_text_style, click: () => document.execCommand('formatBlock', false, '<h3>') },
        { icon: 'mdi-format-clear', title: 'Clear format', disabled: !this.current_text_style, click () { document.execCommand('removeFormat'); document.execCommand('formatBlock', false, '<div>'); } },
        { icon: 'mdi-view-split-horizontal', title: 'Page break', disabled: !this.current_text_style, click: () => this.insertPageBreak() },
        { icon: 'mdi-page-layout-header', title: 'Page Header', click: () => this.headerForm = !this.headerForm },
        { icon: 'mdi-page-layout-footer', title: 'Page Footer', click: () => this.footerForm = !this.footerForm },
        
        { is: 'spacer' },

        { // Format menu
          text: this.current_format_name,
          title: 'Format',
          icon: 'mdi-crop-free',
          chevron: true,
          menu: this.formats.map(([text, w, h]) => {
            return {
              text,
              active: (this.page_format_mm[0] == w && this.page_format_mm[1] == h),
              click: () => this.page_format_mm = [w, h]
            }
          }),
          menu_width: 80,
          menu_height: 280
        },
        { // Margins menu
          text: this.current_margins_name,
          title: 'Margins',
          icon: 'mdi-select-all',
          chevron: true,
          menu: this.margins.map(([text, value]) => {
            return {
              text: text+' ('+value+')',
              active: (this.page_margins == value),
              click: () => { this.page_margins = value; }
            }
          }),
          menu_width: 200,
          menu_class: 'align-center'
        },
        { // Zoom menu
          text: Math.floor(this.zoom * 100) + '%',
          title: 'Zoom',
          icon: 'mdi-magnify',
          chevron: true,
          menu: [
            ['200%', 2.0],
            ['150%', 1.5],
            ['125%', 1.25],
            ['100%', 1.0],
            ['75%', 0.75],
            ['50%', 0.5],
            ['25%', 0.25]
          ].map(([text, zoom]) => {
            return {
              text,
              active: this.zoom == zoom,
              click: () => { this.zoom = zoom }
            }
          }),
          menu_width: 80,
          menu_height: 280,
          menu_class: "align-center"
        },
        { // Display mode menu
          title: "Display",
          icon: this.display == "horizontal" ? "mdi-view-column" : (this.display == "vertical" ? "mdi-view-stream" : "mdi-view-module"),
          chevron: true,
          menu: [{
            icon: 'mdi-view-module',
            text: 'grid',
            active: this.display == 'grid',
            click: () => this.display = 'grid'
          }, {
            icon: 'mdi-view-column',
            text: 'horizontal',
            active: this.display == 'horizontal',
            click: () =>  this.display = 'horizontal'
          }, {
            icon: 'mdi-view-stream',
            text: 'vertical',
            active: this.display == 'vertical',
            click: () =>  this.display = 'vertical'
          }],
          menu_width: 55,
          menu_class: 'align-right'
        },
        // Orientation Change
        {
          title: 'Orientation',
          icon: this.page_format_mm[0] < this.page_format_mm[1] ? 'mdi-crop-portrait' : 'mdi-crop-landscape',
          menu: [{
            icon: 'mdi-crop-portrait',
            text: 'Portait',
            active: this.page_format_mm[0] < this.page_format_mm[1],
            click: () => {
              if (this.page_format_mm[0] > this.page_format_mm[1]) this.page_format_mm = JSON.parse(JSON.stringify([this.page_format_mm[1], this.page_format_mm[0]]))
            }
          }, {
            icon: 'mdi-crop-landscape',
            text: 'Landscape',
            active: this.page_format_mm[0] > this.page_format_mm[1],
            click: () => {
              if (this.page_format_mm[0] < this.page_format_mm[1]) this.page_format_mm = JSON.parse(JSON.stringify([this.page_format_mm[1], this.page_format_mm[0]]))
            }
          }]
        }
      ]
    },

    // Formats management
    current_format_name () {
      const format = this.formats.find(([, width_mm, height_mm]) => (this.page_format_mm[0] == width_mm && this.page_format_mm[1] == height_mm))
      return format ? format[0] : (this.page_format_mm[0]+'mm x '+this.page_format_mm[1]+'mm')
    },
    formats: () => [
      ['A0', 841, 1189],
      ['A1', 594, 841],
      ['A2', 420, 594],
      ['A3', 297, 420],
      ['A4', 210, 297],
      ['A5', 148, 210],
      ['A6', 105, 148],
    ],

    // Margins management
    current_margins_name () {
      const margins = this.margins.find(([, margins]) => (this.page_margins === margins))
      return margins ? margins[0] : margins[1]
    },
    margins: () => [
      ['Medium', '20mm'],
      ['Small', '15mm'],
      ['Slim', '10mm 15mm'],
      ['Tiny', '5mm']
    ],

    // Current text style management
    current_text_style () { return this.mounted ? this.$refs.editor.current_text_style : false },
    isLeftAligned () { return ['start', 'left', '-moz-left'].includes(this.current_text_style.textAlign) },
    isRightAligned () { return ['end', 'right', '-moz-right'].includes(this.current_text_style.textAlign) },
    isCentered () { return ['center', '-moz-center'].includes(this.current_text_style.textAlign) },
    isJustified () { return ['justify', 'justify-all'].includes(this.current_text_style.textAlign) },
    isBold () {
      const fontWeight = this.current_text_style.fontWeight
      return fontWeight && (parseInt(fontWeight) > 400 || fontWeight.indexOf('bold') == 0)
    },
    isItalic () { return this.current_text_style.fontStyle == 'italic' },
    isUnderline () { // text-decoration is not overridden by children, so we query the parent stack
      const stack = this.current_text_style.textDecorationStack
      return stack && stack.some(d => (d.indexOf('underline') == 0))
    },
    isStrikeThrough () { // text-decoration is not overridden by children, so we query the parent stack
      const stack = this.current_text_style.textDecorationStack
      return stack && stack.some(d => (d.indexOf('line-through') == 0))
    },
    isNumberedList () { return this.current_text_style.isList && this.current_text_style.listStyleType == 'decimal' },
    isBulletedList () { return this.current_text_style.isList && ['disc', 'circle'].includes(this.current_text_style.listStyleType) },
    isH1 () { return this.current_text_style.headerLevel == 1 },
    isH2 () { return this.current_text_style.headerLevel == 2 },
    isH3 () { return this.current_text_style.headerLevel == 3 },
    curColor () { return this.current_text_style.color || 'transparent' },

    // Platform management
    isMacLike: () => /(Mac|iPhone|iPod|iPad)/i.test(navigator.platform),

    // Undo / redo flags
    can_undo () { return this.undo_count > 0 },
    can_redo () { return this.content_history.length - this.undo_count - 1 > 0 }
  },

  methods: {
    // Page overlays (headers, footers, page numbers)
    overlay (page, total) {
      // Add page numbers on each page
      let html = '<div style="position: absolute; bottom: 8mm; ' + ((page % 2) ? 'right' : 'left') + ': 10mm">Page ' + page + ' of ' + total + '</div>';
        switch (this.headerOptions) {
          case 'For All': {
            if(page >= 1) {
              html += this.init_Header[0]
              html += this.init_Footer[0]
            }
            break
          }
          case 'Diff First Page' : {
            if (page === 1) {
              html += this.init_Header[0]
              html += this.init_Footer[0]
            } else {
              html += this.init_Header[1]
              html += this.init_Footer[0]
            }
            break
          }
          case 'Diff Odd & Even' : {
            if (page % 2 === 0) {
              // Even Pages
              html += this.init_Header[1]
              html += this.init_Footer[0]
            } else {
              // Odd Pages
              html += this.init_Header[0]
              html += this.init_Footer[0]
            }
            break
          }
          case 'Diff First, Odd & Even' : {
            if (page === 1) {
              html += this.init_Header[0]
              html += this.init_Footer[0]
            } else if (page % 2 === 0) {
              // Even Pages
              html += this.init_Header[2]
              html += this.init_Footer[0]
            } else {
              // Odd Pages
              html += this.init_Header[1]
              html += this.init_Footer[0]
            }
            break
          }
        }
      // Add custom headers and footers from page 1
      return html
    },

    // Undo / redo functions examples
    undo () { if (this.can_undo) { this._mute_next_content_watcher = true; this.content = this.content_history[--this.undo_count] } },
    redo () { if (this.can_redo) { this._mute_next_content_watcher = true; this.content = this.content_history[++this.undo_count] } },
    resetContentHistory () { this.content_history = []; this.undo_count = -1 },

    // Insert page break function example
    async insertPageBreak () {
      // insert paragraph at caret position
      document.execCommand("insertParagraph")

      // insert a marker at caret position (start of the new paragraph)
      const marker = "###PB###" // must be regex compatible
      document.execCommand('insertText', false, marker)

      // wait for DOM update
      await this.$nextTick()

      // find the marker inside content items and split this content item in two items between the two paragraphs
      // only match root tags (p, div, h1, h2...) to avoid non-root tags like <li>
      const regexp = new RegExp('<(p|div|h\\d)( [^/>]+)*>(<[^/>]+>)*'+marker)
      for(let i = 0; i < this.content.length; i++) {
        const item = this.content[i]
        if(typeof item != 'string') continue
        const match = regexp.exec(item)
        if(match) {
          const tags_open = match[0].slice(0, -marker.length)
          let content_plus_tags_close = item.substr(match.index + match[0].length)
          // insert <br> to empty pages that would not be handled correctly by contenteditable
          if(content_plus_tags_close.indexOf('</') == 0) content_plus_tags_close = '<br>' + content_plus_tags_close
          this.content.splice(i, 1, item.substr(0, match.index), tags_open + content_plus_tags_close)
          return
        }
      }

      // if the code didn't return before, the split didn't work (e.g. inside a <li>). just remove the marker from the content
      for(let i = 0; i < this.content.length; i++) {
        const item = this.content[i]
        if(typeof item != 'string' || item.indexOf(marker) < 0) continue
        this.content.splice(i, 1, item.replace(marker, ''))
        break
      }
    }
  },

  watch: {
    content: {
      immediate: true,
      // Fill undo / redo history stack on user input
      handler (new_content) {
        if (!this._mute_next_content_watcher) { // only update the stack when content is changed by user input, not undo/redo commands
          this.content_history[++this.undo_count] = new_content
          this.content_history.length = this.undo_count + 1 // remove all redo items
        }
        this._mute_next_content_watcher = false
      }
    }
  }
}
</script>

<style>
html {
  height: 100%;
}
body {
  margin: 0;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: black;
  background: rgb(129, 130, 131);
}
::-webkit-scrollbar {
  width: 16px;
  height: 16px;
}
::-webkit-scrollbar-track, ::-webkit-scrollbar-corner {
  display: none;
}
::-webkit-scrollbar-thumb {
  background-color: rgba(0, 0, 0, 0.5);
  border: 5px solid transparent;
  border-radius: 16px;
  background-clip: content-box;
}
::-webkit-scrollbar-thumb:hover {
  background-color: rgba(0, 0, 0, 0.8);
}
</style>

<style scoped>
  .main {
    width: fit-content;
    min-width: 100%;
  }
  .bar {
    position: sticky;
    left: 0;
    top: 0;
    width: calc(100vw - 16px);
    z-index: 1000;
    background: rgba(248, 249, 250, 0.8);
    border-bottom: solid 1px rgb(248, 249, 250);
    backdrop-filter: blur(10px);
    --bar-button-active-color: #188038;
    --bar-button-open-color: #188038;
    --bar-button-active-bkg: #e6f4ea;
    --bar-button-open-bkg: #e6f4ea;
  }
</style>
