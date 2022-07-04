<script>
export default {
	name: 'Search',
	data() {
		return {
			inputValue : null,
		}
	},
	methods: {
		addVideo() {
			this.$emit('addVideo', this.inputValue);
			this.inputValue = null;
		}
	}
}
</script>
<template>
	<div class="input-group mb-3">
		<input
			class="form-control"
			placeholder="Añadir"
			v-model="inputValue"
		/>
		<div class="input-group-append">
			<button
				type="button"
				class="btn btn-primary"
				v-on:click="addVideo"
				v-if="inputValue"
			>Añadir</button>
		</div>
	</div>
</template>
