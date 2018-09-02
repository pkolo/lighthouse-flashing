<template>
  <div id="app">
    <div>The Lighthouse Flashing</div>

    <div v-if="!sheets">Loading...</div>

    <div v-if="sheets">
      <div style="margin-bottom: 20px">
        <div class="sheet-links" v-for="sheet in sheets" v-if="sheet.name.includes('Albums')">
          <span class="sheet-link" @click="setSheetYear(sheet.name.match(/\d+/)[0])">{{sheet.name}}</span>
        </div>
      </div>

      <div style="margin-bottom: 20px">
        Search: <input v-model="filterQ" />
      </div>

      <div v-for="album in filteredSheet">
        <div class="list-item">
          <div>
            <div class="artist-name">{{album['Artist']}}</div>

            <div class="album-title">{{album['Album']}}</div>

            <div class="release-date">{{album['Rel. Date']}}/{{sheetYear}}</div>
          </div>

          <div class="list-item-detail">
            <div class="note erik" v-if="album['Score (eh)'].length || album[`erik notes / best tracks`].length">
              <div>
                <div>Erik<span v-if="album['eh play date'].length">, {{album['eh play date']}}</span></div>

                <div class="score" v-if="album['Score (eh)'].length">{{album['Score (eh)']}}</div>
              </div>

              <div v-if="album[`erik notes / best tracks`]">
                {{album[`erik notes / best tracks`]}}
              </div>
            </div>

            <div class="note tim" v-if="album['Score (tk)'].length || album[`tim notes / best tracks`].length">
              <div>
                <div>Tim<span v-if="album['tk play date'].length">, {{album['tk play date']}}</span></div>

                <div class="score" v-if="album['Score (tk)'].length">{{album['Score (tk)']}}</div>
              </div>

              <div v-if="album[`tim notes / best tracks`]">
                {{album[`tim notes / best tracks`]}}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Tabletop from 'tabletop';

export default {
  name: 'app',
  data () {
    return {
      sheets: this.getTable(),
      sheetYear: '2018',
      filterQ: ''
    }
  },
  methods: {
    getTable () {
      var public_spreadsheet_url = '18rLxB1XPOXYtE1Baurydtnq7wwUlfWr4mvWDltQvuw0'

      Tabletop.init( { key: public_spreadsheet_url,
        callback: this.showInfo
      });
    },
    showInfo (data) { // eslint-disable-next-line
      console.log(data)
      this.sheets = data
    },
    getAlbumSheet (year) {
      return this.sheets[`${this.sheetYear} - Albums`]['elements']
    },
    setSheetYear (year) {
      this.sheetYear = year
    }
  },
  computed: {
    filteredSheet: function () {
      return this.getAlbumSheet(this.sheetYear).filter(album => { return (album[`erik notes / best tracks`].includes(this.filterQ) || (album[`tim notes / best tracks`].includes(this.filterQ))) })
    }
  }
}
</script>

<style>
body {
  margin: 0;
  padding: 10px;
}

#app {
  font-family: "Maven Pro", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #444;
}

.list-item {
  display: grid;
  grid-template-columns: 1fr 4fr;
  margin-bottom: 10px;
  border: 1px solid #444;
  color: #444;
}

.list-item > div:first-child {
  padding: 10px;
}

.list-item-detail > div {
  display: grid;
  grid-template-columns: 1fr 3fr;
  padding: 10px;
}

.note {
  line-height: 22px;
}

.note.erik {
  background-color: #3a45a6;
  color: #fff;
}

.note.tim {
  background-color: #ffff00;
}

.artist-name {
  font-size: 22px;
}

.score {
  font-size: 20px;
}

.album-title {
  font-size: 16px;
  font-style: italic;
}

.release-date {
  font-size: 14px;
}

.sheet-links {
  display: inline-block;
  padding: 0 20px 0 0;
}

.sheet-links:hover {
  text-decoration: underline;
  cursor: pointer;
}
</style>
