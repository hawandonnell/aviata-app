<script setup>
import { reactive, computed } from "vue";
import Filter from "./components/Filter.vue";
import FilterOption from "./components/FilterOption.vue";
import Flight from "./components/Flight.vue";
import _ from "lodash";
import data from "./results.json";

const state = reactive({ filters: [], companyFilters: [] });

function setFilter(value, filter, filterPath) {
	if (value) {
		state[filterPath].push(filter);
	} else {
		state[filterPath] = state[filterPath].filter(
			(stateFilter) => stateFilter !== filter
		);
	}
}

function dropFilter(filterPath, clearInputs) {
	state[filterPath] = [];
	if (clearInputs) {
		document
			.querySelectorAll("input[type=checkbox]")
			.forEach((input) => (input.checked = false));
	}
}

const filteredFlights = computed(() => {
	let sourceData = _.cloneDeep(data.flights);
	if (state.companyFilters.length) {
		sourceData = [];
		state.companyFilters.forEach((company) => {
			// sourceData = sourceData.filter(
			// 	(filterData) => filterData.itineraries[0][0].carrier === company
			// );
			sourceData = sourceData.concat(
				data.flights.filter(
					(filterData) => filterData.itineraries[0][0].carrier === company
				)
			);
		});
	}
	if (state.filters.length) {
		state.filters.forEach((filter) => {
			if (filter === "forward") {
				sourceData = sourceData.filter((data) => !data.itineraries[0][0].stops);
			}
			if (filter === "refunding") {
				sourceData = sourceData.filter((data) => data.refundable);
			}
			if (filter === "withBaggage") {
				sourceData = sourceData.filter((data) =>
					Object.keys(data.services).includes("20KG")
				);
			}
		});
	}

	return sourceData;
});
</script>

<template>
	<div class="container">
		<div class="content">
			<div class="filters">
				<Filter
					class="filter"
					title="Опции тарифа"
					checkbox-class="flight-options-checkbox"
					@drop-filter="() => dropFilter('filters')"
				>
					<FilterOption
						checkbox-class="flight-options-checkbox"
						option-name="Только прямые"
						@setFilter="(value) => setFilter(value, 'forward', 'filters')"
					/>
					<FilterOption
						checkbox-class="flight-options-checkbox"
						option-name="Только с багажом"
						@setFilter="(value) => setFilter(value, 'withBaggage', 'filters')"
					/>
					<FilterOption
						checkbox-class="flight-options-checkbox"
						option-name="Только возвратные"
						@setFilter="(value) => setFilter(value, 'refunding', 'filters')"
					/>
				</Filter>
				<Filter
					class="filter"
					checkbox-class="flight-company-checkbox"
					title="Авиакомпании"
					@drop-filter="() => dropFilter('companyFilters')"
				>
					<!-- <FilterOption
						option-name="Все"
						@set-filter="() => dropFilter('companyFilters')"
					/> -->
					<FilterOption
						v-for="(company, index) in data.airlines"
						:key="company"
						checkbox-class="flight-company-checkbox"
						:option-name="company"
						@set-filter="(value) => setFilter(value, index, 'companyFilters')"
					/>
				</Filter>
				<button
					class="additional-info"
					style="margin: 14px 0"
					@click="
						dropFilter('filters');
						dropFilter('companyFilters', true);
					"
				>
					Сбросить все фильтры
				</button>
			</div>
			<div class="flights">
				<TransitionGroup name="fade">
					<Flight
						v-for="flight in filteredFlights"
						:key="flight.id"
						:flight="flight"
						:airlines="data.airlines"
					/>
				</TransitionGroup>
			</div>
		</div>
	</div>
</template>

<style scoped>
.fade-move,
.fade-enter-active,
.fade-leave-active {
	transition: all 0.5s cubic-bezier(0.55, 0, 0.1, 1);
}

/* 2. declare enter from and leave to state */
.fade-enter-from,
.fade-leave-to {
	opacity: 0;
	transform: scaleY(0.01) translate(30px, 0);
}

/* 3. ensure leaving items are taken out of layout flow so that moving
      animations can be calculated correctly. */
.fade-leave-active {
	position: absolute;
	width: 100%;
}
.filters {
	height: max-content;
	position: sticky;
	top: 14px;
}
.container {
	max-width: 1140px;
	margin: 0 auto;
	padding: 50px 14px;
}
.filter + .filter {
	margin-top: 12px;
}
.content {
	display: flex;
	gap: 12px;
}
.flights {
	flex-grow: 1;
	position: relative;
}

@media screen and (max-width: 1100px) {
	.content {
		flex-direction: column;
	}
	.filters {
		position: relative;
	}
}
</style>
