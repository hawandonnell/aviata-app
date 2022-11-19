<script setup>
const props = defineProps({
	title: String,
	checkboxClass: String,
});

function uncheckInputs() {
	const inputs = document.querySelectorAll("." + props.checkboxClass);
	inputs.forEach((input) => (input.checked = false));
}
</script>

<template>
	<div class="filter">
		<div class="filter__header">
			<div class="filter__title">{{ title }}</div>
			<button
				class="filter__drop"
				@click="
					$emit('dropFilter');
					uncheckInputs();
				"
			>
				<img src="../assets/icons/icon-close-new.svg" alt="Drop Filters" />
			</button>
		</div>
		<div class="filter__content">
			<slot></slot>
		</div>
	</div>
</template>

<style lang="scss" scoped>
.filter {
	background-color: white;
	padding: 12px;
	min-width: 240px;
	border-radius: 4px;
	.filter__header {
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
	.filter__title {
		font-size: 14px;
		font-weight: 700;
	}
	.filter__content {
		margin-top: 12px;
		max-height: 320px;
		overflow: auto;
		&::-webkit-scrollbar {
			width: 2px;
		}
		&::-webkit-scrollbar-thumb {
			width: 2px;
			background-color: #e1e1e1;
		}
	}
	.filter__drop:hover {
		cursor: pointer;
	}
}
</style>
