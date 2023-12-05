<template>
  <div>
    <form @submit.prevent="sendEmail">
      <div class="form-group">
        <label for="email">Email:</label>
        <input
          type="email"
          id="email"
          v-model="email"
          class="form-control"
          required
        />
      </div>

      <div class="form-group">
        <button type="submit" class="btn btn-primary">Send Email</button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: '',
    };
  },
  methods: {
    sendEmail() {
      // Here, you can implement the logic to send the email or handle the form submission
      // For this example, we'll simply log the email to the console.
      console.log('Email address submitted:', this.email);
    },
  },
};
</script>

<style scoped>
/* Add your CSS styles here */
</style>
