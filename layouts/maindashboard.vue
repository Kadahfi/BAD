<template>
  <Switcher />
  <div class="page">
    <Header />
    <Sidebar />

    <!-- Start::app-content -->
    <div class="main-content app-content">
      <div class="container-fluid">
        <!-- Start::page-header -->
        <div class="d-md-flex d-block align-items-center justify-content-between my-4 page-header-breadcrumb">
          <div>
            <p class="fw-semibold fs-18 mb-0">Bon retour {{ fullname }}!</p>
            <span class="fs-semibold text-muted">Dernière connexion: {{ currentDateTime }}.</span>
            <p class="fw-semibold" :class="statusMessage === 'Compte actif' ? 'text-success' : 'text-danger'">
              {{ statusMessage }}
            </p>
          </div>
<!--          <div class="btn-list mt-md-0 mt-2">-->
<!--            <button type="button" class="btn btn-primary btn-wave" @click="toggleAccountStatus">-->
<!--              <i class="ri-refresh-line me-2 align-middle"></i>Changer le statut-->
<!--            </button>-->
<!--          </div>-->
        </div>
        <!-- End::page-header -->
        <slot></slot>
      </div>
    </div>
    <!-- End::app-content -->
    <Footer />
  </div>
  <BackToTop />
</template>

<script lang="ts" setup>
import * as bankData from '@/data/financialData.js';
import Header from "@/components/common/header.vue";
import Sidebar from "@/components/common/sidebar.vue";
import Footer from "@/components/common/footer.vue";
import Switcher from "@/components/common/switcher.vue";
import BackToTop from "@/components/common/backtotop.vue";
import PageHeader from "@/components/common/pageheader.vue";
import { ref, watchEffect } from "vue";

// Références pour la date, l'heure actuelle et le statut
const currentDateTime = ref('');
const fullname = ref(bankData.fullname);
const statusMessage = ref(''); // Pour afficher le statut du compte

// Fonction pour formater la date et l'heure sans les secondes
const getCurrentDateTime = () => {
  const now = new Date();
  const options: Intl.DateTimeFormatOptions = {
    weekday: 'long',
    year: 'numeric',
    month: 'long',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  };
  currentDateTime.value = now.toLocaleDateString('fr-FR', options);
};

// Appeler la fonction pour initialiser la date et l'heure
getCurrentDateTime();

// Mettre à jour la date et l'heure toutes les minutes
setInterval(() => {
  getCurrentDateTime();
}, 60000);

// Fonction pour vérifier le statut du compte dans localStorage
const checkAccountStatus = () => {
  const storedStatus = localStorage.getItem('accountStatus');
  if (storedStatus === 'bloqué') {
    statusMessage.value = 'Compte bloqué';
  } else if (storedStatus === 'actif') {
    statusMessage.value = 'Compte actif';
  } else {
    // Si aucun statut n'est défini, on initialise à "actif" par défaut
    localStorage.setItem('accountStatus', 'actif');
    statusMessage.value = 'Compte actif';
  }
};

// Observer et mettre à jour dynamiquement le statut
watchEffect(() => {
  checkAccountStatus();
});

// Fonction pour changer le statut (bloqué/actif)
const toggleAccountStatus = () => {
  const currentStatus = localStorage.getItem('accountStatus');
  const newStatus = currentStatus === 'bloqué' ? 'actif' : 'bloqué';
  localStorage.setItem('accountStatus', newStatus);
  checkAccountStatus();
};
</script>

<style lang="css">
.text-success {
  color: green;
}

.text-danger {
  color: red;
}
</style>
