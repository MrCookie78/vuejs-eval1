<script>
export default {
	data(){
		return{

			listeUtilisateurs: [],

			profilCreated: false,

			pseudo: "",
			email: "",
			password: "",
			confirm_password: "",
			url: "",

			error: {
				statut: false,
				pseudo: false,
				email: false,
				password: false,
				confirm_password: false,
				url: false,
				pseudoExiste: false,
				emailExiste: false,
			}

		}
	},
	computed: {

		emailValide(){
			let e = this.email.toLowerCase()
			.match(
				/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
			);
			return (e != null || this.email == "");
		},

		pseudoOk:function(){
			let i = false;
			if(this.pseudo == "" || this.pseudo.length >= 3){
				i = true;
			}
			return i;
		},

		pwOK: function(){
			let valide = false;
			let remplit = false;

			if( this.password != "" && this.confirm_password != "") remplit = true;

			if(remplit){
				if((this.password.length >= 6) && (this.confirm_password.length >= 6) && (this.password == this.confirm_password)) valide = true;
			}
			if(remplit) return valide;
			else return true;
		}

	},
	mounted(){
		this.getUtilisateurs();
	},
	methods: {

		getUtilisateurs(){
			fetch("http://localhost:3004/utilisateurs")
			.then(reponse => reponse.json())
			.then(data => {
				this.listeUtilisateurs = data ; 
			})
		},

		createProfil(){
			this.error.statut = false;
			this.error.pseudo = false;
			this.error.email = false;
			this.error.password = false;
			this.error.confirm_password = false;
			this.error.url = false;
			this.error.pseudoExiste = false;
			this.error.emailExiste = false;
			
			// On vérifie que tous les champs sont remplis
			if(this.pseudo == ""){this.error.pseudo = true; this.error.statut = true;} 
			if(this.email == ""){this.error.email = true; this.error.statut = true;} 
			if(this.password == ""){ this.error.password = true; this.error.statut = true;}
			if(this.confirm_password == ""){ this.error.confirm_password = true; this.error.statut = true;}
			if(this.url == ""){ this.error.url = true; this.error.statut = true;}

			// On verifie que le pseudo ou l'adresse email n'est pas deja utilisé :
			let u1 = this.listeUtilisateurs.find(u => u.pseudo == this.pseudo);
			let u2 = this.listeUtilisateurs.find(u => u.email == this.email);
			if(u1 != undefined){this.error.pseudoExiste = true; this.error.statut = true}
			if(u2 != undefined){this.error.emailExiste = true; this.error.statut = true}

			// Si il n'y a pas d'erreur dans le formulaire
			if(!this.error.statut){
				let newUtilisateur = {
					pseudo: this.pseudo,
					email: this.email,
					password: this.password,
					urlImgProfil: this.url
				}
				fetch("http://localhost:3004/utilisateurs", {
					method: "post", 
					headers : {"content-type": "application/json"} , 
					body : JSON.stringify(newUtilisateur)
				})
				.then(() => {
					this.profilCreated = true;
					this.$parent.$refs.filsActualites.loadData();
					setTimeout(() => {
						this.$parent.tab = 0;
					}, 5000);
				})
			}
		}

	}
}
</script>

<template>
	<div>
		<div class="border border-dark mb-3 p-3 bg-light" v-show="!profilCreated">
			<h3 class="mb-5">Créer un profil <i class="bi bi-person-plus-fill"></i></h3>

			<input type="text" class="form-control mb-3" placeholder="pseudo" v-model="pseudo" autocomplete="off" />

			<input type="email" class="form-control mb-3" placeholder="votre@email.fr" v-model="email" autocomplete="off" />

			<input type="password" class="form-control mb-3" placeholder="password" v-model="password" autocomplete="off" />

			<input type="password" class="form-control mb-3" placeholder="confirmer password" v-model="confirm_password" autocomplete="off" />

			<input type="text" class="form-control mb-3" placeholder="url de votre image de profil" v-model="url" autocomplete="off" />

			<button class="btn btn-success mb-3" @click="createProfil()" :disabled="!pseudoOk || !emailValide || !pwOK" >Créer un nouveau profil</button>

			<div class="alert alert-danger" v-show="!pseudoOk">
				Le pseudo doit être contenir au moins 3 caractère.
			</div>

			<div class="alert alert-danger" v-show="!emailValide">
				L'email doit être valide.
			</div>

			<div class="alert alert-danger" v-show="!pwOK">
				Les mot de passe doivent être identique et d'une taille supérieur à 6 caractères.
			</div>

			<div class="alert alert-danger" v-show="error.pseudo">
				Le pseudo ne peut pas être vide.
			</div>

			<div class="alert alert-danger" v-show="error.email">
				L'email ne peut pas être vide.
			</div>

			<div class="alert alert-danger" v-show="error.password">
				Le password ne peut pas être vide.
			</div>

			<div class="alert alert-danger" v-show="error.confirm_password">
				Le confirm_password ne peut pas être vide.
			</div>

			<div class="alert alert-danger" v-show="error.url">
				L'url ne peut pas être vide.
			</div>

			<div class="alert alert-danger" v-show="error.pseudoExiste">
				Le pseudo renseigné est déjà utilisé.
			</div>

			<div class="alert alert-danger" v-show="error.emailExiste">
				L'email renseignée est déjà utilisée.
			</div>

		</div>

		<div v-show="profilCreated" class="alert alert-success text-center">
			Le profil a bien été créé. Vous allez être redirigé dans 5s.
		</div>
	
	</div>
</template>
