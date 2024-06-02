<template>
    <main>
        <div>
            <table>
                <caption>Les produits - page {{ data.page.number + 1 }} / {{ data.page.totalPages }} </caption>
                <tr>
                    <th>Nom</th>
                    <th>Prix</th>
                    <th>Stock</th>
                    <th>Commandés</th>
                </tr>
                <tr v-if="data.listeProduits.length === 0">
                    <td colspan="4">Veuillez patienter, les produits chargent...</td>
                </tr>
                <tr v-for="produit in data.listeProduits" :key="produit">
                    <td>{{ produit.nom }}</td>
                    <td>{{ produit.prixUnitaire }}</td>
                    <td>{{ produit.unitesEnStock }}</td>
                    <td>{{ produit.unitesCommandees }}</td>
                </tr>
            </table>
            <div class="styleBoutton">
                <button @click="chargeProduits(0)">
                    Début
                </button>
                <button @click="data.page.number + 1 > 1 ? chargeProduits(data.page.number - 1) : ''">
                    Précédent
                </button>
                <button @click="data.page.number + 1 < data.page.totalPages ? chargeProduits(data.page.number + 1) : ''">
                    Suivant
                </button>
                <button @click="chargeProduits(data.page.totalPages - 1)">
                    Fin
                </button>
            </div>
        </div>
    </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { doAjaxRequest } from "../api";

let data = reactive({
    listeProduits: [], 
    page: {}
});

function showError(error) {
    console.log("Erreur : status %d", error.status)
    console.log(error.body);
    alert(error.message);
}

/**
 * On va chercher les produits en appelant l'API
 * @param {number} numPage Numéro de la page 
 */
function chargeProduits(numPage) {
    doAjaxRequest(`/api/produits?page=${numPage}&size=5&sort=nom,asc`)
        .then((json) => {
            data.listeProduits = json._embedded.produits;
            data.page = json.page;
        })
        .catch(showError);
}

// ce qu'il se passe a l'ouverture du document
onMounted(() => {
    chargeProduits(0);
});
</script>


<style scoped>
td,
th {
    border: 1px solid #ddd;
    padding: 8px;
}

th {
    text-align: center;
    background-color: #1c9b1c;
    color: rgb(255, 255, 255);
}

.styleBoutton {
    display: flex;
    justify-content: center;
    margin-top: 1.5em;
  }

table {
  margin-top: 0.3em;
  border-collapse: collapse;
  width: 100%;
}

  


</style>