<!-- TaskForm.vue -->

<template>
  <div class="card">
    <div class="card-body">
      <h5 class="card-title">Task List</h5>

      <!-- Upper Part - Input Field -->
      <div class="mb-3">
        <input
          class="form-control"
          v-model="newTask"
          @keyup.enter="addTask"
          placeholder="Enter a new task"
        />
      </div>

      <div class="mb-3">
        <textarea
          class="form-control"
          v-model="notes"
          placeholder="Enter notes"
        ></textarea>
      </div>

      <button class="btn btn-primary" @click="addToTaskList">
        Add to List
      </button>

      <!-- Lower Part - Table -->
      <table class="table">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col" class="text-start">Task</th>
            <th scope="col" class="text-start">Notes</th>
            <th scope="col">Check</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(task, index) in tasks" :key="index">
            <th scope="row">{{ index + 1 }}</th>
            <td class="text-start">{{ task.title }}</td>
            <td class="text-start">{{ task.notes }}</td>
            <td>
              <input type="checkbox" @change="deleteTask(index)" />
            </td>
          </tr>
        </tbody>
      </table>

      <!-- Input fields for email customization -->
      <div class="mb-3">
        <label for="recipientEmail" class="form-label"
          >Recipient's Email:</label
        >
        <input
          type="email"
          name="email"
          class="form-control"
          v-model="recipientEmail"
          placeholder="Enter recipient's email"
          required
        />
      </div>

      <div class="mb-3">
        <label for="emailSubject" class="form-label">Email Subject:</label>
        <input
          class="form-control"
          v-model="emailSubject"
          placeholder="Enter email subject"
        />
      </div>

      <!-- Buttons for local storage and email -->
      <div class="d-flex justify-content-between">
        <button class="btn btn-primary" @click="saveToLocalStorage">
          Save to Local Storage
        </button>
        <button class="btn btn-success" @click="sendAsEmail">
          Send as Email
        </button>
      </div>
    </div>
  </div>
</template>

<script>
/* import Datepicker from 'vue-datepicker' */

export default {
  /*   components: {
    Datepicker,
  }, */
  data() {
    return {
      newTask: "",
      notes: "",
      tasks: [],
      recipientEmail: "",
      emailSubject: "",
    };
  },
  methods: {
    addTask() {
      if (this.newTask.trim() !== "") {
        this.tasks.push({ title: this.newTask, notes: this.notes });
        this.newTask = "";
        this.notes = "";
        this.saveToLocalStorage(); // Save to local storage after adding a task
      }
    },
    addToTaskList() {
      this.addTask();
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveToLocalStorage(); // Save to local storage after deleting a task
    },
    saveToLocalStorage() {
      // Save the tasks to local storage
      localStorage.setItem("taskList", JSON.stringify(this.tasks));
    },
    sendAsEmail() {
  const { recipientEmail, emailSubject, notes, dueDate } = this;

  const emailData = {
    to: recipientEmail,
    subject: emailSubject,
    html: `<head>
              <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
            </head>
            <h2>Task List</h2>
            <table class="table">
              <thead>
                <tr>
                  <th>Task No</th>
                  <th>Task</th>
                  <th>Notes</th>
                </tr>
              </thead>
              <tbody>
                ${this.tasks.map((task, index) => 
                  `<tr>
                    <td>${index + 1}</td>
                    <td>${task.title}</td>
                    <td>${task.notes}</td>
                  </tr>`).join("")}
              </tbody>
            </table>`,
  };

  fetch("http://localhost:3001/send-email", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(emailData),
  })
    .then((response) => response.json())
    .then((data) => {
      if (data.success) {
        console.log(data.message);
        alert("Tasks sent as email!");

        // Clear input fields after successful email send
        this.recipientEmail = "";
        this.emailSubject = "";
      } else {
        console.error("Error sending email:", data.error);
        alert("Error sending email");
      }
    })
    .catch((error) => {
      console.error("Error sending email:", error);
      alert("Error sending email");
    });
},

  },
  mounted() {
    // Load tasks from local storage when the component is mounted
    const storedTasks = localStorage.getItem("taskList");
    if (storedTasks) {
      this.tasks = JSON.parse(storedTasks);
    }
  },
};
</script>
