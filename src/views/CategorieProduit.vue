<template>
    <main>
        <div>
            <h1> Les Categories de produits</h1>
        </div>
        <div>
            <select v-model="selected" @change="chargeProduitsCategorie">
                <option disabled value="">Choisissez une catégorie</option>
                <option v-for="categorie in listeCategories" :key="categorie.code" :value="categorie.code">
                    {{ categorie.libelle }}
                </option>
            </select>
            <table>
                <caption>Les produits</caption>
                <tr>
                    <th>nom</th>
                    <th>prix</th>
                    <th>stock</th>
                    <th>commandzs</th>
                </tr>
                <!-- Si le tableau des catégories est vide -->
                <tr v-if="listeProduits.length === 0">
                    <td colspan="4">Veuillez patienter, chargement des produits...</td>
                </tr>
                <!-- Si le tableau des catégories n'est pas vide -->
                <tr v-for="produit in listeProduits" :key="produit.nom">
                    <td>{{ produit.nom }}</td>
                    <td>{{ produit.prixUnitaire }}</td>
                    <td>{{ produit.unitesEnStock }}</td>
                    <td>{{ produit.unitesCommandees }}</td>
                </tr>
            </table>
        </div>
    </main>
</template>
<script setup>
import { doAjaxRequest } from '../api';
import { onMounted, ref } from "vue";
let listeProduits = ref([]);
let listeCategories = ref([]);
let selected = ref("");

function showError(error) {
    console.log("Erreur : status %d", error.status);
    console.log(error.body);
    alert(error.message);
}

function chargeCategories() {
    // Appel à l'API pour avoir la liste des catégories
    // Trié par code, descendant
    // Verbe HTTP GET par défaut
    doAjaxRequest("/api/categories?sort=code,desc")
        .then((json) => {
            listeCategories.value = json._embedded.categories;
            console.log(listeCategories)
        })
        .catch(showError);
}

function chargeProduits() {
    doAjaxRequest(`/api/categories/${selected.value}/produits`)
        .then((json) => {
            listeProduits.value = json._embedded.produits;
            console.log(listeProduits)
        })
        .catch(showError);
}



function chargeProduitsCategorie() {
    chargeProduits();
}


onMounted(chargeCategories);
</script>
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