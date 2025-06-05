<script setup>
// Pour réinitialiser le formulaire
import {onMounted, reactive} from "vue";
import {doAjaxRequest} from "@/api";

const produitVide = {
  nom: "",
};
const url = "https://springajax.herokuapp.com/api"

let data = reactive({
  listeProduits: [],
  listeCategories:[],
});

function showError(error) {
  console.log("Erreur : status %d", error.status)
  console.log(error.body);
  alert(error.message);
}

function afficherProduits(lien) {
  doAjaxRequest(lien)
      .then((json) => {
        data.listeProduits = json._embedded.produits;
      })
      .catch(showError);
}


function recuperarcategorie() {
  doAjaxRequest(url+"/categories?sort=code,desc")
      .then((json) => {
        data.listeCategories = json._embedded.categories;
      })
      .catch(showError);
}

onMounted(recuperarcategorie)
</script>


<template>
  <main>
    <div>
      <table>
        <caption>Liste des Produits</caption>
        <select @change="afficherProduits($event.target.value)">
          <option value="">--Choisir une catégorie--</option>
          <option v-for="categorie in data.listeCategories" :value="categorie._links.produits.href">{{ categorie.libelle }}</option>
        </select>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandes</th>
        </tr>
        <tr v-if="data.listeProduits.length === 0">
          <td colspan="4">Veuillez patienter, chargement des catégories...</td>
        </tr>
        <tr v-for="produit in data.listeProduits" :key="produit.reference">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
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