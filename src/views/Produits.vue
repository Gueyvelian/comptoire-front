<script setup>
// Pour réinitialiser le formulaire
import {onMounted, reactive} from "vue";
import {doAjaxRequest} from "@/api";

const produitVide = {
  nom: "",
};
const url = "https://springajax.herokuapp.com/api/produits"

let data = reactive({
  listeProduits: [],
  listeLinks: [],
  page: 0,
  pageTotal: ""
});

function showError(error) {
  console.log("Erreur : status %d", error.status)
  console.log(error.body);
  alert(error.message);
}

function afficherProduits() {
 getUrlProduits(url+"?page=0&size=5")
}

function getUrlProduits(urlProduits){
  doAjaxRequest(urlProduits)
      .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.listeLinks = json._links;
        data.page = json.page.number + 1;
        data.pageTotal = json.page.totalPages;
      })
      .catch(showError);
}

onMounted(afficherProduits);

function affichageDebut(){
  getUrlProduits(data.listeLinks.first.href)
}

function afficherPresedent(){
  getUrlProduits(data.listeLinks.prev.href)
}

function afficherSuivant(){
  getUrlProduits(data.listeLinks.next.href)
}

function afficherFin(){
  getUrlProduits(data.listeLinks.last.href)
}

</script>


<template>
  <main>
    <div>
      <table>
        <caption>Liste des Produits - Page {{ data.page }}/{{data.pageTotal}}</caption>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandes</th>
        </tr>
        <!-- Si le tableau des catégories est vide -->
        <tr v-if="data.listeProduits.length === 0">
          <td colspan="4">Veuillez patienter, chargement des catégories...</td>
        </tr>
        <!-- Si le tableau des catégories n'est pas vide -->
        <tr v-for="produit in data.listeProduits" :key="produit.reference">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
        <td>
          <button @click="affichageDebut">Debut</button>
        </td>
        <td>
          <button @click="afficherPresedent">Precedent</button>
        </td>
        <td>
          <button @click="afficherSuivant">Suivant</button>
        </td>
       <td>
         <button @click="afficherFin">Fin</button>
       </td>
      </table>
    </div>
  </main>
</template>

<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #232623;
  color: rgb(255, 255, 255);
}
</style>