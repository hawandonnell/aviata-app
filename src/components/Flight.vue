<script setup>
import { format, intervalToDuration } from "date-fns";
import { ru } from "date-fns/locale";

const props = defineProps({
	flight: Object,
	airlines: Object,
});
</script>

<template>
	<div class="flight">
		<div class="left">
			<div class="top">
				<div class="carrier">
					<img
						width="15"
						:src="
							'https://aviata.kz/static/airline-logos/80x80/' +
							flight.itineraries[0][0].carrier +
							'.png'
						"
						:alt="airlines[flight.itineraries[0][0].carrier]"
					/>
					<div class="carrier__name">
						{{ airlines[flight.itineraries[0][0].carrier] }}
					</div>
				</div>
				<div class="service service-top">
					<span class="baggage">{{
						flight.itineraries[0][0].segments[0].services.alt_text === "20 кг"
							? flight.itineraries[0][0].segments[0].services.alt_text
							: "Нет багажа"
					}}</span>
				</div>
				<div class="flight-date dep">
					<div class="date">
						{{
							format(
								new Date(flight.itineraries[0][0].dep_date),
								"d MMM, eeeeee",
								{
									locale: ru,
								}
							)
						}}
					</div>
					<div class="time">
						{{
							format(new Date(flight.itineraries[0][0].dep_date), "p", {
								locale: ru,
							})
						}}
					</div>
				</div>
				<div class="flight-line-wrapper">
					<div class="flight-line-places-and-time">
						<div class="place">
							{{ flight.itineraries[0][0].segments[0].origin_code }}
						</div>
						<div class="time">
							{{
								intervalToDuration({
									start: 0,
									end: flight.itineraries[0][0].traveltime * 1000,
								}).hours
							}}
							ч.
							{{
								intervalToDuration({
									start: 0,
									end: flight.itineraries[0][0].traveltime * 1000,
								}).minutes
							}}
							м.
						</div>
						<div class="place">
							{{ flight.itineraries[0][0].segments[0].dest_code }}
						</div>
					</div>
					<div class="flight-line">
						<div class="line"></div>
						<div
							v-for="(segment, index) in flight.itineraries[0][0].segments
								.length + 1"
							:key="index"
							class="circle"
						></div>
					</div>
					<div
						v-if="flight.itineraries[0][0].segments.slice(1).length"
						class="flight-stops"
					>
						через
						<span
							v-if="flight.itineraries[0][0].segments.slice(1)"
							class="stop-city"
							v-for="segment in flight.itineraries[0][0].segments.slice(1)"
							>{{ segment.origin }}</span
						>
					</div>
				</div>
				<div class="flight-date arr">
					<div class="date">
						{{
							format(
								new Date(flight.itineraries[0][0].arr_date),
								"d MMM, eeeeee",
								{
									locale: ru,
								}
							)
						}}
					</div>
					<div class="time">
						{{
							format(new Date(flight.itineraries[0][0].arr_date), "p", {
								locale: ru,
							})
						}}
					</div>
				</div>
			</div>
			<div class="bottom">
				<button class="additional-info fly-details">Детали перелета</button>
				<button class="additional-info fly-conditions">Условия тарифа</button>
				<div
					class="refund"
					:class="{ refundable: flight.itineraries[0][0].refundable }"
				>
					<img
						v-if="!flight.itineraries[0][0].refundable"
						src="../assets/icons/icon-non-refundable.svg"
						alt="icon-non-refundable"
					/>
					{{
						flight.itineraries[0][0].refundable ? "возвратный" : "невозвратный"
					}}
				</div>
			</div>
		</div>
		<div class="right">
			<div class="flight-price">{{ flight.price }} {{ flight.currency }}</div>
			<button class="choose">Выбрать</button>
			<span class="price-for-all-passengers">Цена для всех пассажиров</span>
			<div class="service">
				<span class="baggage">{{
					flight.itineraries[0][0].segments[0].services.alt_text === "20 кг"
						? flight.itineraries[0][0].segments[0].services.alt_text
						: "Нет багажа"
				}}</span>
				<button class="add-baggage">+ Добавить багаж</button>
			</div>
		</div>
	</div>
</template>

<style lang="scss" scoped>
.flight-stops {
	font-size: 12px;
	color: #ff9900;
	font-weight: 400;
	text-align: center;
	.stop-city + .stop-city::before {
		content: ", ";
	}
}
.baggage {
	font-size: 12px;
	font-weight: 400;
	margin-right: 14px;
}
.right {
	.price-for-all-passengers {
		font-size: 12px;
		font-weight: 400;
		color: #707276;
	}
	.add-baggage {
		color: #5763b3;
		font-weight: 600;
		font-size: 12px;
		padding: 3px 8px;
		background-color: #eaf0fa;
	}
	background-color: rgba(245, 245, 245, 1);
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	width: 240px;
	height: 168px;
	gap: 13px;
	.flight-price {
		font-weight: 400;
		font-size: 26px;
	}
	.choose {
		font-size: 18px;
		font-weight: 700;
		color: white;
		background-color: rgba(85, 187, 6, 1);
		border-radius: 4px;
		padding: 7px 64px;
	}
}
.flight-line-places-and-time {
	display: flex;
	align-items: center;
	justify-content: space-between;

	.place {
		font-size: 10px;
		font-weight: 400;
		color: rgba(185, 185, 185, 1);
	}
	.time {
		font-size: 12px;
		font-weight: 400;
		color: rgba(32, 33, 35, 1);
	}
}
.flight-line {
	width: 168px;
	position: relative;
	display: flex;
	align-items: center;
	justify-content: space-between;
	.line {
		width: 168px;
		height: 1px;
		background-color: rgba(185, 185, 185, 1);
		z-index: 1;
		position: absolute;
	}
	.circle {
		background-color: white;
		width: 5px;
		height: 5px;
		border: 1px solid rgba(185, 185, 185, 1);
		z-index: 2;
		border-radius: 50%;
	}
}
.flight {
	max-height: 168px;
	// overflow: auto;
	background-color: white;
	transition: box-shadow 0.5s;
	border-radius: 4px;
	display: flex;
	align-items: center;
	justify-content: space-between;
	&:hover {
		box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.15);
	}
	.top {
		display: flex;
		margin-bottom: 40px;
		gap: 30px;
		align-items: center;
	}
	.left {
		padding: 40px 40px 14px 40px;
	}
	.carrier {
		display: flex;
		align-items: center;
		gap: 10px;
		.carrier__name {
			color: rgba(32, 33, 35, 1);
			font-weight: 600;
			font-size: 14px;
		}
	}
}
.service-top {
	display: none;
}
.flight-date {
	.date {
		font-size: 12px;
		font-weight: 400;
	}
	.time {
		font-size: 24px;
		font-weight: 600;
	}
}
.bottom {
	display: flex;
	align-items: center;
	gap: 12px;
}
.additional-info {
	font-size: 12px;
	font-weight: 400;
	color: rgba(114, 132, 228, 1);
	text-decoration-line: underline;
	text-decoration-style: dashed;
}
.refund {
	display: flex;
	align-items: center;
	gap: 7px;
	font-size: 12px;
	font-weight: 400;
}
.flight + .flight {
	margin-top: 12px;
}

@media screen and (max-width: 850px) {
	.fly-conditions,
	.fly-details,
	.refund,
	.service {
		display: none;
	}
	.flight {
		flex-direction: column;
		max-height: unset;
	}
	.right {
		width: 100%;
	}
	.top {
		display: grid !important;
		grid-template-columns: repeat(2, 1fr);
		grid-template-rows: repeat(3, 1fr);
		.service-top {
			display: inline;
			grid-row-start: 1;
			grid-column-start: 2;
			text-align: end;
			.baggage {
				margin-right: 0;
			}
		}
		.flight-date {
			grid-row-start: 2;
			&.arr {
				text-align: end;
			}
		}
		.flight-line-wrapper {
			grid-row-start: 3;
			grid-column-start: 1;
			grid-column-end: 3;
			.flight-line {
				width: 100%;
				.line {
					width: 100%;
				}
			}
		}
	}
}
</style>
