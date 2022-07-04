<script>
	import { getFirestore, collection, getDocs, addDoc, query, orderBy } from 'firebase/firestore';
	import { initializeApp } from 'firebase/app';
	import firebaseConfig from './firebaseConfig';
	import Modal from './components/Modal.vue';
	import Search from './components/Search.vue';
	import youtubeConfig from './youtubeConfig';

	const app = initializeApp(firebaseConfig);
	const db = getFirestore(app);
	function getIdVideo(data) {
		if (!data) {
			return;
		}

		const regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#&?]*).*/;
		const match = data.match(regExp);
		return (match&&match[7].length==11) ? match[7] : false;
	};

	export default {
		components: {
			Search,
			Modal,
		},
		name: 'App',
		data() {
			return {
				modalActive : false,
				videoList : [],
				modalEmbed : null,
				modalTitle : null,
				modalDescription : null,
				getIdVideo: getIdVideo(),
			}
		},
		methods: {
			toggleModal(video) {
				this.modalActive = !this.modalActive;
				if (this.modalActive) {
					this.modalEmbed = `https://www.youtube.com/embed/${video.id}`;
					this.modalTitle = video.title;
					this.modalDescription = video.description;
				} else {
					this.modalEmbed = null;
					this.modalTitle = null;
					this.modalDescription = null;
				}
			},

			async addVideo(data) {
				try {
					const videoId = getIdVideo(data);

					if (videoId) {
						const response = await fetch(`https://www.googleapis.com/youtube/v3/videos?id=${videoId}&key=${youtubeConfig.apiKey}&part=snippet`);

						const info = await response.json();

						await addDoc(collection(db, 'prueba'), {
							id : videoId,
							url : data,
							thumbnail : info.items[0].snippet.thumbnails.medium.url,
							date : new Date(),
							title : info.items[0].snippet.title,
							description : info.items[0].snippet.description,
						});

						this.getVideoList();
					} else {
						throw new Error('url inválida');
					}
				} catch(e) {
					console.log(e.message);
				}
			},
			async getVideoList() {
				const videosCollection = collection(db, 'prueba');
				const listDocs = await getDocs(query(videosCollection, orderBy('date' , 'desc')));
				this.videoList = listDocs.docs.map(doc => doc.data());
			}
		},
		async mounted() {
			this.getVideoList();
			/*
			const db = getFirestore(app);
			const unsub = onSnapshot(doc(db, 'prueba', 'video'), (doc) => {
				console.log("Current data: ", doc.data());
			});
			*/
		},
	}
</script>
<template>
	<header>
		<title>Prueba</title>
	</header>
	<main>
		<div class="container">
			<div class="row justify-content-md-center">
				<h4>Añadir nuevo video</h4>
			</div>
			<form>
				<div class="form-group">
					<Search @addVideo="addVideo"/>
				</div>
			</form>
			<div class="col-12" v-for="video in videoList" :key="video.id">
				<img
					:src="video.thumbnail"
					class="thumbnail rounded float-left"
					@click="toggleModal(video)"
				/>
			</div>
		</div>
		<Modal
			:modalActive="modalActive"
			:modalTitle="modalTitle"
			:modalDescription="modalDescription"
			:modalEmbed="modalEmbed"
			@close="toggleModal"
		/>
	</main>
</template>

<style>
.thumbnail {
	margin-left: 2rem;
	margin-top: 2rem;
	cursor: pointer;
}
</style>


