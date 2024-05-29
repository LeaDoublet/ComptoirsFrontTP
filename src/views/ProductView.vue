<template>
    <main>
        <div>
            <h1> Produits</h1>
        </div>
        <div>
            <table>
                <caption>Liste des produits</caption>
                <h2>Page : {{ pageactuelle + 1 }} / {{ nbpageTotales }}</h2>
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
                <tr>
                    <td>
                        <button @click="vaDebut" :disabled="pageactuelle == 0"> debut </button>
                    </td>
                    <td>
                        <button @click="vaPrecedent"> prec </button>
                    </td>
                    <td>
                        <button @click="vaSuivant"> suiv </button>
                    </td>
                    <td>
                        <button @click="vaFin" :disabled="nbpageTotales == pageactuelle + 1"> Fin </button>
                    </td>
                </tr>
            </table>
        </div>
    </main>
</template>
<script setup>
import { doAjaxRequest } from '../api';
import { reactive, onMounted, ref } from "vue";
let listeProduits = ref([]);
let nbpageTotales = ref([]);
let pageactuelle = ref([]);
let page = ref(0);
let taille = ref(5);

function chargeProduits() {
    doAjaxRequest("/api/produits?sort=nom,asc&page=" + page.value + "&size=" + taille.value)
        .then((json) => {
            listeProduits.value = json._embedded.produits;
            console.log("chargeProduit " + page.value)
            console.log(listeProduits.value)
            nbpageTotales.value = json.page.totalPages;
            pageactuelle.value = json.page.number;
        })
        .catch((error) => {
            console.log(error);
        });
}

function vaDebut() {
    page.value = 0;
    chargeProduits();
}

function vaPrecedent() {
    if (page.value > 0) {
        page.value = page.value - 1;
        chargeProduits();
    }

}

function vaSuivant() {
    if (page.value + 1 < nbpageTotales.value) {
        page.value = page.value + 1;
        chargeProduits();
    }
}

function vaFin() {
    page.value = nbpageTotales.value - 1;
    chargeProduits();
}
onMounted(chargeProduits);
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