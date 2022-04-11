<script>
export default {
	props: ['connected'],
	data(){
		return{
			listeArticles: [],
			listeUtilisateurs: [],

			article: {
				contenu: '',
				urlImgArticle: '',
			}
		}
	},
	mounted(){
		this.loadData();
	},
	methods: {

		loadData(){
			let tabPromises = [this.getUtilisateur(), this.getArticles()];
			Promise.all(tabPromises).then(() =>{
				this.getUrlUtilisateur()
			});
		},

		getArticles(){
			return new Promise((resolve, reject) => {
				fetch("http://localhost:3004/articles")
				.then(reponse => reponse.json())
				.then(data => {
					this.listeArticles = data ; 
					resolve(true);
				})
			});
		},

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

		formatDate(date){
			let d = new Date(date);
			let month = d.getMonth
			return d.getDate() + "/" + ("0" + (d.getMonth() + 1)).slice(-2) + "/" +d.getFullYear(); 
		},

		formatHeure(date){
			let d = new Date(date);
			return d.getHours() + "h" + d.getMinutes();
		},

		getUrlUtilisateur(){
			for (let i = 0; i < this.listeArticles.length; i++) {
				let u = this.listeUtilisateurs.find(u => u.id == this.listeArticles[i].idUtilisateur);
				this.listeArticles[i].urlImgProfil = u.urlImgProfil;
			}
		},

		addArticle(){
			let scope = this;
			let date = new Date();
			let newArticle = {
				contenu: scope.article.contenu,
				urlImgArticle: scope.article.urlImgArticle,
				likes: [],
				pseudo: scope.$parent.utilisateur.pseudo,
				idUtilisateur: scope.$parent.utilisateur.id,
				date: date.getTime(),
				commentaires: []
			}
			fetch("http://localhost:3004/articles", {
				method: "post", 
				headers : {"content-type": "application/json"} , 
				body : JSON.stringify(newArticle)
			})
			.then(reponse => reponse.json())
			.then(data => {
					let tabPromises = [this.getArticles()];
					Promise.all(tabPromises).then(() =>{
						this.getUrlUtilisateur()
					});
					scope.article.contenu = "";
					scope.article.urlImgArticle = "";
			})
		},

		addCommentaire(article){
			let scope = this;
			let date = new Date();
			let content = document.getElementById(`addCom-${article.id}`);

			if(content.value != ""){
				let newCommentaire = {
					contenu: content.value,
					pseudo: scope.$parent.utilisateur.pseudo,
					dt: date.getTime()
				}

				article.commentaires.push(newCommentaire)
				delete article.urlImgProfil;

				fetch(`http://localhost:3004/articles/${article.id}`, {
					method: "put", 
					headers : {"content-type": "application/json"} , 
					body : JSON.stringify(article)
				})
				.then(reponse => reponse.json())
				.then(data => {
						let tabPromises = [this.getArticles()];
						Promise.all(tabPromises).then(() =>{
							this.getUrlUtilisateur()
						});
						content.value = "";
				})
			}
			
		},

		likeArticle(article){

			let find = article.likes.find(u => u == this.$parent.utilisateur.id);

			if(find == undefined){
				article.likes.push(this.$parent.utilisateur.id)
			}
			else{
				article.likes = article.likes.filter((l) => l !== this.$parent.utilisateur.id);
			}
			delete article.urlImgProfil;

			fetch(`http://localhost:3004/articles/${article.id}`, {
				method: "put", 
				headers : {"content-type": "application/json"} , 
				body : JSON.stringify(article)
			})
			.then(reponse => reponse.json())
			.then(data => {
					let tabPromises = [this.getArticles()];
					Promise.all(tabPromises).then(() =>{
						this.getUrlUtilisateur()
					});
					content.value = "";
			})
		},

		isLikeByUser(article){
			let find = article.likes.find(u => u == this.$parent.utilisateur.id);
			if(find == undefined){
				return false
			}
			else return true;
		}

	}
}
</script>

<template>
	<div>

		<div v-show="connected" class="border border-dark mb-3 px-3 bg-light">
			<h3 class="mt-3 mb-4 ms-1">Quoi de neuf ? <i class="bi bi-house-fill"></i></h3>

			<textarea v-model="article.contenu" class="form-control mb-3" rows="5" placeholder="Laisser un nouveau post" autocomplete="off"></textarea>

			<input v-model="article.urlImgArticle" class="form-control mb-3" type="text" placeholder="url de votre image - taille conseillée 1000x300" autocomplete="off"/>

			<div @click="addArticle()" class="btn btn-success w-100 mb-3">Laisser un nouveau post</div>
			
		</div>

		<div v-if="listeArticles.length > 0 && listeUtilisateurs.length > 0">
			<div v-for="e in listeArticles" class="border border-dark mb-3">
				
				<div class="d-flex py-1 align-items-center justify-content-between bg-light">
					
					<div class="ps-2 d-flex align-items-center">
						<h3 class="mb-1">{{e.pseudo}}</h3>
						<img :src="e.urlImgProfil" class="imgProfil ms-2">
					</div>
					
					<div class="text-end pe-2">posté le {{ formatDate(e.date) }} à {{ formatHeure(e.date) }}</div>
				</div>

				<div class="row">
					<img :src="e.urlImgArticle" alt="Image de l'article">
				</div>

				<div class="row my-2 mx-3" v-html="e.contenu"></div>

				<div class="d-flex py-1 px-3 bg-light align-items-center">
					<div class="d-flex align-items-center me-3">
						<i class="bi bi-chat-left-dots"></i> 
						<span class="ms-1 badge bg-success">{{e.commentaires.length}}</span>
					</div>
					<div class="d-flex align-items-center">
						<h5 v-show="connected"><i class="bi bi-hand-thumbs-up-fill" :class="{'text-primary': isLikeByUser(e)}" @click="likeArticle(e)"></i></h5>
						<h5 v-show="!connected"><i class="bi bi-hand-thumbs-up-fill"></i></h5>
						<span class="ms-1 badge bg-primary">{{e.likes.length}}</span>
					</div>
				</div>

				<div class="mb-3" v-show="e.commentaires.length > 0">
					<h4 class="px-3 mt-3">Commentaires :</h4>
					<div v-for="c in e.commentaires">

						<hr>
						<div class="d-flex align-content-center justify-content-between px-3">
							<div class="fw-bold">{{c.pseudo}}</div>
							<div>le {{ formatDate(c.dt) }} à {{ formatHeure(c.dt) }}</div>
						</div>
						<div class="px-3 mt-2">{{c.contenu}}</div>
						
					</div>
				</div>

				<hr v-show="connected">
				<div class="mb-3 px-3" v-show="connected">
					<h4 class=" mt-3">Ajouter un commentaires :</h4>
					<textarea :id='"addCom-" + e.id' class="form-control my-3" rows="3" placeholder="Laisser un commentaire"></textarea>

					<button @click="addCommentaire(e)" class="btn btn-outline-success">laisser un commentaire</button>
				</div>

			</div>

		</div>
	</div>
</template>

<style scoped>
	.imgProfil{
		width: 40px;
		border-radius: 50%;
	}
</style>