<template>
	<div
		class="square"
		:class="{
			'solution': isSolution,
			'blank': isBlank,
		}"
	>
		<input
			v-if="!isBlank"
			:id="id"
			type="text"
			size="1"
			maxlength="1"
			:value="value"
			@keydown.delete="backspace"
			@keydown="keydown"
			@focus.prevent="focus"
		>
		<slot />
	</div>
</template>

<script setup lang="ts">
type Props = {
    isSolution?: boolean,
    isBlank: boolean,
    id?: string,
    value?: string,
}
const props = defineProps<Props>();

const emit = defineEmits<{
    (e: 'backspaceEvent'): void,
    (e: 'keydownEvent'): void,
}>();

const isFinished = ref(false);

const backspace = (e: KeyboardEvent) => {
    emit('backspaceEvent', e);
};

const keydown = (e: KeyboardEvent) => {
    emit('keydownEvent', e);
};

const focus = (e: Event) => {
    (e.target as HTMLInputElement).focus();
    (e.target as HTMLInputElement).setSelectionRange(1, 1);
}
</script>

<style scoped>
.square {
	width: 3rem;
	height: 3rem;
	margin-top: -1px;
	margin-left: -1px;
	overflow: hidden;
	line-height: 3rem;
	text-align: center;
	text-transform: uppercase;
	border: 1px solid black;
}

.square input {
	width: 100%;
	height: 100%;
    margin-left: -1px;
	text-align: center;
	text-transform: uppercase;
    border: 0;
}

.blank {
	border: 1px solid transparent;
}

.solution {
	z-index: 1;
	font-weight: bold;
	background-color: #ccc;
}

.solution input {
	font-weight: bold;
	background-color: #ccc;
}
</style>
