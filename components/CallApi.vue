<template>
</template>


<script setup>
const props = defineProps({
    method: String,
    url:String,
})



if (props.method === 'GET') {
    axios.get(url).then(response => { 
        console.log(response.data)
    })
 }
</script>