<script setup>
import { Head, Link, router, useForm } from '@inertiajs/vue3';
import { ref } from 'vue';

const props = defineProps({
    trialLimit: {
        type: Number,
        default: 50,
    },
    isLocal: {
        type: Boolean,
        default: false,
    },
});

const showPassword = ref(false);

const form = useForm({
    email: props.isLocal ? 'admin@simrs.local' : '',
    password: props.isLocal ? 'password' : '',
});

const submit = () => {
    form.post('/login');
};

const loginDemo = () => {
    router.post('/demo-login');
};
</script>

<template>
    <Head title="Masuk — SIMRS Gateway oleh Setiawandev" />

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
                    Satu pintu masuk ke seluruh bridging BPJS rumah sakit.
                </h1>
                <p class="mt-4 max-w-md text-sm leading-relaxed text-blue-200">
                    Masuk untuk membuka Integration Studio, simulator 209 endpoint, dan konsol API key klien Anda.
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
                        <span>Signature HMAC-SHA256 &amp; enkripsi AES-256 otomatis</span>
                    </li>
                    <li class="flex items-start gap-2.5">
                        <svg class="mt-0.5 h-4 w-4 shrink-0 text-orange-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                        </svg>
                        <span>On-premise — data tidak pernah lewat cloud pihak ketiga</span>
                    </li>
                    <li class="flex items-start gap-2.5">
                        <svg class="mt-0.5 h-4 w-4 shrink-0 text-orange-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                        </svg>
                        <span>Siap UAT bersama kantor cabang BPJS Kesehatan</span>
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
                        Masuk ke Gateway Console
                    </h2>
                    <p class="mt-1 text-xs text-slate-500">
                        Akses Integration Studio dan simulator bridging rumah sakit.
                    </p>
                </div>

                <div class="rounded-3xl border border-stone-200 bg-white p-6 shadow-sm sm:p-7">
                    <div
                        v-if="form.errors.email || form.errors.password"
                        class="mb-4 flex items-start gap-2.5 rounded-2xl border border-rose-200 bg-rose-50 p-3 text-xs text-rose-800"
                    >
                        <svg class="mt-0.5 h-4 w-4 shrink-0 text-rose-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" />
                        </svg>
                        <div class="space-y-0.5">
                            <p class="font-bold text-rose-900">Autentikasi Gagal</p>
                            <p class="text-[11px] leading-relaxed text-rose-700">
                                {{ form.errors.email || form.errors.password || 'Email atau password yang dimasukkan salah.' }}
                            </p>
                        </div>
                    </div>

                    <form class="space-y-4" @submit.prevent="submit">
                        <div class="space-y-1">
                            <label class="block text-xs font-semibold text-slate-700">
                                Email Pengguna
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
                                    placeholder="nama@simrs.local"
                                    class="w-full rounded-xl bg-transparent py-2.5 pl-9 pr-3 text-sm text-slate-900 outline-none placeholder:text-slate-400"
                                >
                            </div>
                        </div>

                        <div class="space-y-1">
                            <div class="flex items-center justify-between">
                                <label class="block text-xs font-semibold text-slate-700">
                                    Kata Sandi
                                </label>
                                <button
                                    type="button"
                                    tabindex="-1"
                                    class="text-[11px] font-medium text-blue-800 transition hover:text-blue-950"
                                    @click="showPassword = !showPassword"
                                >
                                    {{ showPassword ? 'Sembunyikan' : 'Tampilkan' }}
                                </button>
                            </div>
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
                                <path stroke-linecap="round" stroke-linejoin="round" d="M11 16l-4-4m0 0l4-4m-4 4h14m-5 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h7a3 3 0 013 3v1" />
                            </svg>
                            <span>{{ form.processing ? 'Memverifikasi Akses...' : 'Masuk ke Gateway Console' }}</span>
                        </button>

                        <div class="relative flex items-center justify-center">
                            <div class="w-full border-t border-stone-200"></div>
                            <span class="bg-white px-2.5 text-[10px] font-bold uppercase tracking-wider text-slate-400">atau</span>
                            <div class="w-full border-t border-stone-200"></div>
                        </div>

                        <button
                            type="button"
                            @click="loginDemo"
                            class="inline-flex w-full items-center justify-center gap-2 rounded-xl bg-blue-800 px-4 py-3 text-sm font-bold text-white transition hover:bg-blue-900"
                        >
                            <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M14.752 11.168l-3.197-2.132A1 1 0 0010 9.87v4.263a1 1 0 001.555.832l3.197-2.132a1 1 0 000-1.664z" />
                                <path stroke-linecap="round" stroke-linejoin="round" d="M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                            </svg>
                            <span>Coba Demo Gratis (Limit {{ trialLimit }} Request/Hari)</span>
                        </button>

                        <p class="pt-1 text-center text-xs text-slate-600">
                            Belum punya akun?
                            <Link href="/register" class="ml-1 font-bold text-blue-800 hover:underline">
                                Daftar Klien Baru &amp; Dapatkan API Key
                            </Link>
                        </p>
                    </form>
                </div>

                <p class="text-center text-[11px] text-slate-400">
                    Koneksi terenkripsi AES-256 · Hak akses sesuai akun terdaftar
                </p>
            </div>
        </main>
    </div>
</template>
