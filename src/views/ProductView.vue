<template>
    <main>
        <div>
            <h1> Produits</h1>
        </div>
        <div>
            <table>
                <caption>Liste des produits</caption>
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
                        <button> debut </button>
                    </td>
                    <td>
                        <button> prec </button>
                    </td>
                    <td>
                        <button> suiv </button>
                    </td>
                    <td>
                        <button> Fin </button>
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

function chargeProduits() {
    doAjaxRequest("/api/produits?sort=nom,asc")
        .then((json) => {
            listeProduits.value = json._embedded.produits;
            console.log(listeProduits.value)
        })
        .catch((error) => {
            console.log(error);
        });
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