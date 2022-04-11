<script>
	import FilsActualites from './components/FilsActualites.vue'
	import Connexion from './components/Connexion.vue'
	import CreerProfil from './components/CreerProfil.vue'

	export default {
		components: {
			FilsActualites, Connexion, CreerProfil
		},
		data(){
			return {
				tab : 0,
				connected: false,
				utilisateur: {}
			}
		},
		methods: {
			deconnexion(){
				this.connected = false;
				this.utilisateur = {};
			}
		}
	}

</script>

<template>
  <main class="container-md">
		<div class="row my-1">
			<h1>MyNetWork <i class="bi bi-facebook"></i></h1>
		</div>
		<div class="row">
			<div class="col-md-2">
				<nav>
					<ul class="list-group text-center">
						<button @click="tab = 0" class="list-group-item" :class="{ active: tab == 0 }"><i class="bi bi-house-fill"></i> Fils d'actualité</button>
						<button @click="tab = 1" class="list-group-item" :class="{ active: tab == 1 }" v-show="!connected"><i class="bi bi-person-plus-fill"></i> Créer un profil</button>
						<button @click="tab = 2" class="list-group-item" :class="{ active: tab == 2 }" v-show="!connected"><i class="bi bi-box-arrow-in-right"></i> Se connecter</button>
						<button @click="deconnexion()" class="list-group-item" :class="{ active: tab == 3 }" v-show="connected"><i class="bi bi-door-open-fill"></i> Déconnexion</button>
					</ul>
				</nav>
			</div>
			<div class="col-md-10 mb-5">
				<div v-show="tab == 0"><FilsActualites ref="filsActualites" :connected="connected" /></div>
				<div v-show="tab == 1"><CreerProfil ref="creerProfil" /></div>
				<div v-show="tab == 2"><Connexion ref="connexion" /></div>
			</div>
		</div>
  </main>
</template>

<style>
/* @import './assets/base.css'; */
</style>
