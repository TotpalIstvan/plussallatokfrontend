<template>
  <div id="app">
    <table>
      <thead>
        <th>ID</th>
        <th>Név</th>
        <th>Operációk</th>
      </thead>
      <tbody>
        <tr v-for="plussallat in plussallatok" :key="plussallat.id">
          <td>{{plussallat.id}}</td>
          <td>{{plussallat.nev}}</td>
          <td>
            <button @click="editPlussallat(plussallat.id)" :disabled="saving">Szerkesztés</button>
            <button @click="deletePlussallat(plussallat.id)" :disabled="saving">Törlés</button>
          </td>
        </tr>
        <tr>
          <td></td>
          <td><input type="text" v-model="plussallat.nev"></td>
          <td>
            <button @click="createPlussallat" :disabled="saving" v-if="!edit">Hozzáadás</button>
            <button @click="savePlussallat" :disabled="saving" v-if="edit">Mentés</button>
            <button @click="resetForm" :disabled="saving" v-if="edit">Mégse</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'App',
  
  data() {
    return {
      plussallatok: [],
      plussallat: {
        id: null,
        nev: ""
      },
      edit: false,
      saving: false
    }
  },
  methods: {
    async listPlussallatok(){
      let response = await fetch('http://127.0.0.1:8000/api/plussallatok')
      let data = await response.json()
      this.plussallatok = data
    },
    async createPlussallat(){
      this.saving = true
      await fetch('http://127.0.0.1:8000/api/plussallatok', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        },
        body: JSON.stringify(this.plussallat) 
      })
      await this.listPlussallatok()
      this.saving = false
      this.resetForm()
    },
    async deletePlussallat(id){
      await fetch(`http://127.0.0.1:8000/api/plussallatok/${id}`, {
        method: 'DELETE',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        }
      })
      await this.listPlussallatok()
    },
    resetForm(){
      this.plussallat = {
        id: null,
        nev: ""
      }
      this.edit = false
    },
    async editPlussallat(id){
      this.edit = true
      let response = await fetch(`http://127.0.0.1:8000/api/plussallatok/${id}`)
      let data = await response.json()
      this.plussallat = {...data}
    },
    
    async savePlussallat(){
      this.saving = true
      await fetch(`http://127.0.0.1:8000/api/plussallatok/${this.plussallat.id}`, {
        method: 'PATCH',
        headers: {
          'Content-Type' : 'application/json',
          'Accept' : 'application/json'
        },
        body: JSON.stringify(this.plussallat)
      })
      await this.listPlussallatok()
      this.saving = false
      this.resetForm()
    }
  },
  mounted(){
    this.listPlussallatok()
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
</style>