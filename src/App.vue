<template>
  <div id="app">
    <header>
      <h1>Download Free Stuff From Reddit's Favorite Websites</h1>
    </header>
    <main>
      <input v-model="filter" placeholder="filter entries" />
      <div class="boxes">
        <div class="box" :id="slug(e.category)" v-for="e in filteredEntries">
          <header>
            <h1>{{ e.category }}</h1>
            <p>
              <a v-if="isActiveEntry(e)" href="#">{{ e.entries.length }} entries</a>
              <a v-else :href="`#${slug(e.category)}`">{{ e.entries.length }} entries</a>
            </p>
          </header>
          <ol>
            <li class="hide"> </li>
            <li v-for="entry in e.entries">
              <p>
                <a :href="entry.url" target="_blank">{{ entry.name }}</a>
                {{ entry.description }}
              </p>
            </li>
          </ol>
        </div>
      </div>
    </main>
    <footer>
      original data via
      <a href="https://lifehacker.com/download-free-stuff-from-reddits-favorite-websites-1825781590" target="_blank">lifehacker</a>
      |
      fork me on
      <a href="https://github.com/koehr/reddres" target="_blank">github</a>
    </footer>
  </div>
</template>

<script>
import entries from './entries.json'

export default {
  name: 'app',
  data () {
    return {
      activeCategory: window.location.hash.slice(1),
      filter: ''
    }
  },
  mounted () {
    window.addEventListener('hashchange', () => {
      this.activeCategory = window.location.hash.slice(1)
    })
  },
  computed: {
    activeEntry () {
      return entries.find(e => this.slug(e.category) === this.activeCategory)
    },
    filteredEntries () {
      const f = this.filter.trim()
      if (f.length === 0) return entries

      return entries.map(c => {
        return {
          category: c.category,
          entries: c.entries.filter(e => {
            const searchString = `${e.url}${e.name}${e.description}`
            return searchString.indexOf(f) >= 0
          })
        }
      })
    }
  },
  methods: {
    slug (s) {
      return s.toLowerCase().replace(/ /g, '-')
    },
    isActiveEntry (e) {
      return this.activeCategory === this.slug(e.category)
    }
  }
}
</script>

<style>
body {
  margin: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

header {
  margin: 0;
  background-color: #35495E;
  color: #ffffff;
  text-align: center;
}

header > h1 {
  display: block;
  margin: 0;
  font-size: 20px;
  line-height: 3em;
  letter-spacing: .02em;
  font-weight: 400;
}

main {
  display: block;
  width: 100vw;
  overflow: hidden;
}
main > input {
  display: block;
  width: 20em;
  font-size: 2em;
  margin: 1em auto .5em;
  text-align: center;
}

.box {
  display: inline-block;
  width: 12em;
  margin: 1em;
  border: 1px solid black;
  vertical-align: top;
}
.box:target ol {
  display: block;
  width: 210%;
  margin: 2em 0 -1px -1px;
  padding: 0 0 1em 1em;
  background: white;
  border: 1px solid black;
}
.box > header {
  background-color: white;
}
.box > header > h1 {
  background-color: #35495E;
  line-height: 2em;
}
.box ol {
  display: none;
  text-align: left;
  list-style: none;
  padding-left: 1em;
}
.box ol > li.hide {
  display: block;
  width: 12em;
  height: 1em;
  background: white;
  margin: -1px 0 0 -1em;
}

footer {
  display: block;
  position: fixed;
  bottom: 0;
  padding: .5em 1em;
  background: white;
}

@media only screen and (max-device-width : 480px) {
  .box {
    display: block;
    width: 100vw;
    margin: 2em auto;
    border: none;
  }
  .box:target ol {
    width: calc(100vw - 2em);
    margin: 2em 0;
    border: none;
    font-size: 1.2em;
  }
  .box:target .hide {
    display: none;
  }
}
</style>
