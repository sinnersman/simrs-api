<script setup>
import { Head, Link, usePage } from '@inertiajs/vue3';
import { computed, onBeforeUnmount, onMounted, ref } from 'vue';

const props = defineProps({
    whatsappNumber: {
        type: String,
        default: '6285156052630',
    },
    supportEmail: {
        type: String,
        default: 'support@simrsgateway.id',
    },
    user: {
        type: Object,
        default: null,
    },
});

const page = usePage();
const settings = computed(() => page.props.site_settings ?? {});

const activeCodeTab = ref('php');
const activeModuleTab = ref('vclaim');
const couponCopied = ref(false);
const isMobileMenuOpen = ref(false);

const navLinks = [
    { label: 'Beranda', href: '#', icon: 'M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2 2v10a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10' },
    { label: 'Arsitektur', href: '#arsitektur', icon: 'M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10' },
    { label: 'Katalog API', href: '#katalog', icon: 'M4 6h16M4 10h16M4 14h16M4 18h16', count: '209' },
    { label: 'Integrasi', href: '#kode', icon: 'M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4' },
    { label: 'Open Source & Donasi', href: '#open-source', icon: 'M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z' },
    { label: 'APM', href: '#apm', icon: 'M9 17v-2m3 2v-4m3 4v-6M5 21h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v14a2 2 0 002 2z', count: 'Segera' },
    { label: 'FAQ', href: '#faq', icon: 'M8.228 9c.549-1.165 2.03-2 3.772-2 2.21 0 4 1.343 4 3 0 1.4-1.278 2.575-3.006 2.907-.542.104-.994.54-.994 1.093m0 3h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z' },
];

const closeMobileMenu = () => {
    isMobileMenuOpen.value = false;
};

const formatRupiah = (num) => {
    if (num === null || num === undefined) return 'Rp 0';
    return 'Rp ' + Number(num).toLocaleString('id-ID');
};

const copyCoupon = async (code) => {
    try {
        await navigator.clipboard.writeText(code);
        couponCopied.value = true;
        setTimeout(() => couponCopied.value = false, 2000);
    } catch {
        // fallback
    }
};

const saweriaUrl = 'https://saweria.co/fauzirahman05';
const githubRepoUrl = 'https://github.com/sinnersman/simrs-api';
const githubZipUrl = 'https://github.com/sinnersman/simrs-api/archive/refs/heads/pmaster.zip';
const postmanCollectionUrl = '/downloads/postman-collection';
const waGeneralUrl = computed(() => {
    const wa = settings.value.whatsapp_contact || props.whatsappNumber;
    const text = encodeURIComponent('Halo, saya ingin konsultasi mengenai implementasi SIMRS Gateway / jasa setup on-premise di RS kami.');
    return `https://wa.me/${wa}?text=${text}`;
});

const waConsultUrl = computed(() => {
    const wa = settings.value.whatsapp_contact || props.whatsappNumber;
    const text = encodeURIComponent('Halo, rumah sakit kami membutuhkan pendampingan teknis / jasa kustomisasi bridging SIMRS Gateway. Mohon info ketersediaan jadwal.');
    return `https://wa.me/${wa}?text=${text}`;
});

const telegramHandle = computed(() => {
    return (settings.value.telegram_contact || 'FauziRahman95').replace('@', '');
});

const telegramGeneralUrl = computed(() => {
    const handle = telegramHandle.value;
    const text = encodeURIComponent(`Halo mas @${handle}, saya pengguna SIMRS Gateway open-source, ingin konsultasi/tanya seputar implementasi.`);
    return `https://t.me/${handle}?text=${text}`;
});

const telegramConsultUrl = computed(() => {
    const handle = telegramHandle.value;
    const text = encodeURIComponent(`Halo mas @${handle}, rumah sakit kami membutuhkan jasa setup on-premise & asistensi bridging SIMRS Gateway. Mohon info ketersediaan dan prosedurnya.`);
    return `https://t.me/${handle}?text=${text}`;
});
const telegramApmUrl = computed(() => {
    const handle = telegramHandle.value;
    const text = encodeURIComponent(`Halo mas @${handle}, rumah sakit kami tertarik menjadi RS uji coba APM (Anjungan Pendaftaran Mandiri). Mohon info jadwal dan syaratnya.`);
    return `https://t.me/${handle}?text=${text}`;
});

const activeApmTab = ref('checkin'); // 'checkin' | 'daftar'

const apmCheckInSteps = [
    {
        title: 'Pasien Sentuh Menu "Check-In Kode Booking"',
        body: 'Pasien yang sudah mendaftar via Mobile JKN atau web faskes langsung mendatangi anjungan kiosk di lobi tanpa antre di loket admisi.',
    },
    {
        title: 'Scan QR Code atau Ketik Kode Booking (Huruf & Angka)',
        body: 'Dekatkan layar HP pasien ke barcode scanner kiosk atau masukkan kode booking (misal: JKN261001) melalui keypad layar sentuh.',
    },
    {
        title: 'Validasi & Otomatis Kirim Task ID 3 ke BPJS Antrol',
        body: 'Gateway memvalidasi jadwal hari ini dan mencatat Task ID 3 (waktu pasien tiba di RS & siap menuju poli) ke server BPJS Antrean Online.',
    },
    {
        title: 'Karcis Antrean Poli & SEP Tercetak Otomatis',
        body: 'Printer thermal kiosk mencetak karcis antrean poli dan SEP VClaim. Pasien langsung menuju ruang tunggu poliklinik.',
    },
];

const apmDaftarSteps = [
    {
        title: 'Pasien Sentuh Menu "Daftar Mandiri BPJS"',
        body: 'Untuk pasien rujukan baru atau pasien kontrol yang belum memiliki kode booking online sebelumnya (pasien datang langsung / go-show).',
    },
    {
        title: 'Ketik NIK KTP (16 Digit) atau No Kartu BPJS',
        body: 'Pasien memasukkan NIK atau nomor kartu BPJS via keypad layar sentuh, atau menempelkan KTP elektronik pada reader.',
    },
    {
        title: 'Cek Eligibilitas Rujukan & Surat Kontrol Aktif',
        body: 'Sistem mengecek status keaktifan BPJS, riwayat rujukan FKTP aktif, dan surat kontrol (Sukon) secara instan lewat bridging VClaim 2.0.',
    },
    {
        title: 'Pilih Poliklinik & Dokter Jaga Hari Ini',
        body: 'Pasien memilih dokter spesialis dan jam praktik yang tersedia kuotanya hari ini, lalu mesin menerbitkan nomor antrean poli.',
    },
];

const apmSteps = computed(() => activeApmTab.value === 'checkin' ? apmCheckInSteps : apmDaftarSteps);

// Satu-satunya animasi bagian APM: karcis keluar dari printer saat bagian terlihat.
const apmSection = ref(null);
const apmPrinted = ref(false);
let apmObserver = null;

// Reveal-on-scroll halus untuk seluruh section (hormat prefers-reduced-motion via CSS).
let revealObserver = null;

onMounted(() => {
    if (!('IntersectionObserver' in window)) {
        apmPrinted.value = true;
        document.querySelectorAll('.reveal').forEach((el) => el.classList.add('is-visible'));
        return;
    }
    apmObserver = new IntersectionObserver((entries) => {
        if (entries.some((entry) => entry.isIntersecting)) {
            apmPrinted.value = true;
            apmObserver?.disconnect();
        }
    }, { threshold: 0.35 });
    if (apmSection.value) apmObserver.observe(apmSection.value);

    revealObserver = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
            if (entry.isIntersecting) {
                entry.target.classList.add('is-visible');
                revealObserver?.unobserve(entry.target);
            }
        });
    }, { threshold: 0.12, rootMargin: '0px 0px -40px 0px' });
    document.querySelectorAll('.reveal').forEach((el) => revealObserver.observe(el));
});

onBeforeUnmount(() => {
    apmObserver?.disconnect();
    revealObserver?.disconnect();
});


const codeSnippets = {
    php: `// Integrasi SIMRS Lokal ke Gateway BPJS (Cukup 4 Baris):
$response = Http::get('http://gateway-rs.lan/api/v1/bpjs/peserta', [
    'nomor' => '0001234567890',
    'tipe'  => '1', // 1: No Kartu BPJS, 2: NIK KTP
]);

$peserta = $response->json('response.peserta');
// Gateway otomatis mengurus HMAC-SHA256, Enkripsi AES-256, & Dekripsi LZ-String!`,

    curl: `# Request dari Terminal atau Server Intranet:
curl -X GET "http://gateway-rs.lan/api/v1/bpjs/peserta?nomor=0001234567890&tipe=1" \\
  -H "Accept: application/json" \\
  -H "X-Client-Id: RS-SEHAT-01"`,

    js: `// Node.js / Express / Frontend Integration:
const res = await axios.get('http://gateway-rs.lan/api/v1/bpjs/peserta', {
  params: { nomor: '0001234567890', tipe: '1' }
});

console.log(res.data.response.peserta.nama); 
// Output: SUTRISNO WIJAYA (Langsung decrypted plain JSON)`,

    python: `# Python / Django / FastAPI Integration:
import requests

res = requests.get('http://gateway-rs.lan/api/v1/bpjs/peserta', params={
    'nomor': '0001234567890',
    'tipe': '1'
})
data = res.json()['response']['peserta']`
};

const moduleList = [
    {
        id: 'vclaim',
        name: 'VClaim v3.0',
        count: '110 Endpoint',
        badge: 'Rawat Jalan & Rawat Inap',
        highlights: ['Penerbitan & Update SEP', 'Rujukan Online FKTP & Antar RS', 'Surat Kontrol & SPRI DPJP', 'Monitoring Klaim & Riwayat Pelayanan', 'Referensi Poli, Faskes & Diagnosa ICD-10']
    },
    {
        id: 'antreanrs',
        name: 'Antrean RS',
        count: '18 Endpoint',
        badge: 'Mobile JKN Ready',
        highlights: ['Sinkronisasi Jadwal Dokter Poli', 'Pemesanan Tiket Antrean Online', 'Check-in Kode Booking Pasien', 'Pembatalan Antrean Otomatis', 'Monitoring Kuota & Estimasi Pelayanan']
    },
    {
        id: 'apotek',
        name: 'Apotek BPJS',
        count: '18 Endpoint',
        badge: 'Farmasi & Obat Kronis',
        highlights: ['Pelayanan Resep Elektronik', 'Klaim Obat Kronis / Program Rujuk Balik (PRB)', 'Pencarian Master DPHO BPJS', 'Daftar Obat Kemoterapi & Khusus']
    },
    {
        id: 'pcare',
        name: 'PCare',
        count: '51 Endpoint',
        badge: 'Faskes 1 / Klinik Pratama',
        highlights: ['Pendaftaran Kunjungan Faskes Primer', 'Input Diagnosa & Tindakan Medis', 'Rujukan Vertikal/Horizontal', 'Pemberian Obat FKTP & Pemeriksaan Lab']
    },
    {
        id: 'aplicares',
        name: 'Aplicares',
        count: '5 Endpoint',
        badge: 'Ketersediaan TT',
        highlights: ['Update Real-time Kapasitas Tempat Tidur', 'Ketersediaan Kamar ICU, Isolasi & Rawat Inap', 'Katalog Kelas Perawatan BPJS']
    },
    {
        id: 'icare',
        name: 'i-Care JKN',
        count: '2 Endpoint',
        badge: 'Medical Record Sharing',
        highlights: ['Akses Riwayat Pelayanan Pasien 1 Tahun', 'Integrasi Rekam Medis Terpadu Antar-Faskes']
    },
    {
        id: 'antreanfktp',
        name: 'Antrean FKTP',
        count: '5 Endpoint',
        badge: 'Klinik & Puskesmas',
        highlights: ['Ambil Antrean Faskes Tingkat Pertama', 'Integrasi Nomor Antrean Poli Klinik ke Mobile JKN']
    },
];

const faqs = [
    {
        q: 'Apakah SIMRS Gateway ini benar-benar 100% Gratis & Open Source?',
        a: 'Ya, 100% Bebas & Terbuka. Anda bebas mengunduh source code lengkap (Laravel 12 + Vue 3), memodifikasi, dan memasangnya di server intranet lokal rumah sakit tanpa kewajiban membayar biaya lisensi bulanan, royalti per-pasien, atau biaya tersembunyi apa pun.',
    },
    {
        q: 'Bagaimana model bisnis proyek ini bertahan jika source code gratis?',
        a: 'Mengadopsi model open-source murni (seperti ekosistem Linux, Curl, SQLite): pengembangan inti didanai sukarela melalui donasi komunitas via Saweria (saweria.co/fauzirahman05). Pendapatan profesional diperoleh dari jasa nilai tambah opsional: asistensi instalasi on-site, kustomisasi bridging SIMRS lama, dan pendampingan UAT BPJS Cabang bagi rumah sakit yang membutuhkan.',
    },
    {
        q: 'Apakah sistem ini sesuai dengan regulasi PMK 24 / 2022 tentang Rekam Medis & UU PDP?',
        a: 'Sangat sesuai. Karena gateway open-source ini diinstal on-premise di server lokal rumah sakit Anda, seluruh data rekam medis pasien mengalir langsung dari database SIMRS lokal ke server BPJS Kesehatan / Kemenkes tanpa pernah melewati cloud pihak ketiga.',
    },
    {
        q: 'Bagaimana cara gateway menangani enkripsi AES-256 dan Signature BPJS?',
        a: 'SIMRS Gateway memiliki modul cipher bawaan yang secara otomatis menghitung HMAC-SHA256 timestamp signature, mengenkripsi payload request sebelum dikirim ke BPJS, dan mendekripsi balasan LZ-string secara instan. Aplikasi SIMRS Anda hanya perlu mengirim dan menerima data dalam format JSON standar.',
    },
    {
        q: 'Apakah bisa digunakan untuk persiapan UAT (User Acceptance Test) BPJS Cabang?',
        a: 'Ya. Salah satu fungsi utama gateway ini adalah sebagai simulator pengujian. Seluruh 209 endpoint telah dilengkapi dengan mock response dummy yang terstruktur rapi sesuai standar katalog BPJS Kesehatan, sehingga tim IT Anda siap melakukan demonstrasi UAT di hadapan tim IT Kantor Cabang BPJS.',
    },
];

const openFaq = ref(0);
const toggleFaq = (idx) => {
    openFaq.value = openFaq.value === idx ? -1 : idx;
};
</script>

<template>
    <!-- Meta description, keywords & Open Graph ada di app.blade.php (sekali untuk seluruh situs). -->
    <Head title="SIMRS Gateway - Infrastruktur Bridging BPJS Kesehatan & SATUSEHAT" />

    <div class="min-h-screen bg-stone-100 font-sans text-slate-900 antialiased selection:bg-orange-500 selection:text-white">
        <!-- Topbar utiliti -->
        <div class="bg-blue-900 text-blue-50">
            <div class="mx-auto flex max-w-7xl items-center justify-between gap-3 px-4 py-2 text-[11px] sm:px-6 lg:px-8">
                <p class="flex min-w-0 items-center gap-2">
                    <svg class="h-3.5 w-3.5 shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M10 20l4-16m4 4l4 4-4 4M6 16l-4-4 4-4" />
                    </svg>
                    <span class="truncate font-medium">Dikembangkan Setiawandev · Fork dari github.com/ahmadfauzirahman99/simrs-api · Lisensi MIT</span>
                </p>
                <div class="hidden shrink-0 items-center gap-4 font-semibold sm:flex">
                    <a :href="githubRepoUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1 transition hover:text-orange-300">
                        <svg class="h-3.5 w-3.5" fill="currentColor" viewBox="0 0 24 24"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.53 1.032 1.53 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z" /></svg>
                        <span>GitHub</span>
                    </a>
                    <a :href="saweriaUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1 transition hover:text-orange-300">
                        <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
                        </svg>
                        <span>Saweria</span>
                    </a>
                    <a :href="telegramGeneralUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1 transition hover:text-orange-300">
                        <svg class="h-3.5 w-3.5" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm4.64 6.8c-.15 1.58-.8 5.42-1.13 7.19-.14.75-.42 1-.68 1.03-.58.05-1.02-.38-1.58-.75-.88-.58-1.38-.94-2.23-1.5-.99-.65-.35-1.01.22-1.59.15-.15 2.71-2.48 2.76-2.69a.2.2 0 00-.05-.18c-.06-.05-.14-.03-.21-.02-.09.02-1.49.95-4.22 2.79-.4.27-.76.41-1.08.4-.36-.01-1.04-.2-1.55-.37-.63-.2-1.12-.31-1.08-.66.02-.18.27-.36.74-.55 2.92-1.27 4.86-2.11 5.83-2.51 2.78-1.16 3.35-1.36 3.73-1.36.08 0 .27.02.39.12.1.08.13.19.14.27-.01.06.01.24 0 .38z" /></svg>
                        <span>Telegram</span>
                    </a>
                </div>
            </div>
        </div>

        <!-- Navbar -->
        <header class="sticky top-3 z-50 px-4 sm:px-6">
            <div class="mx-auto max-w-7xl rounded-2xl bg-blue-950 text-white shadow-xl ring-1 ring-white/10">
            <div class="flex h-16 items-center justify-between gap-3 px-4 sm:px-5">
                <Link href="/" class="flex shrink-0 items-center gap-3">
                    <span class="flex h-10 w-10 items-center justify-center rounded-xl bg-orange-500 text-white">
                        <svg class="h-5 w-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M3 13h4l2-5 4 10 2-5h6" />
                        </svg>
                    </span>
                    <span class="leading-tight">
                        <span class="block text-sm font-extrabold tracking-tight">SIMRS Gateway</span>
                        <span class="block text-[11px] font-medium text-blue-200">Setiawandev · v3.0</span>
                    </span>
                </Link>

                <nav class="hidden items-center gap-1 text-[13px] font-semibold text-blue-100 lg:flex">
                    <a
                        v-for="link in navLinks"
                        :key="link.label"
                        :href="link.href"
                        class="rounded-xl px-3 py-1.5 transition hover:bg-white/10 hover:text-white"
                    >
                        <span>{{ link.label }}</span>
                        <span v-if="link.count" class="ml-1.5 rounded-full bg-orange-500 px-1.5 py-0.5 text-[10px] font-bold text-white">{{ link.count }}</span>
                    </a>
                </nav>

                <div class="flex shrink-0 items-center gap-2">
                    <a
                        :href="telegramGeneralUrl"
                        target="_blank"
                        rel="noopener noreferrer"
                        title="Chat Telegram"
                        class="hidden h-9 w-9 items-center justify-center rounded-xl bg-white/10 text-white transition hover:bg-white/20 md:inline-flex"
                    >
                        <svg class="h-4 w-4" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm4.64 6.8c-.15 1.58-.8 5.42-1.13 7.19-.14.75-.42 1-.68 1.03-.58.05-1.02-.38-1.58-.75-.88-.58-1.38-.94-2.23-1.5-.99-.65-.35-1.01.22-1.59.15-.15 2.71-2.48 2.76-2.69a.2.2 0 00-.05-.18c-.06-.05-.14-.03-.21-.02-.09.02-1.49.95-4.22 2.79-.4.27-.76.41-1.08.4-.36-.01-1.04-.2-1.55-.37-.63-.2-1.12-.31-1.08-.66.02-.18.27-.36.74-.55 2.92-1.27 4.86-2.11 5.83-2.51 2.78-1.16 3.35-1.36 3.73-1.36.08 0 .27.02.39.12.1.08.13.19.14.27-.01.06.01.24 0 .38z" /></svg>
                    </a>
                    <template v-if="user">
                        <Link
                            v-if="user.is_superadmin"
                            href="/admin/dashboard"
                            class="rounded-xl bg-white px-4 py-2 text-xs font-bold text-blue-950 transition hover:bg-blue-50"
                        >
                            Admin Panel
                        </Link>
                        <template v-else>
                            <Link href="/client/dashboard" class="hidden rounded-xl px-4 py-2 text-xs font-bold text-blue-100 ring-1 ring-white/20 transition hover:bg-white/10 sm:inline-flex">
                                Portal Klien
                            </Link>
                            <Link href="/admin/bpjs-tester" class="rounded-xl bg-orange-500 px-4 py-2 text-xs font-bold text-white transition hover:bg-orange-600">
                                Studio
                            </Link>
                        </template>
                    </template>
                    <template v-else>
                        <Link href="/register" class="hidden rounded-xl px-4 py-2 text-xs font-bold text-blue-100 ring-1 ring-white/20 transition hover:bg-white/10 sm:inline-flex">
                            Daftar
                        </Link>
                        <Link href="/login" class="rounded-xl bg-orange-500 px-4 py-2 text-xs font-bold text-white transition hover:bg-orange-600">
                            Masuk
                        </Link>
                    </template>
                    <button
                        type="button"
                        class="inline-flex h-10 w-10 items-center justify-center rounded-xl bg-white/10 text-white transition hover:bg-white/20 lg:hidden"
                        @click="isMobileMenuOpen = !isMobileMenuOpen"
                        :aria-expanded="isMobileMenuOpen"
                        aria-label="Menu Navigasi Mobile"
                    >
                        <svg v-if="!isMobileMenuOpen" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M4 7h16M4 12h16M4 17h16" />
                        </svg>
                        <svg v-else class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" />
                        </svg>
                    </button>
                </div>
            </div>

            <!-- Drawer mobile -->
            <transition
                enter-active-class="transition duration-200 ease-out"
                enter-from-class="opacity-0 -translate-y-2"
                enter-to-class="opacity-100 translate-y-0"
                leave-active-class="transition duration-150 ease-in"
                leave-from-class="opacity-100 translate-y-0"
                leave-to-class="opacity-0 -translate-y-2"
            >
                <div v-if="isMobileMenuOpen" class="max-h-[calc(100vh-6rem)] space-y-3 overflow-y-auto border-t border-white/10 px-4 py-4 lg:hidden">
                    <a
                        v-for="link in navLinks"
                        :key="link.label"
                        :href="link.href"
                        @click="closeMobileMenu"
                        class="flex items-center justify-between rounded-xl bg-white/5 px-4 py-2.5 text-sm font-semibold text-blue-50 transition hover:bg-white/10"
                    >
                        <span class="inline-flex items-center gap-2.5">
                            <svg class="h-4 w-4 text-orange-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" :d="link.icon" />
                            </svg>
                            {{ link.label }}
                        </span>
                        <span v-if="link.count" class="rounded-full bg-white/10 px-2 py-0.5 text-[10px] font-bold text-blue-100">{{ link.count }}</span>
                    </a>
                    <div class="grid grid-cols-2 gap-2 pt-1">
                        <a :href="telegramGeneralUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center justify-center gap-1.5 rounded-xl bg-white/10 px-3 py-2.5 text-xs font-bold text-white">
                            Telegram
                        </a>
                        <a :href="saweriaUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center justify-center gap-1.5 rounded-xl bg-orange-500 px-3 py-2.5 text-xs font-bold text-white">
                            Saweria
                        </a>
                    </div>
                    <template v-if="user">
                        <Link v-if="user.is_superadmin" href="/admin/dashboard" @click="closeMobileMenu" class="flex items-center justify-center rounded-xl bg-white px-4 py-2.5 text-xs font-bold text-blue-950">
                            Masuk ke Dashboard Superadmin
                        </Link>
                        <div v-else class="grid grid-cols-2 gap-2">
                            <Link href="/client/dashboard" @click="closeMobileMenu" class="flex items-center justify-center rounded-xl px-3 py-2.5 text-xs font-bold text-blue-100 ring-1 ring-white/20">
                                Portal Klien
                            </Link>
                            <Link href="/admin/bpjs-tester" @click="closeMobileMenu" class="flex items-center justify-center rounded-xl bg-orange-500 px-3 py-2.5 text-xs font-bold text-white">
                                Buka Studio UI
                            </Link>
                        </div>
                    </template>
                    <template v-else>
                        <div class="grid grid-cols-2 gap-2">
                            <Link href="/register" @click="closeMobileMenu" class="flex items-center justify-center rounded-xl px-3 py-2.5 text-xs font-bold text-blue-100 ring-1 ring-white/20">
                                Daftar Klien
                            </Link>
                            <Link href="/login" @click="closeMobileMenu" class="flex items-center justify-center rounded-xl bg-orange-500 px-3 py-2.5 text-xs font-bold text-white">
                                Masuk Console
                            </Link>
                        </div>
                    </template>
                </div>
            </transition>
            </div>
        </header>

        <!-- HERO SPLIT ASIMETRIS -->
        <section class="border-b border-stone-200 bg-white">
            <div class="mx-auto grid max-w-7xl items-center gap-12 px-4 py-14 sm:px-6 lg:grid-cols-[1.15fr_0.85fr] lg:gap-10 lg:px-8 lg:py-20">
                <!-- Kolom kiri: tipografi raksasa -->
                <div>
                    <div class="reveal inline-flex items-center gap-2 rounded-full border border-orange-200 bg-orange-50 px-4 py-1.5 text-xs font-bold text-orange-600">
                        <span class="relative flex h-2 w-2">
                            <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-orange-500 opacity-60"></span>
                            <span class="relative inline-flex h-2 w-2 rounded-full bg-orange-500"></span>
                        </span>
                        Open Source MIT — On-Premise — Siap UAT BPJS
                    </div>
                    <h1 class="reveal mt-6 text-4xl font-extrabold leading-[1.05] tracking-tight text-slate-900 sm:text-6xl lg:text-7xl">
                        Satu gateway lokal untuk <span class="text-blue-800">seluruh bridging BPJS</span> rumah sakit.
                    </h1>
                    <p class="reveal mt-6 max-w-xl text-sm leading-relaxed text-slate-600 sm:text-base">
                        SIMRS Anda cukup mengirim JSON polos. Gateway mengurus signature HMAC-SHA256, enkripsi AES-256,
                        dan dekripsi LZ-string secara otomatis — <strong class="text-slate-900">209 endpoint terverifikasi</strong>
                        dalam 7 modul BPJS Kesehatan &amp; SATUSEHAT, tanpa biaya lisensi.
                    </p>
                    <div class="reveal mt-8 flex flex-col gap-3 sm:flex-row sm:flex-wrap">
                        <Link href="/login" class="inline-flex items-center justify-center gap-2 rounded-2xl bg-blue-800 px-7 py-3.5 text-sm font-bold text-white transition hover:-translate-y-0.5 hover:bg-blue-900 hover:shadow-xl">
                            <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
                            </svg>
                            Coba Demo Simulator
                        </Link>
                        <a :href="githubZipUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center justify-center gap-2 rounded-2xl border-2 border-stone-900 px-7 py-3.5 text-sm font-bold text-stone-900 transition hover:-translate-y-0.5 hover:bg-stone-900 hover:text-white hover:shadow-xl">
                            <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
                            </svg>
                            Unduh Source (.ZIP)
                        </a>
                        <a :href="saweriaUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center justify-center gap-2 rounded-2xl bg-orange-500 px-7 py-3.5 text-sm font-bold text-white transition hover:-translate-y-0.5 hover:bg-orange-600 hover:shadow-xl">
                            <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
                            </svg>
                            Donasi Saweria
                        </a>
                    </div>
                    <dl class="reveal mt-10 grid max-w-xl grid-cols-2 gap-3 sm:grid-cols-4">
                        <div class="rounded-3xl border border-stone-200 bg-stone-50 px-4 py-4 transition hover:-translate-y-1 hover:shadow-lg">
                            <dt class="text-[11px] font-bold uppercase tracking-wider text-slate-500">Endpoint</dt>
                            <dd class="mt-1 text-2xl font-extrabold text-blue-800">209</dd>
                        </div>
                        <div class="rounded-3xl border border-stone-200 bg-stone-50 px-4 py-4 transition hover:-translate-y-1 hover:shadow-lg">
                            <dt class="text-[11px] font-bold uppercase tracking-wider text-slate-500">Modul BPJS</dt>
                            <dd class="mt-1 text-2xl font-extrabold text-blue-800">7</dd>
                        </div>
                        <div class="rounded-3xl border border-stone-200 bg-stone-50 px-4 py-4 transition hover:-translate-y-1 hover:shadow-lg">
                            <dt class="text-[11px] font-bold uppercase tracking-wider text-slate-500">Biaya Lisensi</dt>
                            <dd class="mt-1 text-2xl font-extrabold text-orange-600">Rp 0</dd>
                        </div>
                        <div class="rounded-3xl border border-stone-200 bg-stone-50 px-4 py-4 transition hover:-translate-y-1 hover:shadow-lg">
                            <dt class="text-[11px] font-bold uppercase tracking-wider text-slate-500">Lisensi</dt>
                            <dd class="mt-1 text-2xl font-extrabold text-blue-800">MIT</dd>
                        </div>
                    </dl>
                </div>

                <!-- Kolom kanan: panel status API mengambang + badge melayang -->
                <div class="reveal relative mx-auto w-full max-w-md lg:max-w-none">
                    <div class="relative rounded-3xl border border-stone-200 bg-white p-6 shadow-2xl transition hover:-translate-y-1 hover:shadow-2xl">
                        <div class="flex items-center justify-between">
                            <div class="flex items-center gap-2.5">
                                <span class="relative flex h-3 w-3">
                                    <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-orange-500 opacity-60"></span>
                                    <span class="relative inline-flex h-3 w-3 rounded-full bg-orange-500"></span>
                                </span>
                                <p class="text-xs font-extrabold uppercase tracking-widest text-slate-500">Gateway Status · Live</p>
                            </div>
                            <span class="rounded-full bg-blue-800 px-2.5 py-1 font-mono text-[10px] font-bold text-white">v3.0</span>
                        </div>
                        <div class="mt-5 space-y-2.5 font-mono text-[11px]">
                            <div class="flex items-center justify-between rounded-2xl bg-stone-50 px-4 py-3 ring-1 ring-stone-200">
                                <span class="font-bold text-slate-700">GET /api/v1/bpjs/peserta</span>
                                <span class="rounded-full bg-blue-800 px-2 py-0.5 font-bold text-white">200 · 41ms</span>
                            </div>
                            <div class="flex items-center justify-between rounded-2xl bg-stone-50 px-4 py-3 ring-1 ring-stone-200">
                                <span class="font-bold text-slate-700">POST /api/v1/vclaim/sep</span>
                                <span class="rounded-full bg-blue-800 px-2 py-0.5 font-bold text-white">200 · 68ms</span>
                            </div>
                            <div class="flex items-center justify-between rounded-2xl bg-stone-50 px-4 py-3 ring-1 ring-stone-200">
                                <span class="font-bold text-slate-700">GET /api/v1/antrean/status</span>
                                <span class="rounded-full bg-orange-500 px-2 py-0.5 font-bold text-white">200 · 35ms</span>
                            </div>
                            <div class="rounded-2xl bg-[#0B1226] px-4 py-3 text-slate-200">
                                <p class="text-slate-400">$ response.peserta.nama</p>
                                <p class="mt-1 font-bold text-white">"SUTRISNO WIJAYA" <span class="font-normal text-slate-400">— terdekripsi otomatis</span></p>
                            </div>
                        </div>
                        <div class="mt-5 flex items-center justify-between border-t border-stone-200 pt-4 text-[11px] font-semibold text-slate-500">
                            <span class="inline-flex items-center gap-1.5">
                                <svg class="h-3.5 w-3.5 text-blue-800" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z" />
                                </svg>
                                HMAC-SHA256 · AES-256 aktif
                            </span>
                            <span>gateway-rs.lan</span>
                        </div>
                    </div>
                    <!-- Badge melayang -->
                    <div class="float-badge absolute -right-2 -top-5 rounded-3xl border border-stone-200 bg-white px-4 py-3 shadow-xl sm:-right-5">
                        <p class="text-xl font-extrabold text-blue-800">209</p>
                        <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500">Endpoint aktif</p>
                    </div>
                    <div class="float-badge float-delayed absolute -bottom-5 -left-2 rounded-3xl border border-orange-200 bg-orange-500 px-4 py-3 text-white shadow-xl sm:-left-5">
                        <p class="text-xl font-extrabold">MIT</p>
                        <p class="text-[10px] font-bold uppercase tracking-wider text-orange-100">Gratis selamanya</p>
                    </div>
                </div>
            </div>
        </section>

        <!-- Navigasi section berbentuk pills -->
        <div class="sticky top-16 z-40 border-b border-stone-200 bg-white/95 backdrop-blur">
            <div class="mx-auto flex max-w-7xl items-center gap-2 overflow-x-auto px-4 py-2.5 sm:px-6 lg:px-8">
                <svg class="h-4 w-4 shrink-0 text-slate-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M4 6h16M4 12h16M4 18h7" />
                </svg>
                <a v-for="link in navLinks.slice(1)" :key="link.label" :href="link.href" class="inline-flex shrink-0 items-center gap-1.5 rounded-full border border-stone-300 bg-white px-3.5 py-1.5 text-xs font-bold text-slate-600 transition hover:-translate-y-0.5 hover:border-blue-800 hover:bg-blue-800 hover:text-white hover:shadow-md">
                    {{ link.label }}
                    <span v-if="link.count" class="rounded-full bg-stone-100 px-1.5 text-[10px] text-slate-600">{{ link.count }}</span>
                </a>
            </div>
        </div>

        <!-- ARSITEKTUR: TIMELINE VERTIKAL -->
        <section id="arsitektur" class="border-b border-stone-200 bg-white">
            <div class="mx-auto max-w-3xl scroll-mt-32 px-4 py-16 sm:px-6 lg:px-8 lg:py-20">
                <div class="reveal">
                    <p class="text-xs font-bold uppercase tracking-widest text-orange-600">01 — Cara Kerja</p>
                    <h2 class="mt-2 text-2xl font-extrabold tracking-tight text-slate-900 sm:text-4xl">SIMRS kirim JSON polos, gateway yang bekerja keras</h2>
                    <p class="mt-3 text-sm leading-relaxed text-slate-600">Tiga tahap alur data dari aplikasi rumah sakit hingga kembali sebagai JSON siap pakai. Seluruh kriptografi terjadi di server intranet Anda sendiri.</p>
                </div>
                <ol class="reveal relative mt-12 space-y-6 border-l-2 border-stone-200 pl-0">
                    <li class="relative pl-14">
                        <span class="absolute left-0 top-1 flex h-9 w-9 -translate-x-1/2 items-center justify-center rounded-full border-4 border-white bg-blue-800 text-sm font-extrabold text-white shadow-md">1</span>
                        <div class="rounded-3xl border border-stone-200 bg-stone-50 p-6 transition hover:-translate-y-1 hover:border-blue-800 hover:shadow-xl">
                            <h3 class="text-base font-extrabold text-slate-900">SIMRS mengirim payload sederhana</h3>
                            <p class="mt-2 text-sm leading-relaxed text-slate-600">Nomor kartu, NIK, atau nomor SEP dikirim via REST standar. Tanpa signature, tanpa enkripsi manual.</p>
                            <p class="mt-3 inline-block rounded-xl bg-white px-3 py-1.5 font-mono text-[11px] font-bold text-slate-700 ring-1 ring-stone-200">Plain JSON keluar</p>
                        </div>
                    </li>
                    <li class="relative pl-14">
                        <span class="absolute left-0 top-1 flex h-9 w-9 -translate-x-1/2 items-center justify-center rounded-full border-4 border-white bg-orange-500 text-sm font-extrabold text-white shadow-md ring-2 ring-orange-200">2</span>
                        <div class="rounded-3xl border-2 border-blue-800 bg-blue-50 p-6 shadow-lg transition hover:-translate-y-1 hover:shadow-xl">
                            <h3 class="text-base font-extrabold text-slate-900">Gateway Engine mengenkripsi otomatis</h3>
                            <p class="mt-2 text-sm leading-relaxed text-slate-600">HMAC-SHA256 dihitung, timestamp dicocokkan, payload dienkripsi AES-256-CBC lalu diteruskan ke server BPJS.</p>
                            <p class="mt-3 inline-block rounded-xl bg-blue-800 px-3 py-1.5 font-mono text-[11px] font-bold text-white">Kriptografi otomatis</p>
                        </div>
                    </li>
                    <li class="relative pl-14">
                        <span class="absolute left-0 top-1 flex h-9 w-9 -translate-x-1/2 items-center justify-center rounded-full border-4 border-white bg-stone-900 text-sm font-extrabold text-white shadow-md">3</span>
                        <div class="rounded-3xl border border-stone-200 bg-stone-50 p-6 transition hover:-translate-y-1 hover:border-blue-800 hover:shadow-xl">
                            <h3 class="text-base font-extrabold text-slate-900">Respons terdekripsi kembali ke SIMRS</h3>
                            <p class="mt-2 text-sm leading-relaxed text-slate-600">Balasan LZ-string BPJS didekripsi transparan dan dikembalikan sebagai JSON bersih siap ditampilkan.</p>
                            <p class="mt-3 inline-block rounded-xl bg-white px-3 py-1.5 font-mono text-[11px] font-bold text-slate-700 ring-1 ring-stone-200">JSON siap pakai</p>
                        </div>
                    </li>
                </ol>
            </div>
        </section>

        <!-- KATALOG: DAFTAR EXPANDABLE -->
        <section id="katalog" class="border-b border-stone-200 bg-stone-100">
            <div class="mx-auto max-w-4xl scroll-mt-32 px-4 py-16 sm:px-6 lg:px-8 lg:py-20">
                <div class="reveal text-center">
                    <p class="text-xs font-bold uppercase tracking-widest text-orange-600">02 — Katalog API</p>
                    <h2 class="mt-2 text-2xl font-extrabold tracking-tight text-slate-900 sm:text-4xl">7 modul BPJS, 209 endpoint</h2>
                    <p class="mx-auto mt-3 max-w-2xl text-sm text-slate-600">Klik setiap modul untuk membuka highlight-nya. Tiap modul dilengkapi skenario sukses &amp; gagal untuk demonstrasi UAT di hadapan tim IT Kantor Cabang BPJS.</p>
                </div>
                <div class="reveal mt-10 space-y-3">
                    <div
                        v-for="(item, idx) in moduleList"
                        :key="item.id"
                        :class="activeModuleTab === item.id ? 'border-blue-800 bg-white shadow-xl' : 'border-stone-200 bg-white hover:border-blue-800 hover:shadow-lg'"
                        class="overflow-hidden rounded-3xl border-2 transition hover:-translate-y-0.5"
                    >
                        <button
                            type="button"
                            @click="activeModuleTab = activeModuleTab === item.id ? '' : item.id"
                            class="flex w-full items-center gap-4 px-5 py-4 text-left sm:px-6 sm:py-5"
                            :aria-expanded="activeModuleTab === item.id"
                        >
                            <span :class="activeModuleTab === item.id ? 'bg-blue-800 text-white' : 'bg-stone-100 text-blue-800'" class="flex h-11 w-11 shrink-0 items-center justify-center rounded-2xl font-mono text-sm font-extrabold transition">{{ String(idx + 1).padStart(2, '0') }}</span>
                            <span class="min-w-0 flex-1">
                                <span class="flex flex-wrap items-center gap-2">
                                    <span class="text-sm font-extrabold text-slate-900 sm:text-base">{{ item.name }}</span>
                                    <span class="rounded-full bg-orange-500 px-2.5 py-0.5 text-[11px] font-bold text-white">{{ item.count }}</span>
                                </span>
                                <span class="mt-1 block text-[11px] font-semibold text-slate-500">{{ item.badge }}</span>
                            </span>
                            <svg :class="activeModuleTab === item.id ? 'rotate-180 text-blue-800' : 'text-slate-400'" class="h-5 w-5 shrink-0 transition" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
                            </svg>
                        </button>
                        <div v-if="activeModuleTab === item.id" class="border-t border-stone-200 bg-blue-50/60 px-5 py-4 sm:px-6 sm:pl-[5.75rem]">
                            <ul class="grid gap-2 sm:grid-cols-2">
                                <li v-for="(hl, hIdx) in item.highlights" :key="hIdx" class="inline-flex items-center gap-2 rounded-2xl bg-white px-3 py-2 text-xs font-medium text-slate-700 ring-1 ring-stone-200">
                                    <svg class="h-4 w-4 shrink-0 text-orange-500" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                                    </svg>
                                    {{ hl }}
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- KODE: CODE WINDOW DARK NAVY -->
        <section id="kode" class="border-b border-stone-200 bg-white">
            <div class="mx-auto max-w-4xl scroll-mt-32 px-4 py-16 sm:px-6 lg:px-8 lg:py-20">
                <div class="reveal text-center">
                    <p class="text-xs font-bold uppercase tracking-widest text-orange-600">03 — Integrasi</p>
                    <h2 class="mt-2 text-2xl font-extrabold tracking-tight text-slate-900 sm:text-4xl">Tempel 4 baris, langsung terhubung</h2>
                    <p class="mt-3 text-sm text-slate-600">Bahasa apa pun yang berjalan di server RS Anda — PHP, cURL, Node.js, Python.</p>
                </div>
                <div class="reveal mt-10 overflow-hidden rounded-3xl bg-[#0B1226] shadow-2xl ring-1 ring-stone-900 transition hover:shadow-2xl">
                    <div class="flex flex-wrap items-center justify-between gap-3 border-b border-white/10 px-5 py-4">
                        <div class="flex items-center gap-1.5">
                            <span class="h-3 w-3 rounded-full bg-orange-400"></span>
                            <span class="h-3 w-3 rounded-full bg-blue-400"></span>
                            <span class="h-3 w-3 rounded-full bg-stone-500"></span>
                            <span class="ml-2 font-mono text-[11px] font-bold text-slate-400">contoh-integrasi</span>
                        </div>
                        <div class="flex items-center gap-1 rounded-full bg-white/10 p-1">
                            <button
                                v-for="lang in ['php', 'curl', 'js', 'python']"
                                :key="lang"
                                type="button"
                                @click="activeCodeTab = lang"
                                :class="activeCodeTab === lang ? 'bg-orange-500 text-white shadow' : 'text-slate-400 hover:text-white'"
                                class="rounded-full px-4 py-1.5 font-mono text-[11px] font-bold uppercase transition"
                            >
                                {{ lang === 'js' ? 'JS' : lang }}
                            </button>
                        </div>
                    </div>
                    <pre class="overflow-x-auto whitespace-pre p-5 font-mono text-xs leading-relaxed text-slate-200 sm:p-6 sm:text-[13px]">{{ codeSnippets[activeCodeTab] }}</pre>
                    <div class="flex flex-col items-stretch justify-between gap-3 border-t border-white/10 bg-white/[0.03] px-5 py-4 sm:flex-row sm:items-center">
                        <p class="font-mono text-[11px] text-slate-400">GET /api/v1/bpjs/peserta — respons JSON terdekripsi otomatis</p>
                        <a :href="postmanCollectionUrl" class="inline-flex items-center justify-center gap-1.5 rounded-full bg-orange-500 px-4 py-2 text-[11px] font-bold text-white transition hover:-translate-y-0.5 hover:bg-orange-600 hover:shadow-lg">
                            <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
                            </svg>
                            Koleksi Postman (209)
                        </a>
                    </div>
                </div>
            </div>
        </section>

        <!-- OPEN SOURCE + ATRIBUT -->
        <section id="open-source" class="border-b border-stone-200 bg-stone-100">
            <div class="mx-auto max-w-7xl scroll-mt-32 px-4 py-16 sm:px-6 lg:px-8 lg:py-20">
                <div class="reveal text-center">
                    <p class="text-xs font-bold uppercase tracking-widest text-orange-600">04 — Open Source &amp; Donasi</p>
                    <h2 class="mt-2 text-2xl font-extrabold tracking-tight text-slate-900 sm:text-4xl">Gratis selamanya, milik komunitas RS Indonesia</h2>
                </div>

                <!-- Panel atribusi pembuat -->
                <div class="reveal mx-auto mt-10 max-w-4xl rounded-3xl border-2 border-blue-800 bg-white p-6 shadow-xl sm:p-8">
                    <div class="flex flex-col gap-5 sm:flex-row sm:items-start">
                        <span class="flex h-14 w-14 shrink-0 items-center justify-center rounded-3xl bg-blue-800 text-white shadow-md">
                            <svg class="h-7 w-7" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
                            </svg>
                        </span>
                        <div class="min-w-0 flex-1">
                            <h3 class="text-base font-extrabold text-slate-900 sm:text-lg">Atribusi Pembuat &amp; Sumber Kode</h3>
                            <p class="mt-2 text-xs leading-relaxed text-slate-700 sm:text-sm">
                                Halaman ini dikembangkan oleh <strong>Setiawandev</strong> — recode dan fork dari
                                <a href="https://github.com/ahmadfauzirahman99/simrs-api" target="_blank" rel="noopener noreferrer" class="font-bold text-blue-800 underline">github.com/ahmadfauzirahman99/simrs-api</a>.
                                Karya asli oleh <strong>Ahmad Fauzi Rahman</strong> (Backend Developer / SIMRS Developer),
                                dirilis dengan <strong>lisensi MIT</strong> — bebas dipakai, dimodifikasi, dan dipasang on-premise di RS Anda.
                            </p>
                            <div class="mt-4 flex flex-wrap gap-2">
                                <a :href="githubRepoUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1.5 rounded-full bg-stone-900 px-4 py-2 text-xs font-bold text-white transition hover:-translate-y-0.5 hover:bg-stone-700 hover:shadow-lg">
                                    <svg class="h-3.5 w-3.5" fill="currentColor" viewBox="0 0 24 24"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.53 1.032 1.53 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z" /></svg>
                                    Repo GitHub
                                </a>
                                <a :href="githubZipUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1.5 rounded-full bg-blue-800 px-4 py-2 text-xs font-bold text-white transition hover:-translate-y-0.5 hover:bg-blue-900 hover:shadow-lg">
                                    <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
                                    </svg>
                                    Download ZIP
                                </a>
                                <a :href="telegramGeneralUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1.5 rounded-full border-2 border-blue-800 bg-white px-4 py-2 text-xs font-bold text-blue-800 transition hover:-translate-y-0.5 hover:bg-blue-50 hover:shadow-lg">
                                    <svg class="h-3.5 w-3.5" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm4.64 6.8c-.15 1.58-.8 5.42-1.13 7.19-.14.75-.42 1-.68 1.03-.58.05-1.02-.38-1.58-.75-.88-.58-1.38-.94-2.23-1.5-.99-.65-.35-1.01.22-1.59.15-.15 2.71-2.48 2.76-2.69a.2.2 0 00-.05-.18c-.06-.05-.14-.03-.21-.02-.09.02-1.49.95-4.22 2.79-.4.27-.76.41-1.08.4-.36-.01-1.04-.2-1.55-.37-.63-.2-1.12-.31-1.08-.66.02-.18.27-.36.74-.55 2.92-1.27 4.86-2.11 5.83-2.51 2.78-1.16 3.35-1.36 3.73-1.36.08 0 .27.02.39.12.1.08.13.19.14.27-.01.06.01.24 0 .38z" /></svg>
                                    Telegram @{{ telegramHandle }}
                                </a>
                                <a :href="saweriaUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1.5 rounded-full bg-orange-500 px-4 py-2 text-xs font-bold text-white transition hover:-translate-y-0.5 hover:bg-orange-600 hover:shadow-lg">
                                    <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
                                    </svg>
                                    saweria.co/fauzirahman05
                                </a>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Kartu dukungan: 3 kolom + header berwarna + daftar centang -->
                <div class="mt-8 grid gap-5 lg:grid-cols-3">
                    <div class="reveal flex flex-col overflow-hidden rounded-3xl border border-stone-200 bg-white shadow-sm transition hover:-translate-y-1 hover:shadow-xl">
                        <div class="bg-blue-800 px-6 py-4">
                            <span class="inline-flex h-10 w-10 items-center justify-center rounded-2xl bg-white/15 text-white">
                                <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M5 12h14M5 12a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v4a2 2 0 01-2 2M5 12a2 2 0 00-2 2v4a2 2 0 002 2h14a2 2 0 002-2v-4a2 2 0 00-2-2m-2-4h.01M17 16h.01" />
                                </svg>
                            </span>
                            <h3 class="mt-3 text-base font-extrabold text-white">Pasang Sendiri — Rp 0</h3>
                        </div>
                        <div class="flex flex-1 flex-col p-6">
                            <ul class="flex-1 space-y-2.5 text-xs leading-relaxed text-slate-600">
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-blue-800" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Source lengkap Laravel 12 + Vue 3</li>
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-blue-800" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Tanpa lisensi, tanpa batas pasien</li>
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-blue-800" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Bebas modifikasi &amp; pasang on-premise</li>
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-blue-800" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Koleksi Postman 209 endpoint</li>
                            </ul>
                            <div class="mt-5 space-y-2">
                                <a :href="githubZipUrl" target="_blank" rel="noopener noreferrer" class="flex items-center justify-center gap-2 rounded-2xl bg-blue-800 px-4 py-2.5 text-xs font-bold text-white transition hover:bg-blue-900">Download Source (.ZIP)</a>
                                <a :href="postmanCollectionUrl" class="flex items-center justify-center gap-2 rounded-2xl border border-stone-300 bg-white px-4 py-2.5 text-xs font-bold text-slate-700 transition hover:bg-stone-100">Koleksi Postman</a>
                            </div>
                        </div>
                    </div>
                    <div class="reveal flex flex-col overflow-hidden rounded-3xl border-2 border-orange-500 bg-white shadow-lg transition hover:-translate-y-1 hover:shadow-xl">
                        <div class="bg-orange-500 px-6 py-4">
                            <span class="inline-flex h-10 w-10 items-center justify-center rounded-2xl bg-white/20 text-white">
                                <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
                                </svg>
                            </span>
                            <h3 class="mt-3 text-base font-extrabold text-white">Donasi Sukarela</h3>
                        </div>
                        <div class="flex flex-1 flex-col p-6">
                            <ul class="flex-1 space-y-2.5 text-xs leading-relaxed text-slate-600">
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-orange-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Mulai Rp 10.000 via QRIS / e-wallet</li>
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-orange-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Menjaga server demo publik tetap jalan</li>
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-orange-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Mendukung riset endpoint BPJS terbaru</li>
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-orange-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Nama donatur tampil di halaman apresiasi</li>
                            </ul>
                            <div class="mt-5">
                                <a :href="saweriaUrl" target="_blank" rel="noopener noreferrer" class="flex items-center justify-center gap-2 rounded-2xl bg-orange-500 px-4 py-2.5 text-xs font-extrabold text-white transition hover:bg-orange-600">Dukung di Saweria</a>
                                <p class="mt-2 text-center font-mono text-[11px] text-slate-500">saweria.co/fauzirahman05</p>
                            </div>
                        </div>
                    </div>
                    <div class="reveal flex flex-col overflow-hidden rounded-3xl border border-stone-200 bg-white shadow-sm transition hover:-translate-y-1 hover:shadow-xl">
                        <div class="bg-stone-900 px-6 py-4">
                            <span class="inline-flex h-10 w-10 items-center justify-center rounded-2xl bg-white/15 text-white">
                                <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z" />
                                </svg>
                            </span>
                            <h3 class="mt-3 text-base font-extrabold text-white">Setup &amp; Asistensi RS</h3>
                        </div>
                        <div class="flex flex-1 flex-col p-6">
                            <ul class="flex-1 space-y-2.5 text-xs leading-relaxed text-slate-600">
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-stone-900" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Instalasi on-premise di server RS</li>
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-stone-900" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Kustomisasi bridging SIMRS lama</li>
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-stone-900" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Pendampingan UAT BPJS Cabang</li>
                                <li class="flex items-start gap-2"><svg class="mt-0.5 h-4 w-4 shrink-0 text-stone-900" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" /></svg>Pelatihan tim IT rumah sakit</li>
                            </ul>
                            <div class="mt-5 space-y-2">
                                <a :href="waConsultUrl" target="_blank" rel="noopener noreferrer" class="flex items-center justify-center gap-2 rounded-2xl bg-blue-800 px-4 py-2.5 text-xs font-bold text-white transition hover:bg-blue-900">Konsultasi via WhatsApp</a>
                                <a :href="telegramConsultUrl" target="_blank" rel="noopener noreferrer" class="flex items-center justify-center gap-2 rounded-2xl border-2 border-blue-800 bg-white px-4 py-2.5 text-xs font-bold text-blue-800 transition hover:bg-blue-50">Konsultasi via Telegram</a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- APM TERANG + KIOSK TABLET-LANDSCAPE -->
        <section id="apm" ref="apmSection" class="apm-section border-b border-stone-200 bg-white">
            <div class="mx-auto max-w-7xl scroll-mt-32 px-4 py-16 sm:px-6 lg:px-8 lg:py-20">
                <div class="grid items-start gap-10 lg:grid-cols-[0.9fr_1.1fr] lg:gap-12">
                    <div class="reveal">
                        <p class="inline-flex items-center gap-2 rounded-full border border-blue-800 bg-blue-50 px-3 py-1 text-xs font-bold text-blue-900">
                            <span class="h-1.5 w-1.5 rounded-full bg-orange-500"></span>
                            05 — APM · Anjungan Pendaftaran Mandiri
                        </p>
                        <h2 class="mt-4 text-2xl font-extrabold tracking-tight text-slate-900 sm:text-4xl">Pasien daftar sendiri di kios, SEP terbit tanpa antre loket</h2>
                        <p class="mt-3 max-w-xl text-sm leading-relaxed text-slate-600">
                            Layar sentuh di lobi RS yang terhubung ke SIMRS dan BPJS Kesehatan. Pasien mandiri memilih poli
                            dan mencetak SEP, petugas loket fokus ke kasus khusus.
                        </p>
                        <div class="mt-6 flex rounded-2xl border border-stone-300 bg-stone-100 p-1.5">
                            <button
                                type="button"
                                @click="activeApmTab = 'checkin'"
                                :class="activeApmTab === 'checkin' ? 'bg-blue-800 text-white shadow' : 'text-slate-500 hover:text-slate-900'"
                                class="flex-1 rounded-2xl px-4 py-2.5 text-left text-xs transition"
                            >
                                <span class="block font-bold">Alur 1: Check-In Online</span>
                                <span class="block text-[11px] opacity-80">Sudah booking via Mobile JKN</span>
                            </button>
                            <button
                                type="button"
                                @click="activeApmTab = 'daftar'"
                                :class="activeApmTab === 'daftar' ? 'bg-orange-500 text-white shadow' : 'text-slate-500 hover:text-slate-900'"
                                class="flex-1 rounded-2xl px-4 py-2.5 text-left text-xs transition"
                            >
                                <span class="block font-bold">Alur 2: Daftar Mandiri</span>
                                <span class="block text-[11px] opacity-80">Pasien datang langsung</span>
                            </button>
                        </div>
                        <ol class="mt-6 space-y-3">
                            <li v-for="(step, idx) in apmSteps" :key="step.title" class="flex gap-3 rounded-3xl border border-stone-200 bg-white p-4 shadow-sm transition hover:-translate-y-0.5 hover:border-blue-800 hover:shadow-lg">
                                <span class="flex h-9 w-9 shrink-0 items-center justify-center rounded-full bg-blue-800 text-xs font-extrabold text-white">{{ idx + 1 }}</span>
                                <div>
                                    <h3 class="text-xs font-bold text-slate-900 sm:text-sm">{{ step.title }}</h3>
                                    <p class="mt-1 text-xs leading-relaxed text-slate-600">{{ step.body }}</p>
                                </div>
                            </li>
                        </ol>
                        <div class="mt-6 flex flex-col gap-2 sm:flex-row">
                            <Link href="/apm" class="inline-flex items-center justify-center gap-2 rounded-2xl bg-blue-800 px-6 py-3 text-xs font-bold text-white transition hover:-translate-y-0.5 hover:bg-blue-900 hover:shadow-xl sm:text-sm">
                                Buka Demo Kiosk APM
                                <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M14 5l7 7m0 0l-7 7m7-7H3" />
                                </svg>
                            </Link>
                            <a :href="telegramApmUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center justify-center gap-2 rounded-2xl border-2 border-stone-300 bg-white px-6 py-3 text-xs font-bold text-slate-700 transition hover:-translate-y-0.5 hover:border-orange-500 hover:text-orange-600 hover:shadow-lg sm:text-sm">
                                Jadi RS Uji Coba
                            </a>
                        </div>
                    </div>

                    <!-- Mock kiosk tablet-landscape terang -->
                    <figure class="reveal mx-auto w-full max-w-2xl">
                        <div class="rounded-3xl border border-stone-300 bg-stone-900 p-3 shadow-2xl">
                            <div class="overflow-hidden rounded-3xl bg-white">
                                <div class="flex items-center justify-between bg-blue-800 px-5 py-3 text-white">
                                    <span class="inline-flex items-center gap-2 text-xs font-bold">
                                        <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                            <path stroke-linecap="round" stroke-linejoin="round" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
                                        </svg>
                                        {{ activeApmTab === 'checkin' ? 'Check-In Mobile JKN' : 'Pendaftaran Mandiri BPJS' }}
                                    </span>
                                    <span class="font-mono text-[11px] text-blue-100">08:15 · Lobi RS</span>
                                </div>
                                <div class="grid gap-4 bg-stone-50 p-5 sm:grid-cols-2">
                                    <div class="rounded-3xl border border-stone-200 bg-white p-4">
                                        <p class="text-[11px] font-semibold text-slate-500">{{ activeApmTab === 'checkin' ? 'Verifikasi Kode Booking' : 'Langkah 3 dari 3' }}</p>
                                        <p class="mt-1 text-lg font-extrabold tracking-tight text-slate-900">{{ activeApmTab === 'checkin' ? 'Check-In Berhasil Terkonfirmasi' : 'Pendaftaran Berhasil' }}</p>
                                        <dl class="mt-3 divide-y divide-stone-200 rounded-2xl border border-stone-200 text-xs">
                                            <div class="flex justify-between bg-white px-4 py-2.5">
                                                <dt class="text-slate-500">{{ activeApmTab === 'checkin' ? 'Kode Booking' : 'Pasien' }}</dt>
                                                <dd class="font-bold text-slate-900">{{ activeApmTab === 'checkin' ? 'JKN261001' : 'Ny. Rahmawati' }}</dd>
                                            </div>
                                            <div class="flex justify-between bg-white px-4 py-2.5">
                                                <dt class="text-slate-500">Poliklinik</dt>
                                                <dd class="font-bold text-slate-900">Penyakit Dalam</dd>
                                            </div>
                                            <div class="flex justify-between bg-white px-4 py-2.5">
                                                <dt class="text-slate-500">Dokter</dt>
                                                <dd class="font-bold text-slate-900">dr. Budi Santoso, Sp.PD</dd>
                                            </div>
                                            <div class="flex justify-between bg-orange-50 px-4 py-2.5">
                                                <dt class="text-slate-500">Status BPJS</dt>
                                                <dd class="font-bold text-orange-600">{{ activeApmTab === 'checkin' ? 'Task 3 Tercatat (Tiba di RS)' : 'SEP & Antrean Terbit' }}</dd>
                                            </div>
                                        </dl>
                                    </div>
                                    <div class="flex flex-col justify-between gap-3">
                                        <div class="flex items-center gap-2 rounded-3xl bg-blue-800 px-4 py-3.5 text-xs font-semibold text-white">
                                            <svg class="h-5 w-5 shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                                <path stroke-linecap="round" stroke-linejoin="round" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z" />
                                            </svg>
                                            Silakan ambil karcis antrean di bawah layar
                                        </div>
                                        <div class="grid grid-cols-2 gap-2">
                                            <div class="rounded-2xl border border-stone-200 bg-white px-3 py-3 text-center">
                                                <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500">Antrean</p>
                                                <p class="mt-0.5 font-mono text-lg font-extrabold text-blue-800">{{ activeApmTab === 'checkin' ? 'INT-001' : 'INT-002' }}</p>
                                            </div>
                                            <div class="rounded-2xl border border-stone-200 bg-white px-3 py-3 text-center">
                                                <p class="text-[10px] font-bold uppercase tracking-wider text-slate-500">Estimasi</p>
                                                <p class="mt-0.5 font-mono text-lg font-extrabold text-orange-600">±12 mnt</p>
                                            </div>
                                        </div>
                                        <div class="rounded-2xl bg-stone-900 px-4 py-2.5 text-center text-[11px] font-bold text-white">Celah printer thermal · karcis keluar otomatis</div>
                                    </div>
                                </div>
                            </div>
                            <div class="mx-auto mt-3 flex items-center justify-center gap-2">
                                <span class="h-1.5 w-1.5 rounded-full bg-stone-600"></span>
                                <span class="h-1.5 w-40 rounded-full bg-stone-700"></span>
                                <span class="h-1.5 w-1.5 rounded-full bg-stone-600"></span>
                            </div>
                        </div>
                        <div class="apm-ticket-clip mx-auto w-44">
                            <div class="apm-ticket mx-auto w-44 rounded-b-2xl border border-t-0 border-stone-300 bg-white px-3 pb-3 pt-2 text-center shadow-md" :class="{ 'is-printed': apmPrinted }">
                                <p class="text-[9px] font-semibold text-slate-500">Nomor antrean</p>
                                <p class="font-mono text-2xl font-extrabold text-blue-800">{{ activeApmTab === 'checkin' ? 'INT-001' : 'INT-002' }}</p>
                                <p class="text-[9px] text-slate-500">Poli Penyakit Dalam</p>
                            </div>
                        </div>
                        <div class="mt-4 text-center">
                            <Link href="/apm" class="inline-flex items-center gap-1.5 text-xs font-bold text-blue-800 hover:text-blue-900">
                                Buka Layar Kiosk Layar Penuh
                                <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M14 5l7 7m0 0l-7 7m7-7H3" />
                                </svg>
                            </Link>
                        </div>
                    </figure>
                </div>
            </div>
        </section>

        <!-- FAQ DUA KOLOM -->
        <section id="faq" class="bg-stone-100">
            <div class="mx-auto max-w-6xl scroll-mt-32 px-4 py-16 sm:px-6 lg:px-8 lg:py-20">
                <div class="reveal text-center">
                    <p class="text-xs font-bold uppercase tracking-widest text-orange-600">06 — Tanya Jawab</p>
                    <h2 class="mt-2 text-2xl font-extrabold tracking-tight text-slate-900 sm:text-4xl">Sering ditanyakan RS &amp; tim IT</h2>
                    <p class="mt-3 text-sm text-slate-600">Masih ragu? Sapa langsung via <a :href="telegramGeneralUrl" target="_blank" rel="noopener noreferrer" class="font-bold text-blue-800 underline">Telegram @{{ telegramHandle }}</a> atau <a :href="waGeneralUrl" target="_blank" rel="noopener noreferrer" class="font-bold text-blue-800 underline">WhatsApp</a>.</p>
                </div>
                <div class="mt-10 grid items-start gap-3 lg:grid-cols-2">
                    <div
                        v-for="(faq, idx) in faqs"
                        :key="idx"
                        :class="openFaq === idx ? 'border-blue-800 bg-white shadow-lg' : 'border-stone-200 bg-white hover:border-blue-800 hover:shadow-md'"
                        class="reveal rounded-3xl border-2 transition hover:-translate-y-0.5"
                    >
                        <button type="button" @click="toggleFaq(idx)" class="flex w-full items-center gap-3 px-5 py-4 text-left" :aria-expanded="openFaq === idx">
                            <span :class="openFaq === idx ? 'bg-orange-500 text-white' : 'bg-stone-100 text-blue-800'" class="flex h-8 w-8 shrink-0 items-center justify-center rounded-full font-mono text-xs font-extrabold transition">{{ idx + 1 }}</span>
                            <span class="flex-1 text-xs font-bold text-slate-900 sm:text-sm">{{ faq.q }}</span>
                            <svg :class="openFaq === idx ? 'rotate-180 text-blue-800' : 'text-slate-400'" class="h-4 w-4 shrink-0 transition" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M19 9l-7 7-7-7" />
                            </svg>
                        </button>
                        <div v-if="openFaq === idx" class="border-t border-stone-200 px-5 py-4 pl-[4.25rem]">
                            <p class="text-xs leading-relaxed text-slate-700 sm:text-sm">{{ faq.a }}</p>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- FOOTER NAVY GELAP -->
        <footer class="bg-[#0B1226] text-slate-300">
            <div class="mx-auto max-w-7xl px-4 py-14 sm:px-6 lg:px-8">
                <div class="grid gap-10 md:grid-cols-3">
                    <div>
                        <div class="flex items-center gap-2.5">
                            <span class="flex h-10 w-10 items-center justify-center rounded-2xl bg-blue-800 text-white">
                                <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M3 13h4l2-5 4 10 2-5h6" />
                                </svg>
                            </span>
                            <span class="text-sm font-extrabold text-white">SIMRS Gateway v3.0</span>
                            <span class="rounded-lg bg-orange-500 px-1.5 py-0.5 text-[10px] font-bold text-white">MIT</span>
                        </div>
                        <p class="mt-4 text-xs leading-relaxed text-slate-400">
                            Dikembangkan oleh <strong class="text-white">Setiawandev</strong> — recode dan fork dari
                            <a href="https://github.com/ahmadfauzirahman99/simrs-api" target="_blank" rel="noopener noreferrer" class="font-bold text-orange-300 underline">github.com/ahmadfauzirahman99/simrs-api</a>.
                            Kode sumber asli: <strong class="text-white">Ahmad Fauzi Rahman</strong> (Backend Developer / SIMRS Developer).
                            Dirilis bebas di bawah lisensi MIT untuk seluruh rumah sakit Indonesia.
                        </p>
                        <div class="mt-4 flex flex-wrap gap-2">
                            <a :href="githubRepoUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1.5 rounded-full bg-white/10 px-3 py-1.5 text-[11px] font-bold text-white ring-1 ring-white/15 transition hover:-translate-y-0.5 hover:bg-blue-800">
                                <svg class="h-3.5 w-3.5" fill="currentColor" viewBox="0 0 24 24"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.53 1.032 1.53 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z" /></svg>
                                GitHub Repo
                            </a>
                            <a :href="githubZipUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1.5 rounded-full bg-orange-500 px-3 py-1.5 text-[11px] font-bold text-white transition hover:-translate-y-0.5 hover:bg-orange-600">
                                <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" />
                                </svg>
                                Download ZIP
                            </a>
                        </div>
                    </div>
                    <div>
                        <h3 class="text-xs font-extrabold uppercase tracking-widest text-slate-400">Jelajah</h3>
                        <ul class="mt-4 grid grid-cols-2 gap-2 text-xs font-semibold text-slate-300">
                            <li v-for="link in navLinks.slice(1)" :key="link.label">
                                <a :href="link.href" class="transition hover:text-orange-300">{{ link.label }}</a>
                            </li>
                        </ul>
                        <h3 class="mt-6 text-xs font-extrabold uppercase tracking-widest text-slate-400">Kontak Developer</h3>
                        <ul class="mt-3 space-y-2 text-xs font-semibold">
                            <li>
                                <a :href="telegramGeneralUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1.5 text-slate-200 transition hover:text-orange-300">
                                    <svg class="h-3.5 w-3.5" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm4.64 6.8c-.15 1.58-.8 5.42-1.13 7.19-.14.75-.42 1-.68 1.03-.58.05-1.02-.38-1.58-.75-.88-.58-1.38-.94-2.23-1.5-.99-.65-.35-1.01.22-1.59.15-.15 2.71-2.48 2.76-2.69a.2.2 0 00-.05-.18c-.06-.05-.14-.03-.21-.02-.09.02-1.49.95-4.22 2.79-.4.27-.76.41-1.08.4-.36-.01-1.04-.2-1.55-.37-.63-.2-1.12-.31-1.08-.66.02-.18.27-.36.74-.55 2.92-1.27 4.86-2.11 5.83-2.51 2.78-1.16 3.35-1.36 3.73-1.36.08 0 .27.02.39.12.1.08.13.19.14.27-.01.06.01.24 0 .38z" /></svg>
                                    Telegram @{{ telegramHandle }}
                                </a>
                            </li>
                            <li>
                                <a :href="waGeneralUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1.5 text-slate-200 transition hover:text-orange-300">
                                    <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z" />
                                    </svg>
                                    WhatsApp Konsultasi
                                </a>
                            </li>
                            <li>
                                <a :href="saweriaUrl" target="_blank" rel="noopener noreferrer" class="inline-flex items-center gap-1.5 text-slate-200 transition hover:text-orange-300">
                                    <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
                                    </svg>
                                    saweria.co/fauzirahman05
                                </a>
                            </li>
                        </ul>
                    </div>
                    <div>
                        <h3 class="text-xs font-extrabold uppercase tracking-widest text-slate-400">Dukung Keberlanjutan</h3>
                        <p class="mt-4 text-xs leading-relaxed text-slate-400">Proyek ini hidup dari donasi komunitas. Setiap donasi menjaga server demo publik, riset endpoint BPJS terbaru, dan pengembangan APM.</p>
                        <a :href="saweriaUrl" target="_blank" rel="noopener noreferrer" class="mt-4 inline-flex items-center gap-2 rounded-2xl bg-orange-500 px-5 py-2.5 text-xs font-extrabold text-white transition hover:-translate-y-0.5 hover:bg-orange-600 hover:shadow-xl">
                            <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z" />
                            </svg>
                            Donasi via Saweria
                        </a>
                        <p class="mt-4 border-t border-white/10 pt-4 text-[11px] leading-relaxed text-slate-400">
                            Dukungan: <a :href="`mailto:${supportEmail}`" class="font-semibold text-slate-200 underline">{{ supportEmail }}</a>
                        </p>
                    </div>
                </div>
                <div class="mt-10 flex flex-col items-center justify-between gap-3 border-t border-white/10 pt-6 text-[11px] text-slate-400 sm:flex-row">
                    <p>SIMRS Gateway v3.0 oleh Setiawandev — fork dari github.com/ahmadfauzirahman99/simrs-api · Kode asli Ahmad Fauzi Rahman · Lisensi MIT</p>
                    <p class="font-mono">209 endpoint · 7 modul · on-premise</p>
                </div>
            </div>
        </footer>

        <!-- Tombol floating Telegram -->
        <a
            :href="telegramGeneralUrl"
            target="_blank"
            rel="noopener noreferrer"
            title="Konsultasi Telegram"
            class="fixed bottom-6 right-6 z-50 inline-flex items-center gap-2 rounded-full bg-blue-800 px-4 py-3 text-xs font-bold text-white shadow-xl transition hover:-translate-y-0.5 hover:bg-blue-900"
        >
            <svg class="h-4 w-4" fill="currentColor" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm4.64 6.8c-.15 1.58-.8 5.42-1.13 7.19-.14.75-.42 1-.68 1.03-.58.05-1.02-.38-1.58-.75-.88-.58-1.38-.94-2.23-1.5-.99-.65-.35-1.01.22-1.59.15-.15 2.71-2.48 2.76-2.69a.2.2 0 00-.05-.18c-.06-.05-.14-.03-.21-.02-.09.02-1.49.95-4.22 2.79-.4.27-.76.41-1.08.4-.36-.01-1.04-.2-1.55-.37-.63-.2-1.12-.31-1.08-.66.02-.18.27-.36.74-.55 2.92-1.27 4.86-2.11 5.83-2.51 2.78-1.16 3.35-1.36 3.73-1.36.08 0 .27.02.39.12.1.08.13.19.14.27-.01.06.01.24 0 .38z" /></svg>
            <span>Telegram @{{ telegramHandle }}</span>
        </a>
    </div>
</template>

<style scoped>
.reveal {
    opacity: 0;
    transform: translateY(18px);
    transition: opacity 0.6s ease-out, transform 0.6s ease-out;
}

.reveal.is-visible {
    opacity: 1;
    transform: none;
}

.float-badge {
    animation: float-y 5s ease-in-out infinite;
}

.float-delayed {
    animation-delay: 2.5s;
}

@keyframes float-y {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-8px); }
}

@media (prefers-reduced-motion: reduce) {
    .reveal {
        opacity: 1;
        transform: none;
        transition: none;
    }
    .float-badge {
        animation: none;
    }
}
</style>
