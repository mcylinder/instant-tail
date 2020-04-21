<template>
  <div id="app">
    <div class="search">
      <input type="text" v-model="query" placeholder="Search" />
    </div>
    <div class="spacer"></div>
    <table>
      <tr>
        <th>class</th>
        <th>rule</th>
      </tr>
      <tr v-for="item in results" :key="item.id" :id="'rw' + item.id">
        <td class="tcls" title="click to copy" @click.stop.prevent="copyClass(item.class, item.id)">{{ item.class }}</td>
        <td class="rule">{{ item.rule }}</td>
      </tr>
    </table>

    <input type="hidden" id="pass-css" />
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'app',
  data() {
    return {
      query: '',
      results: [],
    };
  },
  methods: {
    copyClass(sendCopy, itemId) {
      let testingCodeToCopy = document.querySelector('#pass-css');
      testingCodeToCopy.value = sendCopy.trim().substr(1);
      testingCodeToCopy.setAttribute('type', 'text');
      testingCodeToCopy.select();

      try {
        document.execCommand('copy');
        signalCopy(itemId);
      } catch (err) {
        alert('Oops, unable to copy');
      }
      testingCodeToCopy.setAttribute('type', 'hidden');
      window.getSelection().removeAllRanges();
    },
  },
  watch: {
    query: function(val) {
      if (val !== '') {
        let app = this;
        axios.get('http://127.0.0.1:7700/indexes/tailcss/search?limit=700&q=' + val).then(function(response) {
          app.results = response.data.hits;
        });
      } else {
        this.results = [];
      }
    },
  },
};

function signalCopy(itemId) {
  let row = document.getElementById('rw' + itemId);
  row.classList.add('clear');

  setTimeout(function() {
    row.classList.remove('clear');
  }, 60);
}
</script>

<style>
* {
  box-sizing: border-box;
}

#app {
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  color: #2c3e50;
  font-family: Courier, serif;
  font-size: 13px;
  position: relative;
}
input {
  background-color: #e2e2e2;
  border: none;
  display: block;
  font-size: 16px;
  opacity: 1;
  padding: 7px 15px;
  transition-timing-function: ease-in;
  transition: opacity 0.3s;
  width: 100%;
}

.search {
  display: block;
  position: fixed;
  width: 100%;
}

.spacer {
  display: block;
  height: 39px;
}

body,
html {
  margin: 0;
  padding: 0;
}
table {
  border-collapse: collapse;
  width: 100%;
}

tr td {
  background-color: white;
  transition: background-color 70ms ease-in;
  width: 50%;
}

tr.clear td {
  background-color: #c1d8df;
  transition: background-color 200ms ease-out;
}

td,
th {
  padding: 7px 15px;
  text-align: left;
}
td {
  border-bottom: 1px solid #f1f1f1;
}

.tcls {
  color: #000000;
  cursor: pointer;
  font-weight: 600;
}
</style>
