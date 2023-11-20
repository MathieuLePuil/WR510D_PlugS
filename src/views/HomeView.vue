 <script setup>
import { ref, onMounted } from 'vue';

const data = ref(null);
const isLightOn = ref(false);

const toggleLight = async () => {
    const action = isLightOn.value ? 'off' : 'on';
    const url = `https://shelly-86-eu.shelly.cloud/device/relay/control?channel=0&turn=${action}&id=083A8DC30983&auth_key=MWRmYzFldWlk4D16582E43E196112886AC9F26DC326B400BAD032511CF71503DE17BDCAB0CFE03B0147A1ABA5420`;
    const response = await fetch(url, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
    });
    if(response.ok) {
        isLightOn.value = !isLightOn.value;
    }
}

onMounted(async () => {
    const response = await fetch(`https://shelly-86-eu.shelly.cloud/device/status?id=083A8DC30983&auth_key=MWRmYzFldWlk4D16582E43E196112886AC9F26DC326B400BAD032511CF71503DE17BDCAB0CFE03B0147A1ABA5420`);
    data.value = await response.json();
});

</script>

<template>
    <main>
        <button @click="toggleLight" :style="{ background: isLightOn.value ? 'red' : 'green', border: 'none', color: 'white' }">
            {{ isLightOn.value ? 'Éteindre la lumière' : 'Allumer la lumière' }}
        </button>
        <div v-if="data">
            <div>
                <h2>Wi-fi</h2>
                <p>Statut du Wi-fi : {{ data.data.device_status.wifi_sta.connected ? 'Oui' : 'Non' }}</p>
                <p>Réseau du Wi-fi : {{ data.data.device_status.wifi_sta.ssid }}</p>
                <p>Adresse IP : {{ data.data.device_status.wifi_sta.ip }}</p>
            </div>
            <div>
                <h2>Cloud</h2>
                <p>Activé : {{ data.data.device_status.cloud.enabled }}</p>
                <p>Connecté : {{ data.data.device_status.cloud.connected }}</p>
            </div>
            <div>
                <h2>Autres informations</h2>
                <p>Température : {{ data.data.device_status.temperature }}</p>
                <p v-if="data.data.device_status.update.has_update !== false">Une mise à jour est nécessaire.</p>
                <p v-else>La prise est à jour.</p>
            </div>
        </div>
    </main>
</template>
