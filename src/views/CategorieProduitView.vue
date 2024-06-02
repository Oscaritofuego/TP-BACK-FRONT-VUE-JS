<template>
    <main>
        <h1>Les catégories de produits</h1>
        <div>
        <select v-model="data.selectedCate" @change="changeCate">
            <option disabled value="">Veuillez choisir une catégorie</option>
            <option v-for="categorie in data.listeCategories" :value="categorie.code">{{ categorie.libelle }}</option>
        </select>
        </div>
        <div>
            <table>
                <caption>Les produits</caption>
                <tr>
                    <th>Nom</th>
                    <th>Prix</th>
                    <th>Stock</th>
                    <th>Commandés</th>
                </tr>
    
                <tr v-if="data.produitsParCate.length === 0">
                    <td colspan="4">Veuillez patienter, chargement des produits...</td>
                </tr>
                
                <tr v-for="produit in data.produitsParCate" :key="produit">
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
import { reactive, onMounted } from "vue";
import { doAjaxRequest } from "../api";

let data = reactive({

    selectedCate: 1,
    
    produitsParCate: [],
    
    listeCategories: [], 
    
    page: {}
});

function changeCate(){
    chargeProduitsByCate()
}
function showError(error) {
    console.log("Erreur : status %d", error.status)
    console.log(error.body);
    alert(error.message);
}

function chargeProduitsByCate() {
    doAjaxRequest(`/api/categories/${data.selectedCate}/produits`)
        .then((json) => {
            data.produitsParCate = json._embedded.produits;
            data.page = json.page;
        })
        .catch(showError);
}

function chargeCategories() {
    // Appel à l'API pour avoir la liste des catégories
    // Trié par code, descendant
    // Verbe HTTP GET par défaut
    doAjaxRequest("/api/categories?sort=code,desc")
        .then((json) => {
            data.listeCategories = json._embedded.categories;
        })
        .catch(showError);
}

onMounted(() => {
    chargeProduitsByCate(data.selectedCate);
    chargeCategories()
});
</script>


<style scoped>
td,
th {
    border: 1px solid #ddd;
    padding: 8px;
}
table {
  margin-top: 0.3em;
  border-collapse: collapse;
  width: 100%;
}
th {
    padding-top: 0.2em;
    padding-bottom: 0.2em;
    text-align: center;
    background-color: #216921;
    color: rgb(255, 255, 255);
}

</style>