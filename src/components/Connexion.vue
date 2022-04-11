<script>
export default {
	data(){
		return{
			listeUtilisateurs: [],

			email: '',
			password: '',

			error: false,
		}
	},
	mounted(){
		let tabPromises = [this.getUtilisateur()];
		Promise.all(tabPromises).then(() =>{
		});
	},
	methods: {

		getUtilisateur(){
			return new Promise((resolve, reject) => {
				fetch("http://localhost:3004/utilisateurs")
				.then(reponse => reponse.json())
				.then(data => {
					this.listeUtilisateurs = data ; 
					resolve(true);
				})
			});
		},

		login(){
			fetch(`http://localhost:3004/utilisateurs?email=${this.email}&password=${this.password}`)
			.then(response => response.json())
			.then(data => {
				if(data[0] != undefined){
					this.error = false;
					this.$parent.connected = true;
					this.$parent.utilisateur = data[0];
					this.$parent.tab = 0;
				}
				else{
					this.error = true;
				}
			})
		}
		
	}
}
</script>

<template>
	<div class="border border-dark mb-3 p-3 bg-light">
		<h3 class="mb-4">Se connecter <i class="bi bi-box-arrow-in-right"></i></h3>

		<input type="email" class="form-control mb-3" placeholder="votre@email.fr" v-model="email" />

		<input type="password" class="form-control mb-3" placeholder="password" v-model="password"/>

		<button class="btn btn-success mb-3" @click="login()">Connexion</button>

		<div v-show="error" class="alert alert-danger">Email et/ou mot de passe incorrect.</div>

	</div>
</template>
