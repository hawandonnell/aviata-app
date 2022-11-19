<script setup>
import { ref, watch } from "vue";
const props = defineProps({
	optionName: String,
	checkboxClass: String,
});
const isChecked = ref(false);
const emit = defineEmits(["setFilter"]);
watch(isChecked, (newVal) => {
	emit("setFilter", newVal);
});
</script>

<template>
	<div class="filter-option">
		<input
			v-model="isChecked"
			type="checkbox"
			:name="optionName"
			:class="checkboxClass"
		/>
		<label :for="optionName">{{ optionName }}</label>
	</div>
</template>

<style lang="scss" scoped>
input[type="checkbox"]:checked {
	background-color: red;
}
.filter-option {
	font-size: 12px;
	display: flex;
	align-items: center;
	font-weight: 500;

	label {
		margin-left: 12px;
	}
}
.filter-option + .filter-option {
	margin-top: 12px;
}
</style>
