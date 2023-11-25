<template>
  <div>
    <input type="file" @change="handleFileChange" accept="image/*" />
    <button @click="uploadImage">Subir Imagen</button>
    <div v-if="imagePreview">
      <img :src="imagePreview" alt="Preview" style="max-width: 300px; max-height: 300px;" />
    </div>
    <div v-if="description">
      <h3>Descripción de la imagen:</h3>
      <p>{{ description }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      imageFile: null,
      imagePreview: null,
      description: null,
    };
  },
  methods: {
    handleFileChange({ target }) {
      this.imageFile = target.files[0];

      if (this.imageFile) {
        const reader = new FileReader();
        reader.onload = () => (this.imagePreview = reader.result);
        reader.readAsDataURL(this.imageFile);
      }
    },
    async uploadImage() {
      if (this.imageFile) {
        const formData = new FormData();
        formData.append('image', this.imageFile);

        try {
          const response = await fetch('https://huggingface.co/Sof22/image-caption-large-copy', {
            method: 'POST',
            body: formData,
          });

          if (response.ok) {
            const data = await response.json();
            this.description = data.description || 'No hay descripción disponible'; // Ajusta esto según la respuesta de tu API
          } else {
            console.error('Error al subir la imagen');
          }
        } catch (error) {
          console.error('Error de red:', error);
        }
      } else {
        console.error('Selecciona una imagen primero');
      }
    },
  },
};
</script>

<style scoped>
/* Estilos opcionales para el componente */
</style>