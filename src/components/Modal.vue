<template>
	<transition name="modal-animation">
		<div v-show="modalActive" class="modal">
		<transition name="modal-animation-inner">
			<div v-show="modalActive" class="modal-inner">
				<i @click="close" class="far fa-times-circle"></i>
					<div class="modal-content">
					<div class="row modal-row">
						<div class="col-6">
							<iframe
								:src="modalEmbed"
								frameborder="0"
							/>
						</div>
						<div class="col-6">
							<h3>{{ modalTitle }}</h3>
							<p>{{ modalDescription }}</p>
						</div>
					</div>
				</div>
				<button @click="close" type="button">Cerrar</button>
			</div>
		</transition>
		</div>
	</transition>
</template>

<script>
export default {
	props : {
		modalActive : {
			type : Boolean,
		},
		modalTitle : {
			type : String,
			default : 'Título',
		},
		modalDescription : {
			type : String,
			default : '',
		},
		modalEmbed : {
			type : String,
			default : '',
		}
	},
	setup(props, { emit }) {
		const close = () => {
			emit('close');
		};

		return { close };
	},
};
</script>

<style lang="scss" scoped>
	.modal-animation-enter-active,
	.modal-animation-leave-active {
		transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
	}
	.modal-animation-enter-from,
	.modal-animation-leave-to {
		opacity: 0;
	}
	.modal-animation-inner-enter-active {
		transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;
	}
	.modal-animation-inner-leave-active {
		transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
	}
	.modal-animation-inner-enter-from {
		opacity: 0;
		transform: scale(0.8);
	}
	.modal-animation-inner-leave-to {
		transform: scale(0.8);
	}
	.modal {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100vh;
		width: 100vw;
		position: fixed;
		top: 0;
		left: 0;
		background-color: rgba(255, 255, 255, 0.7);
	.modal-inner {
		position: relative;
		max-width: 640px;
		max-height: 400px;
		width: 100%;
		box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
		background-color: #fff;
		padding: 64px 16px;
		i {
			position: absolute;
			top: 15px;
			right: 15px;
			font-size: 1rem;
			cursor: pointer;
			&:hover {
				color: crimson;
			}
		}
		button {
			padding: 0.5rem 1rem;
			border: none;
			font-size: 1rem;
			background-color: crimson;
			color: #fff;
			cursor: pointer;
			float : right;
			margin-top: 3rem;
		}
	}
	.modal-row {
		max-height: 200px;
    	overflow-y: scroll;
	}
}
</style>
