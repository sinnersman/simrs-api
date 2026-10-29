<script setup>
import { Head, Link, useForm } from '@inertiajs/vue3';
import { ref } from 'vue';

const props = defineProps({
    whatsappContact: {
        type: String,
        default: '6281234567890',
    },
    trialLimit: {
        type: Number,
        default: 50,
    },
});

const showPassword = ref(false);

const form = useForm({
    name: '',
    email: '',
    phone: '',
    password: '',
});

const submit = () => {
    form.post('/register');
};
</script>

<template>
    <Head title="Daftar Akun Klien — SIMRS Gateway oleh Setiawandev" />

    <div class="min-h-screen bg-stone-100 text-slate-900 lg:grid lg:grid-cols-[1fr_1.1fr]">
        <!-- Panel kiri: brand -->
        <aside class="relative hidden flex-col justify-between overflow-hidden bg-blue-950 px-10 py-10 text-white lg:flex">
            <div>
                <Link href="/" class="inline-flex items-center gap-3">
                    <span class="flex h-11 w-11 items-center justify-center rounded-2xl bg-orange-500 text-white">
                        <svg class="h-6 w-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M3 13h4l2-5 4 10 2-5h6" />
                        </svg>
                    </span>
                    <span class="leading-tight">
                        <span class="block text-base font-extrabold tracking-tight">SIMRS Gateway</span>
                        <span class="block text-xs font-medium text-blue-200">oleh Setiawandev · v3.0</span>
                    </span>
                </Link>

                <h1 class="mt-14 text-4xl font-extrabold leading-[1.1] tracking-tight">
                    Dapatkan API Key instan untuk bridging BPJS rumah sakit.
                </h1>
                <p class="mt-4 max-w-md text-sm leading-relaxed text-blue-200">
                    Daftar sekali, langsung terima kredensial klien dan mulai uji coba {{ trialLimit }} request per hari.
                </p>

                <dl class="mt-10 grid max-w-md grid-cols-3 gap-3">
                    <div class="rounded-2xl bg-white/10 px-4 py-3">
                        <dt class="text-[11px] font-semibold uppercase tracking-wider text-blue-200">Endpoint</dt>
                        <dd class="mt-1 text-2xl font-extrabold">209</dd>
                    </div>
                    <div class="rounded-2xl bg-white/10 px-4 py-3">
                        <dt class="text-[11px] font-semibold uppercase tracking-wider text-blue-200">Modul</dt>
                        <dd class="mt-1 text-2xl font-extrabold">7</dd>
                    </div>
                    <div class="rounded-2xl bg-white/10 px-4 py-3">
                        <dt class="text-[11px] font-semibold uppercase tracking-wider text-blue-200">Lisensi</dt>
                        <dd class="mt-1 text-2xl font-extrabold">MIT</dd>
                    </div>
                </dl>

                <ul class="mt-8 max-w-md space-y-3 text-sm text-blue-100">
                    <li class="flex items-start gap-2.5">
                        <svg class="mt-0.5 h-4 w-4 shrink-0 text-orange-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                        </svg>
                        <span>API Key aktif seketika setelah pendaftaran</span>
                    </li>
                    <li class="flex items-start gap-2.5">
                        <svg class="mt-0.5 h-4 w-4 shrink-0 text-orange-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                        </svg>
                        <span>Kuota uji coba {{ trialLimit }} request per hari, gratis</span>
                    </li>
                    <li class="flex items-start gap-2.5">
                        <svg class="mt-0.5 h-4 w-4 shrink-0 text-orange-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                        </svg>
                        <span>Upgrade ke akses semaunya kapan saja via portal klien</span>
                    </li>
                </ul>
            </div>

            <p class="max-w-md text-[11px] leading-relaxed text-blue-300">
                Dikembangkan <span class="font-bold text-white">Setiawandev</span> — fork dari
                github.com/ahmadfauzirahman99/simrs-api · kode asli Ahmad Fauzi Rahman · lisensi MIT.
            </p>
        </aside>

        <!-- Panel kanan: form -->
        <main class="flex flex-col items-center justify-center px-4 py-10 sm:px-8">
            <div class="w-full max-w-md space-y-5">
                <Link
                    href="/"
                    class="inline-flex items-center gap-1.5 text-xs font-semibold text-slate-500 transition hover:text-blue-800"
                >
                    <svg class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
                    </svg>
                    <span>Kembali ke Beranda</span>
                </Link>

                <!-- Logo mobile -->
                <div class="flex items-center gap-3 lg:hidden">
                    <span class="flex h-10 w-10 items-center justify-center rounded-xl bg-orange-500 text-white">
                        <svg class="h-5 w-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M3 13h4l2-5 4 10 2-5h6" />
                        </svg>
                    </span>
                    <span class="leading-tight">
                        <span class="block text-sm font-extrabold">SIMRS Gateway</span>
                        <span class="block text-[11px] font-medium text-slate-500">oleh Setiawandev · v3.0</span>
                    </span>
                </div>

                <div>
                    <h2 class="text-2xl font-extrabold tracking-tight">
                        Daftar Akun Klien &amp; Developer
                    </h2>
                    <p class="mt-1 text-xs text-slate-500">
                        Buat akun dan terima API Key untuk integrasi bridging BPJS.
                    </p>
                </div>

                <div class="rounded-3xl border border-stone-200 bg-white p-6 shadow-sm sm:p-7">
                    <form class="space-y-4" @submit.prevent="submit">
                        <div class="space-y-1">
                            <label class="block text-xs font-semibold text-slate-700">
                                Nama Rumah Sakit / Klinik / Pengembang
                            </label>
                            <div class="relative rounded-xl border border-stone-300 bg-white transition focus-within:border-blue-800 focus-within:ring-1 focus-within:ring-blue-800">
                                <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                                    <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M3 21h18M5 21V7l7-4 7 4v14M9 21v-4h4v4M9 10h.01M15 10h.01M12 10h.01M9 14h.01M15 14h.01M12 14h.01" />
                                    </svg>
                                </div>
                                <input
                                    v-model="form.name"
                                    type="text"
                                    required
                                    placeholder="Contoh: RSU Permata Medika / PT Solusi SIMRS"
                                    class="w-full rounded-xl bg-transparent py-2.5 pl-9 pr-3 text-sm text-slate-900 outline-none placeholder:text-slate-400"
                                >
                            </div>
                            <p v-if="form.errors.name" class="text-[11px] font-medium text-rose-600">{{ form.errors.name }}</p>
                        </div>

                        <div class="space-y-1">
                            <label class="block text-xs font-semibold text-slate-700">
                                Alamat Email Resmi
                            </label>
                            <div class="relative rounded-xl border border-stone-300 bg-white transition focus-within:border-blue-800 focus-within:ring-1 focus-within:ring-blue-800">
                                <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                                    <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M16 12a4 4 0 10-8 0 4 4 0 008 0zm0 0v1.5a2.5 2.5 0 005 0V12a9 9 0 10-9 9m4.5-1.206a8.959 8.959 0 01-4.5 1.207" />
                                    </svg>
                                </div>
                                <input
                                    v-model="form.email"
                                    type="email"
                                    required
                                    placeholder="it.simrs@rumahsakit.com"
                                    class="w-full rounded-xl bg-transparent py-2.5 pl-9 pr-3 text-sm text-slate-900 outline-none placeholder:text-slate-400"
                                >
                            </div>
                            <p v-if="form.errors.email" class="text-[11px] font-medium text-rose-600">{{ form.errors.email }}</p>
                        </div>

                        <div class="space-y-1">
                            <div class="flex items-center justify-between">
                                <label class="block text-xs font-semibold text-slate-700">
                                    Nomor WhatsApp Aktif
                                </label>
                                <span class="text-[11px] font-medium text-slate-400">Untuk info aktivasi</span>
                            </div>
                            <div class="relative rounded-xl border border-stone-300 bg-white transition focus-within:border-blue-800 focus-within:ring-1 focus-within:ring-blue-800">
                                <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                                    <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M8 12h.01M12 12h.01M16 12h.01M21 12a9 9 0 01-13.2 7.9L3 21l1.1-4.8A9 9 0 1121 12z" />
                                    </svg>
                                </div>
                                <input
                                    v-model="form.phone"
                                    type="text"
                                    placeholder="081234567890"
                                    class="w-full rounded-xl bg-transparent py-2.5 pl-9 pr-3 text-sm text-slate-900 outline-none placeholder:text-slate-400"
                                >
                            </div>
                            <p v-if="form.errors.phone" class="text-[11px] font-medium text-rose-600">{{ form.errors.phone }}</p>
                        </div>

                        <div class="space-y-1">
                            <label class="block text-xs font-semibold text-slate-700">
                                Kata Sandi (Minimal 6 Karakter)
                            </label>
                            <div class="relative rounded-xl border border-stone-300 bg-white transition focus-within:border-blue-800 focus-within:ring-1 focus-within:ring-blue-800">
                                <div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                                    <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
                                    </svg>
                                </div>
                                <input
                                    v-model="form.password"
                                    :type="showPassword ? 'text' : 'password'"
                                    required
                                    placeholder="••••••••"
                                    class="w-full rounded-xl bg-transparent py-2.5 pl-9 pr-10 text-sm text-slate-900 outline-none placeholder:text-slate-400"
                                >
                                <button
                                    type="button"
                                    tabindex="-1"
                                    class="absolute inset-y-0 right-0 flex items-center pr-3 text-slate-400 transition hover:text-slate-600"
                                    @click="showPassword = !showPassword"
                                >
                                    <svg v-if="!showPassword" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
                                    </svg>
                                    <svg v-else class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-3.029m5.858.908a3 3 0 114.243 4.243M9.878 9.878l4.242 4.242M9.88 9.88l-3.29-3.29m7.532 7.532l3.29 3.29M3 3l18 18" />
                                    </svg>
                                </button>
                            </div>
                            <p v-if="form.errors.password" class="text-[11px] font-medium text-rose-600">{{ form.errors.password }}</p>
                        </div>

                        <button
                            type="submit"
                            class="inline-flex w-full items-center justify-center gap-2 rounded-xl bg-orange-500 px-4 py-3 text-sm font-bold text-white transition hover:bg-orange-600 active:scale-[0.99] disabled:cursor-not-allowed disabled:opacity-70"
                            :disabled="form.processing"
                        >
                            <svg v-if="form.processing" class="h-4 w-4 animate-spin" viewBox="0 0 24 24" fill="none">
                                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v8H4z"></path>
                            </svg>
                            <svg v-else class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M18 9v3m0 0v3m0-3h3m-3 0h-3m-2-5a4 4 0 11-8 0 4 4 0 018 0zM3 20a6 6 0 0112 0v1H3v-1z" />
                            </svg>
                            <span>{{ form.processing ? 'Membuat Akun & API Key...' : 'Daftar & Buat API Key' }}</span>
                        </button>

                        <p class="pt-1 text-center text-xs text-slate-600">
                            Sudah punya akun?
                            <Link href="/login" class="ml-1 font-bold text-blue-800 hover:underline">
                                Masuk di sini
                            </Link>
                        </p>
                    </form>
                </div>

                <p class="text-center text-[11px] text-slate-400">
                    Akun baru otomatis mendapatkan {{ trialLimit }} request uji coba gratis per hari · Koneksi terenkripsi AES-256
                </p>
            </div>
        </main>
    </div>
</template>
