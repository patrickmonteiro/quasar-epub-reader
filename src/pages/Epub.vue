<template>
  <q-page padding>
    <div class="row" v-if="show">
      <div class="col-12">
        <q-file
          @input="uploadFile"
          outlined
          v-model="newEpub"
          label="Enter new Epub File"
          dense
          accept=".epub, .mobi"
        >
          <template v-slot:prepend>
            <q-icon name="attach_file" />
          </template>
        </q-file>
      </div>
      <div class="col-12 q-pt-sm">
        <q-select
          filled
          v-model="chapter"
          :options="toc"
          label="Sumário"
          dense
          option-value="href"
          option-label="label"
          option-disable="inactive"
          emit-value
          map-options
          @input="goToExcerpt"
        />
      </div>
    </div>
    <div
      class="col-12"
      style="position: absolute;z-index: 9999 !important; height: 67vh; width: 96%"
      v-touch-swipe.mouse.left.right="touchMoveToPage"
    >
    </div>
    <div id="epub-render"></div>
    <div class="row q-gutter-md q-px-sm" v-if="show">
      <q-btn
        @click="previousPage()"
        icon="arrow_left"
        label="Anterior"
        class="col"
        dense
      />
      <q-btn
        @click="nextPage()"
        icon-right="arrow_right"
        label="Próximo"
        class="col"
        dense
      />
    </div>
    <q-inner-loading :showing="!show">
      <q-spinner-gears size="50px" color="primary" />
    </q-inner-loading>
  </q-page>
</template>

<script>
import ePub from 'epubjs'
const URL_EPUB = 'https://dental-college.s3.amazonaws.com'
export default {
  name: 'EpubReader',
  data () {
    return {
      epub: `${URL_EPUB}/49709315336.epub`,
      newEpub: [],
      show: false,
      book: {},
      rendition: {},
      chapter: '',
      toc: []
    }
  },
  mounted () {
    this.loadEpub()
  },
  methods: {
    loadEpub (e) {
      this.book = ePub(e ? e.target.result : this.epub)
      this.book.loaded.navigation.then(({ toc }) => {
        this.toc = toc
      })
      this.book.ready.then(() => {
        this.show = true
      })
      this.rendition = this.book.renderTo('epub-render', {
        height: '70vh',
        width: '98%'
      })
      this.rendition.display()
      document.getElementById('add')
    },
    uploadFile () {
      this.show = false
      var reader = new FileReader()
      reader.readAsArrayBuffer(this.newEpub)
      reader.onload = this.loadEpub
    },
    nextPage () {
      this.rendition.next()
    },
    previousPage () {
      this.rendition.prev()
    },
    touchMoveToPage ({ ...info }) {
      if (info.direction === 'left') {
        this.nextPage()
      } else {
        this.previousPage()
      }
    },
    goToExcerpt () {
      if (this.chapter.toLowerCase().indexOf('xhtml') > 0) {
        this.rendition.display(this.chapter)
      } else {
        this.rendition.display('epubcfi(' + this.chapter + ')')
        this.rendition.annotations.highlight('epubcfi(' + this.chapter + ')')
      }
    }
  }
}
</script>
