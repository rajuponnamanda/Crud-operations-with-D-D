<template>
  <div>
    <h2 class="text-end">
      <router-link to="/add" class="btn btn-dark m-2">
        <i class="bi bi-person-fill-add"></i> Add Students
      </router-link>
    </h2>

    <div class="m-3">
      <div class="row">
        <div v-for="(group, index) in studentGroups" :key="index" class="col-md-3 mb-3">
          <div class="card" :draggable="true" @dragstart="dragStart(index)" @dragover.prevent @drop="drop($event, index)">
            <div class="card-body">
              <h5 class="card-title">{{ group.title }}</h5>
              <p class="card-text" v-for="(student, idx) in group.students" :key="idx">
                Name: {{ student.name }}<br>
                Age: {{ group.age }}<br>
                Contact: {{ student.contact }}<br>
                Email: {{ student.email }}<br>
                <button class="btn btn-danger" @click="deleteStudent(group.id, idx)"><i class="bi bi-trash3-fill"></i></button>
                <router-link :to="'/edit/' + student.id" class="btn btn-primary ms-2" @click="updateStudent(student)"><i class="bi bi-pencil-square"></i></router-link>
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";
import axios from "axios";

const studentGroups = ref([
  { title: 'Age 1-18', age: '10', students: [] },
  { title: 'Age 19-25', age: '20', students: [] },
  { title: 'Age 25-45', age: '25', students: [] },
  { title: 'Age 45+', age: '45+', students: [] }
]);

const getStudentsData = async () => {
  try {
    const res = await axios.get('http://localhost:3000/users');
    res.data.forEach(student => {
      if (student.age >= 1 && student.age <= 18) {
        studentGroups.value[0].students.push({ ...student, age: studentGroups.value[0].age });
      } else if (student.age >= 19 && student.age <= 25) {
        studentGroups.value[1].students.push({ ...student, age: studentGroups.value[1].age });
      } else if (student.age >= 26 && student.age <= 45) {
        studentGroups.value[2].students.push({ ...student, age: studentGroups.value[2].age });
      } else {
        studentGroups.value[3].students.push({ ...student, age: studentGroups.value[3].age });
      }
    });
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};

onMounted(getStudentsData);

const deleteStudent = async (groupId, studentIdx) => {
  if (!window.confirm("Are You Sure ?")) {
    return;
  }

  try {
    await axios.delete(`http://localhost:3000/users/${studentGroups.value[groupId].students[studentIdx].id}`);
    studentGroups.value[groupId].students.splice(studentIdx, 1);
  } catch (error) {
    console.error("Error deleting student:", error);
  }
};

const updateStudent = async (student) => {
  // Here you can implement the logic to update the student details
  console.log("Updating student:", student);
};

let draggedItem = null;

const dragStart = (index) => {
  draggedItem = { groupIndex: index, students: studentGroups.value[index].students };
};

const drop = (event, toIndex) => {
  event.preventDefault();
  if (draggedItem === null) return;

  const { groupIndex, students } = draggedItem;
  const fromIndex = groupIndex;

  if (fromIndex !== -1 && fromIndex !== toIndex) {
    const [removed] = students.splice(0, 1);
    studentGroups.value[toIndex].students.push({ ...removed, age: studentGroups.value[toIndex].age });
  }

  draggedItem = null;
};
</script>

<style scoped>
.card {
  width: 18rem;
}
</style>
