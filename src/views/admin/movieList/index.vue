<template>
	<v-container background-color="grey" class="ma-#">
		<v-row align="center" justify="center">
			<v-col cols="12">
				<v-card class="mx-auto pa-4 pt-0" elevation="1">
					<v-row>
						<v-col cols="1" md="2" sm="2" justify="center" align="center">
							<v-card color="pink lighten-1" class="card-icon" dark elevation="1">
								<v-icon large>mdi-playlist-check</v-icon>
							</v-card>
						</v-col>
						<v-col>
							<h2 class="pt-5 blue-grey--text">All Movies</h2>
						</v-col>
					</v-row>
					<v-card-title class="pr-0">
						<v-spacer></v-spacer>
						<v-row no-gutters>
							<v-col>
								<v-text-field
									v-model="search"
									append-icon="mdi-magnify"
									placeholder="Search"
									class="font-weight-medium"
									background-color="grey lighten-4"
									light
									color="primary"
									hide-details
									clearable
									solo
									rounded
								></v-text-field>
							</v-col>
						</v-row>
					</v-card-title>
					<v-card-text class="mt-5 pa-0">
						<v-row no-gutters>
							<v-col>
								<v-data-table :headers="headers" :items="allMovies" class="font-weight-light" id="customBorder" :search="search">
									<template v-slot:body="{ items }">
										<tbody>
											<tr v-for="(item, i) in items" :key="i">
												<td>
													<v-list-item class="pa-0">
														<v-list-item-avatar>
															<v-img :src="getMoviePoster(item.image)"></v-img>
														</v-list-item-avatar>

														<v-list-item-content>
															<v-list-item-title>
																<h4 class="blue-grey--text pl-2" v-html="item.title"></h4>
															</v-list-item-title>
															<v-list-item-subtitle>
																<span v-for="(actor, index) in item.actor" :key="index">
																	<v-chip small label class="ma-1 font-weight-medium" :color="randomColor()" text-color="white">{{
																		actor
																	}}</v-chip>
																</span>
															</v-list-item-subtitle>
														</v-list-item-content>
													</v-list-item>
												</td>
												<td class="pr-1 pl-1">
													<span v-for="(genre, ind) in item.genre" :key="ind">
														<v-chip small class="ma-2 font-weight-bold" color="blue" label text-color="white">{{ genre.title }}</v-chip>
													</span>
												</td>
												<td class="pr-1 pl-1">
													<h5 class="blue-grey--text">{{ item.inStock }}</h5>
												</td>
												<td class="pr-1 pl-1">
													<h5 class="blue-grey--text">₱ {{ item.rentPrice | formatNumber }}</h5>
												</td>
												<td class="pr-1 pl-1">
													<v-btn class="mr-1" height="30" width="35" dark small color="green">
														<v-icon small dark>mdi-square-edit-outline</v-icon>
													</v-btn>
													<v-btn class="ml-1" height="30" width="35" dark small color="error">
														<v-icon dark small>mdi-close</v-icon>
													</v-btn>
												</td>
											</tr>
										</tbody>
									</template>
									<!-- <template v-slot:no-data>
										<span>No data available</span>
									</template>-->
								</v-data-table>
							</v-col>
						</v-row>
					</v-card-text>
				</v-card>
			</v-col>
		</v-row>
	</v-container>
</template>

<script>
import store from "Store";
import numeral from "numeral";
export default {
	name: "AdminMovieList",
	data() {
		return {
			dialog: false,
			search: "",
			headers: [
				{
					text: "Movie Title",
					class: "font-weight-bold grey lighten-4 pt-5 pb-5 blue-grey--text pt-5 pb-5",
					align: "",
					sortable: false,
					value: "title",
					width: "200",
				},
				{
					text: "Genres",
					value: "genre",
					class: "font-weight-bold grey lighten-4 pt-5 pb-5 blue-grey--text",
					width: "160",
				},
				{
					text: "Qty",
					value: "inStock",
					class: "font-weight-bold grey lighten-4 pt-5 pb-5 blue-grey--text",
					width: "70",
				},
				{
					text: "Rent",
					value: "actor",
					class: "font-weight-bold grey lighten-4 pt-5 pb-5 blue-grey--text",
					width: "70",
				},
				{
					text: "Actions",
					value: "",
					sortable: false,
					class: "font-weight-bold grey lighten-4 pt-5 pb-5 blue-grey--text",
					width: "90",
				},
			],

			movies: [],
		};
	},
	computed: {
		allMovies() {
			const getAllMovies = store.getters["Admin/getAllMovies"];
			return getAllMovies.docs;
		},
	},
	methods: {
		randomColor() {
			const r = () => Math.floor(Math.random() * (150 - 50) + 50);

			return `rgb(${r()}, ${r()}, ${r()})`;
		},
		getMoviePoster(poster) {
			let url = process.env.NODE_ENV !== "development" ? process.env.VUE_APP_PROD_URL : process.env.VUE_APP_API_URL;
			return `${url}/uploads/${poster}`;
		},
		async getAllMovies() {
			const params = {
				limit: 500,
				page: 1,
				sort: { title: 1 },
			};
			const movies = await store.dispatch("Admin/gettingAllMovies", params);
		},
	},
	created() {
		this.getAllMovies();
	},
	filters: {
		formatNumber(val) {
			if (!val) return "";
			return numeral(val).format("0.00");
		},
	},
};
</script>

<style scoped>
.v-data-table .v-data-table__wrapper table,
.v-data-table table,
#customBorder {
	border: 1px solid rgba(0, 0, 0, 0.12) !important;
}

.theme--light.v-text-field--solo-inverted.v-input--is-focused > .v-input__control > .v-input__slot input {
	color: #000 !important;
}
.card-icon {
	margin-top: -30px;
	padding-top: 35px;
	padding-bottom: 35px;
}
.v-btn:not(.v-btn--round).v-size--small {
	min-width: unset;
}
</style>