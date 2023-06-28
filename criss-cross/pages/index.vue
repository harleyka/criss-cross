<template>
    <div>
        <h1>Křížovka</h1>

        <criss-cross :words="words" />
    </div>
</template>

<script setup lang="ts">
const words = ref([
	{ word: 'korek', letterPosition: 1, legend: 'Materiál získaný odřezáním kůry dubu korkového' },
	{ word: 'kostel', letterPosition: 2, legend: 'Stavba sloužící k modlitbám' },
	{ word: 'web', letterPosition: 3, legend: 'Jiný výraz pro internetovou stránku, v angličtině znamená "pavučina"' },
	{ word: 'kláda', letterPosition: 2, legend: 'Kmen poraženého stromu' },
	{ word: 'žito', letterPosition: 2, legend: 'Obilí (vyrábí se z něj také mouka a chléb)' },
    { word: 'holínky', letterPosition: 1, legend: 'Obuv do deště' },
    { word: 'ahoj', letterPosition: 1, legend: 'Pozdrav' },
]);
</script>

<style scoped>
h1 {
    font-family: Verdana, Geneva, Tahoma, sans-serif;
	margin-top: 2rem;
	font-weight: bold;
	font-size: 1.5rem;
	text-align: center;
}
</style>