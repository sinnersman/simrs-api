<script setup>
import { Head, router, Link } from '@inertiajs/vue3';
import { ref } from 'vue';

const props = defineProps({
    selectedPoli: {
        type: String,
        default: 'INT',
    },
    currentlyCalling: {
        type: Object,
        default: null,
    },
    antreans: {
        type: Array,
        default: () => [],
    },
    today: {
        type: String,
        default: '',
    },
});

const poliList = [
    { kd: 'INT', nm: 'Penyakit Dalam' },
    { kd: 'ANA', nm: 'Anak' },
    { kd: 'BED', nm: 'Bedah Umum' },
    { kd: 'OBG', nm: 'Kandungan & Kebidanan' },
    { kd: 'MAT', nm: 'Mata' },
    { kd: 'GIG', nm: 'Gigi & Mulut' },
];

const isProcessing = ref(false);

const changePoli = (kd) => {
    router.get('/poli/panggil', { kodepoli: kd }, { preserveState: true });
};

const panggilAntrean = (antreanId) => {
    isProcessing.value = true;
    router.post(`/poli/panggil/${antreanId}/panggil`, {}, {
        preserveScroll: true,
        onFinish: () => { isProcessing.value = false; }
    });
};

const selesaiPelayanan = (antreanId) => {
    isProcessing.value = true;
    router.post(`/poli/panggil/${antreanId}/selesai`, {}, {
        preserveScroll: true,
        onFinish: () => { isProcessing.value = false; }
    });
};

// Pasien berikutnya yang siap dipanggil
const nextPatient = computed(() => {
    return props.antreans.find(a => a.status === 'Check-In' || a.status === 'Menunggu');
});
</script>

<script>
import { computed } from 'vue';
</script>

<template>
    <Head title="Panggil Antrean Poli Dokter — SIMRS Gateway" />

    <div class="min-h-screen bg-stone-100 text-stone-800 flex flex-col font-sans select-none">

        <!-- HEADER POLI DOKTER -->
        <header class="bg-blue-950 text-white px-8 py-4 flex items-center justify-between shadow-md">
            <div class="flex items-center gap-4">
                <div class="w-12 h-12 rounded-xl bg-white text-blue-900 flex items-center justify-center font-black text-2xl shadow-sm">
                    +
                </div>
                <div>
                    <h1 class="text-2xl font-bold tracking-tight">Meja Periksa Dokter & Perawat Poli</h1>
                    <p class="text-xs text-stone-300 font-medium">Pengontrol Pemanggilan Antrean Poli & Task ID 4 & 5 BPJS</p>
                </div>
            </div>

            <div class="flex items-center gap-4">
                <Link
                    href="/display/antrean"
                    target="_blank"
                    class="px-4 py-2 rounded-lg bg-orange-500 hover:bg-orange-600 text-white font-bold text-xs border border-orange-600 flex items-center gap-1.5 transition active:scale-95"
                >
                    <svg class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
                    </svg>
                    Buka Layar TV Ruang Tunggu
                </Link>
                <div class="text-xs text-stone-300 font-semibold">{{ today }}</div>
            </div>
        </header>

        <!-- MAIN WORKSPACE -->
        <main class="flex-1 max-w-6xl mx-auto w-full p-6 space-y-6">

            <!-- POLIKLINIK SELECTOR BUTTONS -->
            <div class="bg-white border border-stone-200 rounded-2xl p-4 shadow-sm space-y-2">
                <label class="text-xs font-bold text-stone-500 uppercase tracking-wider">Pilih Ruang Poliklinik Anda:</label>
                <div class="flex flex-wrap gap-2">
                    <button
                        v-for="p in poliList"
                        :key="p.kd"
                        @click="changePoli(p.kd)"
                        :class="[
                            selectedPoli === p.kd
                                ? 'bg-blue-800 text-white font-bold shadow-sm'
                                : 'bg-stone-100 text-stone-700 hover:bg-stone-200 font-semibold',
                            'px-4 py-2 rounded-xl text-xs transition cursor-pointer'
                        ]"
                    >
                        {{ p.nm }} ({{ p.kd }})
                    </button>
                </div>
            </div>

            <!-- ACTIVE CALLING CARD & CONTROLS -->
            <div class="grid grid-cols-1 md:grid-cols-12 gap-6">

                <!-- CURRENT PATIENT CARD -->
                <div class="md:col-span-7 bg-white border-2 border-blue-800 rounded-3xl p-6 shadow-md flex flex-col justify-between">
                    <div>
                        <div class="flex items-center justify-between border-b border-stone-200 pb-3">
                            <span class="text-xs font-bold uppercase tracking-wider text-blue-900">Pasien Dalam Ruang Periksa</span>
                            <span class="px-2.5 py-0.5 rounded-full text-xs font-bold bg-blue-100 text-blue-900">
                                {{ currentlyCalling ? 'Sedang Dilayani' : 'Belum Ada Panggilan' }}
                            </span>
                        </div>

                        <div class="text-center py-6 space-y-1">
                            <div class="text-xs font-bold uppercase tracking-wider text-stone-400">NOMOR ANTREAN:</div>
                            <div class="text-7xl font-black font-mono tracking-widest text-blue-900">
                                {{ currentlyCalling?.nomorantrean || '---' }}
                            </div>
                            <div class="text-xl font-bold text-stone-900 mt-2">
                                {{ currentlyCalling?.nama_pasien || 'Silakan panggil antrean berikutnya' }}
                            </div>
                            <div class="text-xs text-stone-500 font-mono">
                                {{ currentlyCalling ? `Kode Booking: ${currentlyCalling.kode_booking} • NIK: ${currentlyCalling.nik || '-'}` : '' }}
                            </div>
                        </div>
                    </div>

                    <!-- ACTION BUTTONS -->
                    <div class="space-y-3 pt-4 border-t border-stone-100">
                        <div v-if="currentlyCalling" class="grid grid-cols-2 gap-3">
                            <button
                                type="button"
                                @click="panggilAntrean(currentlyCalling.id)"
                                :disabled="isProcessing"
                                class="h-14 rounded-xl bg-orange-500 hover:bg-orange-600 active:bg-orange-700 text-white font-extrabold text-sm uppercase tracking-wider shadow transition cursor-pointer flex items-center justify-center gap-2"
                            >
                                <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5.882V19.24a1.76 1.76 0 01-3.417.592l-2.147-6.15M18 13a3 3 0 100-6M5.436 13.683A4.001 4.001 0 017 6h1.832c4.1 0 7.625-1.234 9.168-3v14c-1.543-1.766-5.067-3-9.168-3H7a3.988 3.988 0 01-1.564-.317z" />
                                </svg>
                                Panggil Ulang di TV
                            </button>

                            <button
                                type="button"
                                @click="selesaiPelayanan(currentlyCalling.id)"
                                :disabled="isProcessing"
                                class="h-14 rounded-xl bg-blue-800 hover:bg-blue-900 active:bg-blue-950 text-white font-extrabold text-sm uppercase tracking-wider shadow transition cursor-pointer flex items-center justify-center gap-2"
                            >
                                <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
                                </svg>
                                Selesai Layanan (Task 5)
                            </button>
                        </div>

                        <!-- PANGGIL BERIKUTNYA BUTTON -->
                        <button
                            v-if="nextPatient"
                            type="button"
                            @click="panggilAntrean(nextPatient.id)"
                            :disabled="isProcessing"
                            class="w-full h-14 rounded-xl bg-orange-500 hover:bg-orange-600 active:bg-orange-700 text-white font-extrabold text-base tracking-wider shadow-md transition cursor-pointer flex items-center justify-center gap-2"
                        >
                            <span>PANGGIL ANTREAN BERIKUTNYA ({{ nextPatient.nomorantrean }})</span>
                            <span>&rarr;</span>
                        </button>
                        <div v-else class="text-center text-xs text-stone-400 py-3 font-medium">
                            Semua antrean poli ini telah selesai dilayani.
                        </div>
                    </div>
                </div>

                <!-- QUEUE WAITING LIST -->
                <div class="md:col-span-5 bg-white border border-stone-200 rounded-3xl p-6 shadow-sm flex flex-col justify-between">
                    <div class="space-y-3">
                        <div class="flex items-center justify-between border-b border-stone-200 pb-3">
                            <span class="text-xs font-bold uppercase tracking-wider text-stone-600">Daftar Antrean Hari Ini</span>
                            <span class="text-xs font-semibold text-stone-400">{{ antreans.length }} Pasien</span>
                        </div>

                        <div class="space-y-2 max-h-[360px] overflow-y-auto pr-1">
                            <div
                                v-for="a in antreans"
                                :key="a.id"
                                class="p-3 rounded-xl border border-stone-200 flex items-center justify-between text-xs hover:bg-stone-50 transition"
                            >
                                <div class="space-y-0.5">
                                    <div class="flex items-center gap-2">
                                        <span class="font-black text-sm font-mono text-blue-900">{{ a.nomorantrean }}</span>
                                        <span :class="[
                                            a.status === 'Dipanggil Poli' ? 'bg-orange-100 text-orange-900' :
                                            a.status === 'Selesai Layanan Dokter' ? 'bg-blue-100 text-blue-900' :
                                            'bg-stone-100 text-stone-700',
                                            'px-2 py-0.5 rounded text-[10px] font-bold'
                                        ]">
                                            {{ a.status }}
                                        </span>
                                    </div>
                                    <div class="font-bold text-stone-900">{{ a.nama_pasien }}</div>
                                    <div class="text-[10px] text-stone-400 font-mono">RM: {{ a.norm || '-' }} • {{ a.kode_booking }}</div>
                                </div>

                                <button
                                    v-if="a.status !== 'Selesai Layanan Dokter'"
                                    type="button"
                                    @click="panggilAntrean(a.id)"
                                    :disabled="isProcessing"
                                    class="px-3 py-1.5 rounded-lg bg-blue-800 hover:bg-blue-900 text-white font-bold text-xs transition cursor-pointer"
                                >
                                    Panggil
                                </button>
                            </div>
                        </div>
                    </div>

                    <div class="pt-3 border-t border-stone-100 text-center text-[11px] text-stone-400">
                        *Klik tombol "Panggil" untuk menyuarakan nomor di layar TV dan otomatis mencatat Task 4 di BPJS Antrol.
                    </div>
                </div>

            </div>

        </main>
    </div>
</template>
