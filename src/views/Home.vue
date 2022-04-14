<template>
<div class="country" v-if="!error">
	<div class="country__title">{{ title }}</div>

	<h1 class="country__name" v-if="showDetails">{{ activeCountry.name.common}}</h1>

	<img class="country__flag" :src="activeCountry.flags.svg" >

	<button @click="toggleDetails" class="country__showDetails"> {{ button.text }} </button>
	
	<div  v-if="showDetails">
		<h2 class="country__title">capital:</h2>
		<span>{{ activeCountry.capital[0] }}</span>

		<h2 class="country__title">language:</h2>
		<li v-for="language in activeCountry.languages"> <ul>{{ language }}</ul> </li>
		
		<h2 class="country__title">population:</h2>
		<span> {{ activeCountry.population }} </span>
		
		<h2 class="country__title" >continent:</h2>
		<span>{{ activeCountry.continents[0] }} </span>
	</div>

	<button @click="fetchData" class="country__newData">	
		<svg width="50" height="50" viewBox="0 0 72 72" fill="none" xmlns="http://www.w3.org/2000/svg"> <path d="M36 0C45.5478 0 54.7045 3.79285 61.4558 10.5442C68.2072 17.2955 72 26.4522 72 36C72 45.5478 68.2072 54.7045 61.4558 61.4558C54.7045 68.2072 45.5478 72 36 72C26.4522 72 17.2955 68.2072 10.5442 61.4558C3.79285 54.7045 0 45.5478 0 36C0 26.4522 3.79285 17.2955 10.5442 10.5442C17.2955 3.79285 26.4522 0 36 0V0ZM20.25 33.75C19.6533 33.75 19.081 33.9871 18.659 34.409C18.2371 34.831 18 35.4033 18 36C18 36.5967 18.2371 37.169 18.659 37.591C19.081 38.0129 19.6533 38.25 20.25 38.25H46.3185L36.657 47.907C36.4478 48.1162 36.2819 48.3645 36.1686 48.6379C36.0554 48.9112 35.9972 49.2042 35.9972 49.5C35.9972 49.7958 36.0554 50.0888 36.1686 50.3621C36.2819 50.6355 36.4478 50.8838 36.657 51.093C36.8662 51.3022 37.1145 51.4681 37.3879 51.5814C37.6612 51.6946 37.9542 51.7528 38.25 51.7528C38.5458 51.7528 38.8388 51.6946 39.1121 51.5814C39.3855 51.4681 39.6338 51.3022 39.843 51.093L53.343 37.593C53.5525 37.384 53.7188 37.1357 53.8322 36.8624C53.9456 36.589 54.004 36.296 54.004 36C54.004 35.704 53.9456 35.411 53.8322 35.1376C53.7188 34.8643 53.5525 34.616 53.343 34.407L39.843 20.907C39.6338 20.6978 39.3855 20.5319 39.1121 20.4186C38.8388 20.3054 38.5458 20.2472 38.25 20.2472C37.9542 20.2472 37.6612 20.3054 37.3879 20.4186C37.1145 20.5319 36.8662 20.6978 36.657 20.907C36.4478 21.1162 36.2819 21.3645 36.1686 21.6379C36.0554 21.9112 35.9972 22.2042 35.9972 22.5C35.9972 22.7958 36.0554 23.0888 36.1686 23.3621C36.2819 23.6355 36.4478 23.8838 36.657 24.093L46.3185 33.75H20.25Z" fill="#909090"/></svg>
	</button>
	<!-- <img src="../../public/images/next.png" alt="next"> -->
</div>  
<p v-if="error" class="errors">{{ error }}</p>
</template>

<script>
export default {
	name: 'app1',
	data() {
		return {
			title: 'Countries of the World',
			countries: [], 
			activeCountry: {
				name: {
					common: ''
				},
				flags: {
					svg: ''
				},
				capital: '',
				languages: '',
				population: '',
				continents: '',
			},
			error: '',
			showDetails: false,
			button: {
				text: 'Show details'
			},
		}
	},

	created() {
		this.fetchData();
	},

	methods: {
		async fetchData() {
			const url = 'https://restcountries.com/v3.1/all';
			const response = await fetch(url); // default GET 
			// const results = await response.json();
			try {
				await this.handleResponse(response);
			} catch (error) {
				this.error = error.message;
			}

			// this.countries = results;
			// this.setActiveCountry(); 
		},

		async handleResponse(response) {
			if (response.status >= 200 && response.status < 300) {
				const results = await response.json();
				this.countries = results;
				this.setActiveCountry();
				return true; 
			}	else	{
				if(response.status === 404) {
					throw new Error('Url ikke funnet..');
				}
				if(response.status === 401) {
					throw new Error('Ikke autorisert ');
				}
				if(response.status > 500) {
					throw new Error('Server error!');
				}
				throw new Error (' Woops, noe gikk galt!');
			}
		},

		setActiveCountry() {
			const random = Math.floor(Math.random() * this.countries.length)  // https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random
			this.activeCountry = this.countries[(random - 1)];
		},

		toggleDetails() {
			this.showDetails = !this.showDetails
			this.button.text = this.showDetails ? 'Hide details' : 'Show details'; // https://stackoverflow.com/questions/46567323/vue-js-change-text-within-a-button-after-an-event
		}
	}
}
</script>

<style>

.country__title {
	color: grey;
	font-size: var(--font-size-medium);
}

h2 {
	color: grey;
	margin: 10px;
}

span {
	font-size: var(--font-size-big);
	margin: 10px;
}

li {
	list-style: none;
	font-size: var(--font-size-big);
}

.country__name {
	font-size: var(--font-size-big); 
}

.country {
	/* position: relative; */
	height: 100vh;	
	display: flex;
	flex-direction: column;
	justify-content:space-evenly;
	text-align: center;
}

.country__flag {
	/* position: absolute; */
	align-self: center;
	width: 260px;
	height: 160px;
}

.errors {
	display: flex;
	justify-content: center;
	font-size: var(--font-size-mega);
	color: red;
	padding: 100px;	

}

</style>

