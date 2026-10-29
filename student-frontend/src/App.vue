
<script setup>
import { ref, onMounted } from "vue";

const API = "http://localhost:8080/api/student";

const students = ref([]);
const name = ref("");
const department = ref("");
const year = ref("");
const editId = ref(null);

async function loadStudents() {
  const res = await fetch(API);
  students.value = await res.json();
}

async function createStudent() {
  const student = {
    name: name.value,
    department: department.value,
    year: Number(year.value)
  };

  await fetch(API, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(student)
  });

  clearForm();
  await loadStudents();
}

async function updateStudent(s = null) {
  if (s !== null) {
    editId.value = s.id;
    name.value = s.name;
    department.value = s.department;
    year.value = s.year;
    return;
  }

  const student = {
    name: name.value,
    department: department.value,
    year: Number(year.value)
  };

  await fetch(API + "/" + editId.value, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(student)
  });

  clearForm();
  await loadStudents();
}

async function deleteStudent(id) {
  await fetch(API + "/" + id, {
    method: "DELETE"
  });

  await loadStudents();
}

function clearForm() {
  name.value = "";
  department.value = "";
  year.value = "";
  editId.value = null;
}

onMounted(loadStudents);
</script>

<template>
  <h1>Student CRUD</h1>

  <form @submit.prevent="editId === null ? createStudent() : updateStudent()">
    <input v-model="name" placeholder="Name" required />

    <input v-model="department" placeholder="Department" required />

    <input v-model="year" type="number" placeholder="Year" required />

    <button type="submit">
      {{ editId === null ? "Add Student" : "Update Student" }}
    </button>
  </form>

  <h2>Student List</h2>

  <table border="1">
    <thead>
      <tr>
        <th>Name</th>
        <th>Department</th>
        <th>Year</th>
        <th>Actions</th>
      </tr>
    </thead>

    <tbody>
      <tr v-for="s in students" :key="s.id">
        <td>{{ s.name }}</td>
        <td>{{ s.department }}</td>
        <td>{{ s.year }}</td>
        <td>
          <button @click="updateStudent(s)">Edit</button>
          <button @click="deleteStudent(s.id)">Delete</button>
        </td>
      </tr>
    </tbody>
  </table>
</template>
