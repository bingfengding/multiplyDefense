<template>
  <div id="app">
    <gamecontainer></gamecontainer>
  </div>
</template>

<script>
import gamecontainer from './components/gamecontainer.vue'
export default {
  name: 'App',
  components: {
    gamecontainer
  },
  mounted () {

  },
  methods: {

  }
}
</script>

<style lang="stylus">
* {
  box-sizing: border-box;
}
body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,form,fieldset,input,textarea,p,blockquote,th,td {
  margin:0;
  padding:0;
}
table {
  border-collapse:collapse;
  border-spacing:0;
}
fieldset,img {
  border:0;
}
address,caption,cite,code,dfn,em,strong,th,var {
  font-style:normal;
  font-weight:normal;
}
ol,ul {
  list-style:none;
}
caption,th {
  text-align:left;
}
q:before,q:after {
  content:'';
}
abbr,acronym {
  border:0;
}
body
  position relative
  background #999
#app
  display flex
  justify-content center
</style>
