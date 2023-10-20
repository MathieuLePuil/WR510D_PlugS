<script setup>
import { ref, onMounted } from 'vue';

const data = ref(null);
const isLightOn = ref(false);

const toggleLight = async () => {
    const action = isLightOn.value ? 'off' : 'on';
    const url = `http://192.168.1.100/relay/0/${action}`;
    const response = await fetch(url, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify({ ison: action === 'on' })
    });
    if(response.ok) {
        isLightOn.value = !isLightOn.value;
    }
}

onMounted(async () => {
    const response = await fetch('http://192.168.1.100/status');
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
                <p>Statut du Wi-fi : {{ data.wifi_sta.connected ? 'Oui' : 'Non' }}</p>
                <p>Réseau du Wi-fi : {{ data.wifi_sta.ssid }}</p>
                <p>Adresse IP : {{ data.wifi_sta.ip }}</p>
            </div>
            <div>
                <h2>Cloud</h2>
                <p>Activé : {{ data.cloud.enabled }}</p>
                <p>Connecté : {{ data.cloud.connected }}</p>
            </div>
            <div>
                <h2>Autres informations</h2>
                <p>Température : {{ data.temperature }}</p>
                <p v-if="data.update.has_update !== false">Une mise à jour est nécessaire.</p>
                <p v-else>La prise est à jour.</p>
            </div>
        </div>
    </main>
</template>
