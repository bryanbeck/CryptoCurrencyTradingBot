<template lang='jade'>
.grd
  .px1
    h3.center2 Paper Trader
    em(v-if='toggle === "closed"')
      div.sub-header-text
        a(href='#', v-on:click.prevent='switchToggle') Change paper trader settings
    template(v-if='toggle === "open"')
      p Settings:     
      textarea.params(v-model='rawPaperTraderParams')
      p.bg--red.p1(v-if='rawPaperTraderParamsError') {{ rawPaperTraderParamsError.message }}
</template>

<script>

import _ from 'lodash'
import { get } from '../../../tools/ajax'

export default {
  created: function() {
    get('configPart/paperTrader', (error, response) => {
      this.rawPaperTraderParams = response.part;
    });
  },
  data: () => {
    return {
      rawPaperTraderParams: '',
      rawPaperTraderParamsError: false,
      paperTraderParams: {},
      toggle: 'closed'
    };
  },
  watch: {
    rawPaperTraderParams: function() { this.emitConfig() }
  },
  methods: {
    switchToggle: function() {
      if(this.toggle === 'open')
        this.toggle = 'closed';
      else
        this.toggle = 'open';
    },
    emitConfig: function() {
      this.parseParams();
      this.$emit('settings', this.paperTraderParams);
    },
    parseParams: function() {
      try {
        this.paperTraderParams = toml.parse(this.rawPaperTraderParams);
        this.paperTraderParams.reportRoundtrips = true;
        this.rawPaperTraderParamsError = false;
      } catch(e) {
        this.rawPaperTraderParamsError = e;
        this.paperTraderParams = {};
      }
    }   
  }
}
</script>
<style>
.align .custom-select select {
  padding: 0.4em 1.2em .3em .8em;
}

.label-like {
  display: block;
  font-size: 0.9em;
  color: white;
}

.align {
  padding-left: 1em;
}

.sub-header-text{
text-align: center;
 
}
</style>
