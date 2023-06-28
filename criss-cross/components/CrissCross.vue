<template>
	<div style="position: relative">
		<form
			ref="form"
			class="content"
		>
			<div
				v-for="(word, id) in words"
				:key="id"
				class="row"
			>
				<criss-cross-letter :is-blank="true">
					{{ id + 1 }}.
				</criss-cross-letter>
				<criss-cross-letter
					v-for="(square, position) in lettersCount"
					:id="`${id}_${position}`"
					:key="`${id}_${position}`"
					:is-solution="position === leftLetters"
                    :is-blank="(leftLetters - position > word.letterPosition - 1) || (position - (leftLetters - word.letterPosition + 1) >= word.word.length)"
					@backspaceEvent="backspace"
					@keydownEvent="keydown"
				/>
			</div>
		</form>

		<div
			v-if="isFinished"
			class="modal"
		>
			HOTOVO!
		</div>

		<div class="legend">
			<p
				v-for="(w, i) in words"
				:key="i"
			>
				{{ i + 1 }}. {{ w.legend }}
			</p>
		</div>
	</div>
</template>

<script setup lang="ts">
type Word = {
    id: number,
    word: string,
    letterPosition: number,
    legend: string,
};

type Props = {
    words: Word[],
};

const props = defineProps<Props>();

const solution = computed(() => props.words.map((a) => String(a.word)).join(''));
const isFinished = ref(false);
const inputNumber = ref(0);

const leftLetters = computed<number>(() => {
    const leftestWord = props.words.reduce((prev, curr) => {
        return (prev.letterPosition >= curr.letterPosition ? prev : curr);
    });
    return leftestWord.letterPosition - 1;  
});

const rightLetters = computed<number>(() => {
    const rightestWord = props.words.reduce((prev, curr) => {
        return (prev.letterPosition <= curr.letterPosition && prev.word.length > curr.word.length ? prev : curr);
    });
    return rightestWord.word.length - rightestWord.letterPosition;
});

const lettersCount = computed(() => leftLetters.value + rightLetters.value + 1)

const form = ref();

onMounted(() => {
    form.value.elements.item(0).focus();
});

const removeDiacritics = (str: string) => {
    const from = 'àáäâèéëêìíïîòóöôùúüûñçěščřžýáíéďť';
    const to = 'aaaaeeeeiiiioooouuuuncescrzyaiedt';
    for (let i = 0, l = from.length; i < l; i++) {
        str = str.replace(new RegExp(from.charAt(i), 'g'), to.charAt(i));
    }
    return str;
};

const backspace = (event: KeyboardEvent) => {
    // matches delete, too
    if (event.key === 'Backspace') {
        const elements = form.value.elements;
        const currentIndex = Array.from(elements).indexOf(event.target);

        if (elements.item(currentIndex).value !== '') {
            elements.item(currentIndex).value == '';
            return false;
        }
        elements.item(currentIndex > 1 ? currentIndex - 1 : 0).focus();
        return true;
    }
};

const keydown = (event: KeyboardEvent) => {
    const isLetter = /^[a-zA-Zá-žÁ-Ž]$/.test(event.key);
    
    if (isLetter && !event.altKey && !event.ctrlKey && !event.metaKey) {
        // focus next input
        const elements = form.value.elements;
        const currentIndex = Array.from(elements).indexOf(event.target);

        elements.item(currentIndex).value = event.key;
        elements.item(currentIndex < elements.length - 1 ? currentIndex + 1 : 0).focus();

        const fullString = Array.from(elements).map((a) => String(a.value)).join('');

        if (removeDiacritics(fullString.toLowerCase()) == removeDiacritics(solution.value.toLowerCase())) {
            isFinished.value = true;
        }
        event.preventDefault();
    }
    else if (event.key.substring(0, 5) == 'Arrow') {
        const elements = form.value.elements;
        const currentIndex = Array.from(elements).indexOf(event.target);
        let row = parseInt(elements.item(currentIndex).id.substring(0, 1));
        let col = parseInt(elements.item(currentIndex).id.substring(2));
        
        switch (event.key.substring(5)) {
        case 'Left':
            col--;
            break;
        case 'Right':
            col++;
            break;
        case 'Up':
            row--;
            break;
        case 'Down':
            row++;
            break;
        }
        if (elements.namedItem(row + '_' + col)) {
            elements.namedItem(row + '_' + col).focus();
        }
        event.preventDefault();
    } else if (event.key !== 'Backspace' && event.key !== 'Tab') {
        event.preventDefault();
    }
};
</script>

<style scoped>
.content {
	display: flex;
	flex-direction: column;
	justify-content: center;
	width: 100%;
	margin-top: 3rem;
}

.legend {
	display: flex;
	flex-direction: column;
	align-items: center;
	margin-top: 4rem;
}

.legend p {
    margin-top: 0;
}

.row {
    
	display: flex;
	flex-direction: row;
	justify-content: center;
}

.modal {
    position: absolute;
    top: 20%;
	font-size: 3rem;
    width: 100%;
	text-align: center;
	background: #fff;
	border: 1px solid black;
    margin-top: 2rem;
    padding-top: 2rem;
    padding-bottom: 2rem;
    z-index: 5;
}

* {
    font-family: Verdana, Geneva, Tahoma, sans-serif;
}
</style>
