<template>
  <div>
      <input type="text" @keypress.enter="onInput">
  </div>
</template>

<script>
export default {
    name: 'SearchBar',
    methods: {
        onInput(event) {
            //입력된 데이터를 찾자!
            // console.log(event) 
            this.$emit('input-change', event.target.value)
        }
    }
}
</script>

<style scoped>
input {
  width: 75%;
}

div {
  text-align: center;
  margin: 20px;
}
</style>