<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>CRYPTO RANK</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">CRYPTO RANKk</ion-title>
        </ion-toolbar>

      </ion-header>
          <ion-refresher slot="fixed" @ionRefresh="handleRefresh($event)">
                <ion-refresher-content></ion-refresher-content>
          </ion-refresher>

        <ion-list v-for="item in cryptoData">
            <ion-item>
            <ion-label>
                <p>Rank</p>
                <h1>{{  item.rank }}</h1>
            </ion-label>
            
            <ion-label>
                <p>{{  item.name }}</p>
                <h1>{{  item.symbol }}</h1>
            </ion-label>

            <div slot="end" class="price-container">
                <ion-note color="dark">{{ formatCurrency(item.price_usd) }}</ion-note>
                <ion-badge color="none">
                    <ion-text :color="isNegative(item.percent_change_24h) ? 'danger' : 'success'">
                    <ion-icon :icon="isNegative(item.percent_change_24h) ? caretDown : caretUp"></ion-icon>
                        {{  item.percent_change_24h }}
                    </ion-text>
                </ion-badge>
            </div>
            </ion-item>
        </ion-list>
        <ion-fab slot="fixed" vertical="bottom" horizontal="end">
            <ion-fab-button @click="getDataCrypto" class = "refresh-btn">
        <ion-icon :icon="refresh"></ion-icon>
      </ion-fab-button>
        </ion-fab>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { 
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonList,
    IonItem,
    IonLabel,
    IonBadge,
    IonNote,
    IonIcon,
    IonText,
    IonRefresher,
    IonRefresherContent,
    IonFab,
    IonFabButton
    } from '@ionic/vue';
import { caretUp, caretDown, refresh} from 'ionicons/icons';
import { ref, onMounted, getCurrentInstance } from 'vue';
import axios from 'axios';

interface Ticker {
      id: string,
      symbol: string,
      name: string,
      nameid: string,
      rank: number,
      price_usd: string,
      percent_change_24h: string,
      percent_change_1h: string,
      percent_change_7d: string,
      price_btc: string,
      market_cap_usd: string,
      volume24: number,
      volume24a: number,
      csupply: string,
      tsupply: string,
      msupply: string
};
// Variabel reactif/ref

const cryptoData = ref <Ticker[]>([]);
const apiUrl = "https://api.coinlore.net/api/tickers/";
onMounted (() => {
    getDataCrypto();
});

const getDataCrypto = async () => {
    try {
        const response = await axios.get (apiUrl);
        cryptoData.value = response.data.data;

        
    } catch (error) {
        console.log('Error Response' + error);
        
    }
}

const formatCurrency = (value: string) => {
    const number = parseFloat (value);
    return number.toLocaleString('id-ID', {
        style: 'currency',
        currency: 'USD',
        minimumFractionDigits: 2,
        maximumFractionDigits: 2
    });
}

const isNegative = (value: string) : boolean => {
    return parseFloat (value) < 0;
}

const handleRefresh = async (Event : any) => {
    await getDataCrypto ();
    Event.target.complete();
}

</script>

<style scoped>
.price-container {
    display: flex;
    flex-direction: column;
    align-items:flex-end ;
}
</style>
