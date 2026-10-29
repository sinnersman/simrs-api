<script setup>
import { Head, router, Link } from '@inertiajs/vue3';
import { ref, computed } from 'vue';
import AdminLayout from '@/Layouts/AdminLayout.vue';

const props = defineProps({
    antreans: {
        type: Array,
        default: () => [],
    },
    selectedTanggal: {
        type: String,
        default: '',
    },
    stats: {
        type: Object,
        default: () => ({
            total: 0,
            checkin: 0,
            sedang_layani: 0,
            selesai: 0,
        }),
    },
});

const searchQuery = ref('');
const selectedPoliFilter = ref('ALL');
const tanggalFilter = ref(props.selectedTanggal);
const isUpdating = ref(null);

const uniquePolis = computed(() => {
    const list = new Set();
    props.antreans.forEach(a => {
        if (a.namapoli) list.add(a.namapoli);
    });
    return Array.from(list);
});

const filteredAntreans = computed(() => {
    return props.antreans.filter(a => {
        const matchesPoli = selectedPoliFilter.value === 'ALL' || a.namapoli === selectedPoliFilter.value;
        const q = searchQuery.value.toLowerCase().trim();
        const matchesQuery = !q ||
            (a.kode_booking && a.kode_booking.toLowerCase().includes(q)) ||
            (a.nomorantrean && a.nomorantrean.toLowerCase().includes(q)) ||
            (a.nama_pasien && a.nama_pasien.toLowerCase().includes(q)) ||
            (a.nik && a.nik.includes(q)) ||
            (a.nomorkartu && a.nomorkartu.includes(q));

        return matchesPoli && matchesQuery;
    });
});

const changeDate = () => {
    router.get('/admin/antrol-monitor', { tanggal: tanggalFilter.value }, { preserveState: true });
};

const triggerAdvanceTask = (antreanId, nextTaskId) => {
    isUpdating.value = `${antreanId}-${nextTaskId}`;
    router.post(`/admin/antrol-monitor/${antreanId}/advance-task`, {
        taskid: nextTaskId,
    }, {
        preserveScroll: true,
        onFinish: () => {
            isUpdating.value = null;
        },
    });
};

const taskStyle = (task, taskNum) => {
    if (!task?.waktu) {
        return 'bg-stone-100 border-stone-200 text-stone-500';
    }
    if (taskNum === 7) return 'bg-blue-800 border-blue-800 text-white font-black';
    if (taskNum === 6) return 'bg-purple-100 border-purple-300 text-purple-950 font-semibold';
    if (taskNum === 4 || taskNum === 5) return 'bg-orange-100 border-orange-300 text-orange-950 font-semibold';
    if (taskNum === 3) return 'bg-blue-100 border-blue-400 text-blue-950 font-bold';
    return 'bg-blue-50 border-blue-300 text-blue-950 font-semibold';
};
</script>

<template>
    <Head title="Monitoring Task ID Antrol BPJS" />

    <AdminLayout>
        <div class="space-y-5">

            <!-- HEADER BANNER -->
            <div class="flex flex-col justify-between gap-4 rounded-3xl border border-stone-200 bg-white p-6 shadow-sm md:flex-row md:items-center">
                <div>
                    <div class="flex items-center gap-2">
                        <span class="rounded-full bg-blue-800 px-2.5 py-0.5 text-xs font-bold uppercase tracking-wider text-white">
                            BPJS Antrean Online
                        </span>
                        <span class="text-xs font-medium text-stone-500">Tracking Aliran Task 1 s/d 7</span>
                    </div>
                    <h1 class="mt-2 text-2xl font-extrabold tracking-tight text-stone-900">
                        Monitoring &amp; Simulator Task ID Antrol
                    </h1>
                    <p class="mt-1 max-w-2xl text-xs leading-relaxed text-stone-500">
                        Pantau riwayat pergerakan waktu tunggu dan waktu layan pasien dari APM, poliklinik, hingga instalasi farmasi yang disinkronkan ke BPJS Antrol secara real-time.
                    </p>
                </div>

                <div class="flex items-center gap-3">
                    <div class="flex items-center gap-2 rounded-xl border border-stone-300 bg-stone-50 px-3 py-1.5">
                        <span class="text-xs font-bold uppercase text-stone-500">Tanggal:</span>
                        <input
                            type="date"
                            v-model="tanggalFilter"
                            @change="changeDate"
                            class="cursor-pointer bg-transparent text-xs font-bold text-stone-800 focus:outline-none"
                        />
                    </div>
                    <Link
                        href="/apm"
                        target="_blank"
                        class="flex items-center gap-1.5 rounded-xl bg-orange-500 px-4 py-2 text-xs font-bold text-white shadow-sm transition hover:bg-orange-600 active:scale-95"
                    >
                        <span>Buka APM Kiosk</span>
                        <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M14 5l7 7m0 0l-7 7m7-7H3" />
                        </svg>
                    </Link>
                </div>
            </div>

            <!-- STATS CARDS -->
            <div class="grid grid-cols-2 gap-4 md:grid-cols-4">
                <div class="rounded-3xl border border-stone-200 border-l-4 border-l-stone-400 bg-white p-4 shadow-sm">
                    <div class="text-xs font-bold uppercase tracking-wider text-stone-400">Total Antrean Hari Ini</div>
                    <div class="mt-1 font-mono text-3xl font-extrabold text-stone-900">{{ stats.total }}</div>
                    <div class="mt-0.5 text-[11px] text-stone-500">Pasien terdaftar di sistem</div>
                </div>
                <div class="rounded-3xl border border-stone-200 border-l-4 border-l-blue-800 bg-white p-4 shadow-sm">
                    <div class="text-xs font-bold uppercase tracking-wider text-blue-800">Check-In / Tiba (Task 3)</div>
                    <div class="mt-1 font-mono text-3xl font-extrabold text-blue-800">{{ stats.checkin }}</div>
                    <div class="mt-0.5 text-[11px] text-blue-700">Sudah tiba di faskes</div>
                </div>
                <div class="rounded-3xl border border-stone-200 border-l-4 border-l-orange-500 bg-white p-4 shadow-sm">
                    <div class="text-xs font-bold uppercase tracking-wider text-orange-600">Sedang Di Poli (Task 4)</div>
                    <div class="mt-1 font-mono text-3xl font-extrabold text-orange-600">{{ stats.sedang_layani }}</div>
                    <div class="mt-0.5 text-[11px] text-orange-600">Pemeriksaan dokter</div>
                </div>
                <div class="rounded-3xl border border-stone-200 border-l-4 border-l-blue-950 bg-white p-4 shadow-sm">
                    <div class="text-xs font-bold uppercase tracking-wider text-blue-950">Selesai (Task 7)</div>
                    <div class="mt-1 font-mono text-3xl font-extrabold text-blue-950">{{ stats.selesai }}</div>
                    <div class="mt-0.5 text-[11px] text-stone-500">Obat sudah diserahkan</div>
                </div>
            </div>

            <!-- CONTROLS & FILTER -->
            <div class="flex flex-col items-center justify-between gap-3 rounded-3xl border border-stone-200 bg-white p-4 shadow-sm sm:flex-row">
                <div class="relative w-full sm:w-80">
                    <input
                        type="text"
                        v-model="searchQuery"
                        placeholder="Cari kode booking / antrean / pasien..."
                        class="w-full rounded-xl border border-stone-300 bg-stone-50 py-2.5 pl-9 pr-3 text-xs text-stone-900 focus:border-blue-800 focus:bg-white focus:outline-none"
                    />
                    <svg class="absolute left-3 top-3 h-4 w-4 text-stone-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                    </svg>
                </div>

                <div class="flex w-full items-center gap-2 sm:w-auto">
                    <span class="whitespace-nowrap text-xs font-bold text-stone-500">Filter Poli:</span>
                    <select
                        v-model="selectedPoliFilter"
                        class="rounded-xl border border-stone-300 bg-stone-50 px-3 py-2 text-xs font-semibold text-stone-800 focus:bg-white focus:outline-none"
                    >
                        <option value="ALL">Semua Poliklinik</option>
                        <option v-for="p in uniquePolis" :key="p" :value="p">{{ p }}</option>
                    </select>
                </div>
            </div>

            <!-- PATIENT TASK ID PIPELINE TABLE -->
            <div class="overflow-hidden rounded-3xl border border-stone-200 bg-white shadow-sm">
                <div class="overflow-x-auto">
                    <table class="w-full border-collapse text-left text-xs">
                        <thead>
                            <tr class="bg-blue-950 font-bold uppercase tracking-wider text-white">
                                <th class="p-4">Pasien &amp; Antrean</th>
                                <th class="p-4">Poli &amp; DPJP</th>
                                <th class="p-4 min-w-[480px]">Pipeline Aliran Task ID (1 s/d 7)</th>
                                <th class="p-4 text-center">Durasi Tunggu</th>
                                <th class="p-4 text-right">Aksi Simulator</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-stone-100">
                            <tr v-if="filteredAntreans.length === 0">
                                <td colspan="5" class="p-8 text-center text-stone-400">
                                    Belum ada antrean terdaftar pada tanggal ini.
                                </td>
                            </tr>
                            <tr
                                v-for="a in filteredAntreans"
                                :key="a.id"
                                class="transition hover:bg-stone-50"
                            >
                                <!-- PASIEN -->
                                <td class="space-y-1 p-4 align-top">
                                    <div class="flex items-center gap-2">
                                        <span class="font-mono text-base font-extrabold text-blue-800">{{ a.nomorantrean }}</span>
                                        <span class="rounded border border-stone-200 bg-stone-100 px-1.5 py-0.5 text-[10px] font-bold text-stone-600">
                                            {{ a.sumber }}
                                        </span>
                                    </div>
                                    <div class="text-sm font-bold text-stone-900">{{ a.nama_pasien || '-' }}</div>
                                    <div class="font-mono text-[11px] text-stone-500">
                                        Booking: <span class="font-semibold text-stone-700">{{ a.kode_booking }}</span>
                                    </div>
                                </td>

                                <!-- POLI & DOKTER -->
                                <td class="space-y-1 p-4 align-top">
                                    <div class="font-bold text-stone-900">{{ a.namapoli }}</div>
                                    <div class="text-stone-600">{{ a.namadokter }}</div>
                                    <div class="text-[11px] text-stone-400">{{ a.jampraktek }}</div>
                                </td>

                                <!-- PIPELINE TASK ID (1-7) -->
                                <td class="p-4 align-top">
                                    <div class="grid grid-cols-7 gap-1.5 text-center">
                                        <!-- TASK 1 -->
                                        <div :class="taskStyle(a.tasks.task1, 1)" class="flex min-h-[58px] flex-col justify-between rounded-xl border p-2">
                                            <div class="text-[9px] font-bold uppercase">T1. Ambil</div>
                                            <div class="mt-1 font-mono text-[10px] font-extrabold">{{ a.tasks.task1.time_str || '-' }}</div>
                                        </div>
                                        <!-- TASK 2 -->
                                        <div :class="taskStyle(a.tasks.task2, 2)" class="flex min-h-[58px] flex-col justify-between rounded-xl border p-2">
                                            <div class="text-[9px] font-bold uppercase">T2. Loket</div>
                                            <div class="mt-1 font-mono text-[10px] font-extrabold">{{ a.tasks.task2.time_str || '-' }}</div>
                                        </div>
                                        <!-- TASK 3 -->
                                        <div :class="taskStyle(a.tasks.task3, 3)" class="flex min-h-[58px] flex-col justify-between rounded-xl border p-2">
                                            <div class="text-[9px] font-bold uppercase">T3. CheckIn</div>
                                            <div class="mt-1 font-mono text-[10px] font-extrabold">{{ a.tasks.task3.time_str || '-' }}</div>
                                        </div>
                                        <!-- TASK 4 -->
                                        <div :class="taskStyle(a.tasks.task4, 4)" class="flex min-h-[58px] flex-col justify-between rounded-xl border p-2">
                                            <div class="text-[9px] font-bold uppercase">T4. Dokter</div>
                                            <div class="mt-1 font-mono text-[10px] font-extrabold">{{ a.tasks.task4.time_str || '-' }}</div>
                                        </div>
                                        <!-- TASK 5 -->
                                        <div :class="taskStyle(a.tasks.task5, 5)" class="flex min-h-[58px] flex-col justify-between rounded-xl border p-2">
                                            <div class="text-[9px] font-bold uppercase">T5. Selesai</div>
                                            <div class="mt-1 font-mono text-[10px] font-extrabold">{{ a.tasks.task5.time_str || '-' }}</div>
                                        </div>
                                        <!-- TASK 6 -->
                                        <div :class="taskStyle(a.tasks.task6, 6)" class="flex min-h-[58px] flex-col justify-between rounded-xl border p-2">
                                            <div class="text-[9px] font-bold uppercase">T6. Farmasi</div>
                                            <div class="mt-1 font-mono text-[10px] font-extrabold">{{ a.tasks.task6.time_str || '-' }}</div>
                                        </div>
                                        <!-- TASK 7 -->
                                        <div :class="taskStyle(a.tasks.task7, 7)" class="flex min-h-[58px] flex-col justify-between rounded-xl border p-2">
                                            <div class="text-[9px] font-bold uppercase">T7. Obat OK</div>
                                            <div class="mt-1 font-mono text-[10px] font-extrabold">{{ a.tasks.task7.time_str || '-' }}</div>
                                        </div>
                                    </div>
                                </td>

                                <!-- DURASI / MUTU SPM -->
                                <td class="space-y-1 p-4 text-center align-top">
                                    <div v-if="a.durasi.tunggu_poli !== null" class="text-[11px] text-stone-600">
                                        Tunggu Poli: <span class="font-bold text-stone-900">{{ a.durasi.tunggu_poli }} m</span>
                                    </div>
                                    <div v-if="a.durasi.layan_dokter !== null" class="text-[11px] text-stone-600">
                                        Dokter: <span class="font-bold text-stone-900">{{ a.durasi.layan_dokter }} m</span>
                                    </div>
                                    <div v-if="a.durasi.total_waktu !== null" class="text-[11px] font-bold text-blue-800">
                                        Total: {{ a.durasi.total_waktu }} m
                                    </div>
                                    <div v-else class="text-[11px] italic text-stone-400">
                                        Sedang proses
                                    </div>
                                </td>

                                <!-- SIMULATOR AKSI PERJALANAN TASK -->
                                <td class="space-y-1.5 p-4 text-right align-top">
                                    <!-- Jika T3 selesai tapi T4 belum: Panggil Dokter -->
                                    <button
                                        v-if="a.tasks.task3.waktu && !a.tasks.task4.waktu"
                                        type="button"
                                        @click="triggerAdvanceTask(a.id, 4)"
                                        :disabled="isUpdating === `${a.id}-4`"
                                        class="whitespace-nowrap rounded-xl bg-orange-500 px-3 py-1.5 text-xs font-bold text-white shadow-sm transition hover:bg-orange-600 active:scale-95 cursor-pointer"
                                    >
                                        Panggil Dokter (Task 4)
                                    </button>

                                    <!-- Jika T4 selesai tapi T5 belum: Selesai Dokter -->
                                    <button
                                        v-else-if="a.tasks.task4.waktu && !a.tasks.task5.waktu"
                                        type="button"
                                        @click="triggerAdvanceTask(a.id, 5)"
                                        :disabled="isUpdating === `${a.id}-5`"
                                        class="whitespace-nowrap rounded-xl bg-blue-800 px-3 py-1.5 text-xs font-bold text-white shadow-sm transition hover:bg-blue-900 active:scale-95 cursor-pointer"
                                    >
                                        Selesai Resep (Task 5)
                                    </button>

                                    <!-- Jika T5 selesai tapi T6 belum: Mulai Farmasi -->
                                    <button
                                        v-else-if="a.tasks.task5.waktu && !a.tasks.task6.waktu"
                                        type="button"
                                        @click="triggerAdvanceTask(a.id, 6)"
                                        :disabled="isUpdating === `${a.id}-6`"
                                        class="whitespace-nowrap rounded-xl bg-purple-600 px-3 py-1.5 text-xs font-bold text-white shadow-sm transition hover:bg-purple-700 active:scale-95 cursor-pointer"
                                    >
                                        Racik Obat (Task 6)
                                    </button>

                                    <!-- Jika T6 selesai tapi T7 belum: Selesai Obat -->
                                    <button
                                        v-else-if="a.tasks.task6.waktu && !a.tasks.task7.waktu"
                                        type="button"
                                        @click="triggerAdvanceTask(a.id, 7)"
                                        :disabled="isUpdating === `${a.id}-7`"
                                        class="whitespace-nowrap rounded-xl bg-blue-800 px-3 py-1.5 text-xs font-bold text-white shadow-sm transition hover:bg-blue-900 active:scale-95 cursor-pointer"
                                    >
                                        Serahkan Obat (Task 7)
                                    </button>

                                    <!-- Jika T7 selesai -->
                                    <div v-else-if="a.tasks.task7.waktu" class="inline-flex items-center gap-1 text-xs font-bold text-blue-800">
                                        <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
                                            <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                                        </svg>
                                        <span>Tuntas Melayani</span>
                                    </div>

                                    <!-- Belum check in -->
                                    <div v-else class="text-[11px] italic text-stone-400">
                                        Menunggu Check-In APM
                                    </div>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>

        </div>
    </AdminLayout>
</template>
