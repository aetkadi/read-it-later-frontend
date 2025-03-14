<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute();
const router = useRouter();

const review = ref({ bookId: '', review: '' });
const errorMessage = ref('');

// Define bookId
onMounted(() => {
  if (route.params.id) {
    review.value.bookId = route.params.id;
  } else {
    console.error("Route params.id is undefined");
  }
});

// Submit form
async function submitForm() {
  errorMessage.value = '';
  try {
    const response = await axios.post('/api/review', review.value);
    console.log("Response from server:", response.data);

    review.value.review = '';
    goBack()
  } catch (error) {
    if (error.response?.data) {
      errorMessage.value = error.response.data.message || 'An error occurred';
    } else {
      errorMessage.value = 'Network error, please try again later.';
    }
  }
}

// Change the textbox size
function autoResize(event) {
  const textarea = event.target;
  textarea.style.height = 'auto';
  textarea.style.height = `${textarea.scrollHeight}px`;
}

function goBack() {
  router.back();
}
</script>

<template>
  <div class="create-review">
    <h2>Add Review</h2>
    <form @submit.prevent="submitForm">
      <div>
        <label for="review">Write a review</label>
        <textarea
            id="review"
            v-model="review.review"
            required
            class="review"
            rows="1"
            @input="autoResize"
        ></textarea>
      </div>
      <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
      <button type="submit">Add Review</button>
      <button type="button" @click="goBack" class="cancel">Cancel</button>
    </form>
  </div>
</template>

<style scoped>
.create-review {
  max-width: 80%;
  margin: 0 auto;
  padding: 1rem;
  border: 1px solid var(--color-pink-lavender-darker);
  border-radius: 4px;
}

.create-review h2 {
  text-align: center;
}

.create-review div {
  margin-bottom: 1rem;
}

.create-review label {
  display: block;
  margin-bottom: 0.5rem;
}

textarea.review {
  width: 100%;
  resize: none;
  overflow: hidden;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

button.cancel {
  background-color: var(--color-pink-lavender-darker);
  color: #333;
  border: 1px solid var(--color-pink-lavender-darker);
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 1rem;
}

button.cancel:hover {
  color: #e0e0e0;
  background-color: hsla(278, 100%, 34%, 0.2);
}

@media (min-width: 1024px) {
  .create-review {
    width: 50%;
  }
}
</style>
