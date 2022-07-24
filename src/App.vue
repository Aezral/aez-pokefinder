<template>
	<div class="page d-flex flex-column justify-content-center align-items-center">
		<div class="main">
			<form @submit.prevent="buscar()">
				<div class="d-inline-flex flex-row align-items-baseline">
					<MDBInput label="Pokemón a buscar" v-model="search" autofocus />
					<MDBBtn
						type="submit"
						color="dark"
						class="ms-3"
						:disabled="!Boolean(search)"
					>
						<MDBSpinner v-if="loading" tag="span" size="sm" />
						<div v-else>Buscar</div>
					</MDBBtn>
				</div>
			</form>
			<transition>
				<div v-if="data" class="pokemon">
					<div class="mt-5" style="padding: 50px 50px 50px 50px">
						<div
							class="marco mx-auto d-flex justify-content-center align-items-center"
						>
							<img :src="data.sprites.front_default" alt="" />
						</div>
						<h3 class="align-self-center mt-4">{{ upper(data.name) }}</h3>
						<div class="d-flex flex-column">
							<div
								class="d-inline-flex mb-2 justify-content-center align-items-center"
							>
								<b>Pokedex: </b>
								<div class="ms-1">#{{ data.id }}</div>
							</div>
							<div class="d-inline-flex justify-content-center align-items-center">
								<b>Tipo:</b>
								<div class="ms-1">
									{{
										translate(data.types.map((x) => x.type.name))
											.map((x) => upper(x))
											.join(", ")
									}}
								</div>
							</div>
							<div class="mt-5">
								<h4>Stats base:</h4>
								<div>
									<div class="row mt-2">
										<div class="col">
											<div class="w-50 mx-auto">
												<div class="mt-2">
													<b>Hp: {{ data.stats[0].base_stat }}</b>
												</div>
												<MDBProgress class="w-100">
													<MDBProgressBar
														:value="prcnt(data.stats[0].base_stat)"
														bg="warning"
													></MDBProgressBar>
												</MDBProgress>
											</div>
										</div>
									</div>
									<div class="row mt-3">
										<div class="col">
											<div class="mt-2">
												<b>Atk: {{ data.stats[1].base_stat }}</b>
											</div>
											<MDBProgress class="w-100">
												<MDBProgressBar
													:value="prcnt(data.stats[1].base_stat)"
													bg="warning"
												></MDBProgressBar>
											</MDBProgress>
										</div>
										<div class="col">
											<div class="mt-2">
												<b>Def: {{ data.stats[2].base_stat }}</b>
											</div>
											<MDBProgress class="w-100">
												<MDBProgressBar
													:value="prcnt(data.stats[0].base_stat)"
													bg="warning"
												></MDBProgressBar>
											</MDBProgress>
										</div>
									</div>
									<div class="row mt-4">
										<div class="col">
											<div class="mt-2">
												<b>Atk Esp: {{ data.stats[3].base_stat }}</b>
											</div>
											<MDBProgress class="w-100">
												<MDBProgressBar
													:value="prcnt(data.stats[1].base_stat)"
													bg="warning"
												></MDBProgressBar>
											</MDBProgress>
										</div>
										<div class="col">
											<div class="mt-2">
												<b>Def Esp: {{ data.stats[4].base_stat }}</b>
											</div>
											<MDBProgress class="w-100">
												<MDBProgressBar
													:value="prcnt(data.stats[1].base_stat)"
													bg="warning"
												></MDBProgressBar>
											</MDBProgress>
										</div>
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
			</transition>
		</div>
	</div>
</template>

<script setup>
import { ref, inject } from "vue";
import {
	MDBBtn,
	MDBInput,
	MDBSpinner,
	MDBProgress,
	MDBProgressBar,
} from "mdb-vue-ui-kit";
import axios from "axios";
const swal = inject("$swal");

const loading = ref(false);
const search = ref(null);
const data = ref(null);

const buscar = async () => {
	loading.value = true;
	data.value = null;

	if (!search.value) {
		loading.value = false;
		return;
	}

	const busqueda = search.value.toLowerCase();

	try {
		const response = (
			await Promise.all([
				new Promise((resolve) => setTimeout(resolve, 500)),
				axios.get(`https://pokeapi.co/api/v2/pokemon/${busqueda}`),
			])
		)[1];

		data.value = response.data;

		console.log(data.value);

		loading.value = false;
	} catch (e) {
		data.value = null;
		swal.fire({
			icon: "error",
			title: "Error",
			text: "No encontramos un pokemon válido con ese nombre.",
		});
		loading.value = false;
	}
};

const upper = (str) => {
	return str.charAt(0).toUpperCase() + str.slice(1);
};

const translate = (arr) => {
	const dictionary = {
		bug: "insecto",
		dark: "siniestro",
		dragon: "dragon",
		electric: "electrico",
		fairy: "hada",
		fighting: "lucha",
		fire: "fuego",
		flying: "volador",
		ghost: "fantasma",
		grass: "planta",
		ground: "tierra",
		ice: "hielo",
		normal: "normal",
		poison: "veneno",
		psychic: "psíquico",
		rock: "roca",
		steel: "acero",
		water: "agua",
	};

	return arr.map((x) => dictionary[x] || x);
};

const prcnt = (x) => {
	return Math.floor((x * 100) / 255);
};
</script>

<style>
#app {
	font-family: Roboto, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
}

html,
body {
	background: rgb(228, 228, 228);
}

.page {
	height: 100vh;
}

.main {
	background: white;
	border-radius: 15px;

	padding: 50px 20px 50px 20px;
	max-width: 90vw;
}

@media (min-width: 700) {
	.main {
		max-width: 800px;
	}
}

.header {
	max-width: 600px;
}
.v-enter-active,
.v-leave-active {
	transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
	opacity: 0;
}

.pokemon {
	background: rgb(255, 205, 112);
	border-radius: 15px;
}

.marco {
	width: 150px;
	height: 150px;
	border: 2px solid white;
	border-radius: 100%;
	background: rgb(255, 226, 158);
}
</style>
