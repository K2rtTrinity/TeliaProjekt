<template>
  <div id="app">
      <h1>Isikute nimekiri</h1>
      <p>Sorteerimiseks vajuta veeru pealkirjal!</p>
      <table style="margin: auto;">
      <thead>
        <tr>
          <th class="sort" @click="sortBy('name')">Nimi</th>
          <th class="sort" @click="sortBy('email')">Email</th>
          <th class="sort" @click="sortBy('birthday')">Sünnipäev</th>
          <th class="sort" @click="sortBy('pet')">Lemmikloom</th>
          <th>Märkmed</th>
          <th>Tegevused</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="person in persons" :key="person.id">
          <td>{{ person.name }}</td>
          <td>{{ person.email }}</td>
          <td>{{ person.birthday }}</td>
          <td>{{ person.pet }}</td>
          <td>{{ person.note }}</td>
          <td>
            <button @click="startEdit(person)">Muuda</button>
            <button @click="deletePerson(person.id)">Kustuta</button>
          </td>
        </tr>
      </tbody>
    </table>
    <h2>{{ isEditing ? 'Muuda isikut' : 'Lisa uus isik' }}</h2>
    <form @submit.prevent="isEditing ? updatePerson() : addPerson()">
      <input type="text" v-model="newPerson.name" placeholder="Nimi" required />
      <input type="email" v-model="newPerson.email" placeholder="E-post" required />
      <input type="date" v-model="newPerson.birthday" placeholder="Sünnipäev" required />
      <input type="text" v-model="newPerson.pet" placeholder="Lemmikloom" required />
      <input type="text" v-model="newPerson.note" placeholder="Märkmed" required />
      <button type="submit">{{ isEditing ? 'Salvesta' : 'Lisa' }}</button>
    </form>

    </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data() {
    return {
      persons: [],
      newPerson: {
        name: '',
        email: '',
        birthday: '',
        pet: '',
        note: ''
      },
      isEditing: false
    }
  },
   mounted() {
     this.loadPersons()
   },
   methods: {
     loadPersons() {
           axios.get('http://localhost:8080/api/persons')
             .then(response => {
               this.persons = response.data
             })
             .catch(error => {
               console.error("Viga andmete laadimisel:", error)
             })
     },
     addPerson() {
       axios.post('http://localhost:8080/api/persons', this.newPerson)
         .then(response => {
           this.persons.push(response.data)
           this.newPerson = { name: '', email: '', birthday: '', pet: '', note: ''}
         })
         .catch(error => {
           console.error("Viga isiku lisamisel:", error)
         })
     },
     deletePerson(id) {
        axios.delete(`http://localhost:8080/api/persons/${id}`)
          .then(() => {
            this.persons = this.persons.filter(person => person.id !== id)
          })
          .catch(error => {
            console.error("Viga isiku kustutamisel:", error)
          })
      },
      startEdit(person) {
        this.newPerson = { ...person }
        this.isEditing = true
      },
      updatePerson() {
        axios.put(`http://localhost:8080/api/persons/${this.newPerson.id}`, this.newPerson)
          .then(response => {
            const index = this.persons.findIndex(p => p.id === this.newPerson.id)
            if (index !== -1) {
            this.persons.splice(index, 1, response.data)
            }
            this.newPerson = { name: '', email: '', birthday: '', pet: '', note: '' }
            this.isEditing = false
          })
          .catch(error => {
            console.error("Viga isiku muutmisel:", error)
          })
      },
      sortBy(key) {
        this.persons.sort((a, b) => {
          if (a[key] && b[key]) {
            return a[key].localeCompare(b[key])
          }
          return 0
        })
      }
    }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
/* tabel */
table {
  border-collapse: collapse;
  background-color: #f9f9f9;
  border-radius: 10px;
  overflow: hidden;
}

th, td {
  padding: 12px 20px;
  border-bottom: 1px solid #ddd;
}

th {
  background-color: #8e44ad;
  color: white;
  cursor: pointer;

}
th.sort:hover {
  background-color: #732d91;
  transform: scale(1.03);
}

tr:hover {
  background-color: #f1e6f8;
}

p {
  margin-top: 10px; 
  font-size: 18px; 
  color: #732d91;
}

/* muutmine, kustutamine, lisamine*/
form {
  margin-top: 20px;
}

input, textarea {
  padding: 8px;
  margin: 5px;
  border-radius: 6px;
  border: 1px solid #ccc;
  width: 250px;
}

/* nupud */
button {
  background-color: #8e44ad;
  color: white;
  padding: 10px 16px;
  margin: 5px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
}

button:hover {
  background-color: #732d91;
  transform: scale(1.05);
}
</style>
