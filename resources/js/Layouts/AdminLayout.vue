<script setup>
import { router, usePage } from '@inertiajs/vue3';
import { computed, ref } from 'vue';

const page = usePage();

defineProps({
    title: {
        type: String,
        default: 'Dashboard Gateway & Telemetri API',
    },
    subtitle: {
        type: String,
        default: 'Testing and simulation environment for BPJS & SATUSEHAT endpoints.',
    },
});

const isMobileSidebarOpen = ref(false);
const topSearch = ref('');

const user = computed(() => page.props.auth?.user ?? null);
const currentPath = computed(() => page.url ?? '');
const trialLimit = computed(() => Number(page.props.site_settings?.pricing_trial_limit ?? 50));

const submitTopSearch = () => {
    router.get('/admin/dashboard', {
        search: topSearch.value ? topSearch.value : undefined,
    });
};

const isActiveRoute = (href) => {
    if (href === '/') {
        return currentPath.value === '/' || currentPath.value === '';
    }
    return currentPath.value.startsWith(href);
};

const navigationGroups = computed(() => {
    const groups = [
        {
            title: 'Menu Utama',
            items: [
                {
                    label: 'Dashboard Gateway',
                    href: '/admin/dashboard',
                    icon: 'M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z',
                },
                {
                    label: 'Monitoring Task ID Antrol',
                    href: '/admin/antrol-monitor',
                    icon: 'M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z',
                },
                {
                    label: 'Panel Lembar SEP VClaim',
                    href: '/admin/sep',
                    icon: 'M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z',
                },
                {
                    label: 'Panggil Dokter & Poli',
                    href: '/poli/panggil',
                    icon: 'M11 5.882V19.24a1.76 1.76 0 01-3.417.592l-2.147-6.15M18 13a3 3 0 100-6M5.436 13.683A4.001 4.001 0 017 6h1.832c4.1 0 7.625-1.234 9.168-3v14c-1.543-1.766-5.067-3-9.168-3H7a3.988 3.988 0 01-1.564-.317z',
                },
                {
                    label: 'Display TV Ruang Tunggu',
                    href: '/display/antrean',
                    icon: 'M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z',
                },
                {
                    label: 'Kiosk APM Mandiri',
                    href: '/apm',
                    icon: 'M12 4v1m6 11h2m-6 0h-2v4m0-11v3m0 0h.01M12 12h4.01M16 20h4M4 12h4m12 0h.01M5 8h2a1 1 0 001-1V5a1 1 0 00-1-1H5a1 1 0 00-1 1v2a1 1 0 001 1zm12 0h2a1 1 0 001-1V5a1 1 0 00-1-1h-2a1 1 0 00-1 1v2a1 1 0 001 1zM5 20h2a1 1 0 001-1v-2a1 1 0 00-1-1H5a1 1 0 00-1 1v2a1 1 0 001 1z',
                },
            ],
        },
    ];

    if (user.value?.is_superadmin) {
        groups[0].items.push(
            {
                label: 'Kelola Klien & Lisensi',
                href: '/admin/clients',
                icon: 'M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z',
            },
            {
                label: 'Pengaturan Harga & Promo',
                href: '/admin/pricing',
                icon: 'M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z',
            }
        );
    }

    groups.push({
        title: 'Developer Suite',
        items: [
            {
                label: 'BPJS Tester Studio',
                href: '/admin/bpjs-tester',
                icon: 'M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z',
            },
            {
                label: 'Portal Klien (Demo View)',
                href: '/client/dashboard',
                icon: 'M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4',
            },
        ],
    });

    const telegramContact = (page.props.site_settings?.telegram_contact || 'FauziRahman95').replace('@', '');
    groups.push({
        title: 'Sistem & Support',
        items: [
            {
                label: 'Beranda Website',
                href: '/',
                icon: 'M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6',
            },
            {
                label: 'Postman Collection',
                href: '/downloads/postman-collection',
                icon: 'M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4',
                download: 'simrs-gateway-bpjs-postman-collection.json',
            },
            {
                label: 'Bantuan Telegram',
                href: `https://t.me/${telegramContact}`,
                icon: 'M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm4.64 6.8c-.15 1.58-.8 5.42-1.13 7.19-.14.75-.42 1-.68 1.03-.58.05-1.02-.38-1.58-.75-.88-.58-1.38-.94-2.23-1.5-.99-.65-.35-1.01.22-1.59.15-.15 2.71-2.48 2.76-2.69a.2.2 0 00-.05-.18c-.06-.05-.14-.03-.21-.02-.09.02-1.49.95-4.22 2.79-.4.27-.76.41-1.08.4-.36-.01-1.04-.2-1.55-.37-.63-.2-1.12-.31-1.08-.66.02-.18.27-.36.74-.55 2.92-1.27 4.86-2.11 5.83-2.51 2.78-1.16 3.35-1.36 3.73-1.36.08 0 .27.02.39.12.1.08.13.19.14.27-.01.06.01.24 0 .38z',
                target: '_blank',
            },
        ],
    });

    return groups;
});

const logout = () => {
    router.post('/logout');
};
</script>

<template>
    <div class="min-h-screen bg-stone-100 text-stone-800 flex">
        <!-- ========================================== -->
        <!-- 1. DESKTOP SIDEBAR (Navy)                  -->
        <!-- ========================================== -->
        <aside class="hidden lg:fixed lg:inset-y-0 lg:left-0 lg:flex lg:w-64 lg:flex-col bg-blue-950 border-r border-blue-900 z-40 text-blue-100 select-none">
            <!-- Brand & Product Header -->
            <div class="flex h-16 shrink-0 items-center px-5 border-b border-blue-900 bg-blue-950">
                <a href="/" class="flex items-center gap-3 group" title="Kembali ke Beranda">
                    <div class="flex h-9 w-9 items-center justify-center rounded-xl bg-orange-500 text-white font-bold shadow-xs group-hover:bg-orange-600 transition shrink-0">
                        <svg class="h-5 w-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4" />
                        </svg>
                    </div>
                    <div>
                        <div class="flex items-center gap-1.5">
                            <span class="font-bold text-sm text-white tracking-tight">SIMRS Gateway</span>
                            <span class="rounded bg-blue-900 text-orange-300 border border-blue-800 px-1.5 py-0.2 text-[10px] font-mono font-bold">v3.0</span>
                        </div>
                        <p class="text-[11px] text-blue-300 font-medium">Setiawandev &middot; Console Administrator</p>
                    </div>
                </a>
            </div>

            <!-- Navigation Links by Category -->
            <nav class="flex-1 overflow-y-auto px-3.5 py-5 space-y-6">
                <div v-for="(group, gIdx) in navigationGroups" :key="gIdx" class="space-y-1">
                    <p class="text-[11px] font-bold text-blue-300/70 uppercase tracking-wider px-3 mb-1.5">
                        {{ group.title }}
                    </p>

                    <a
                        v-for="item in group.items"
                        :key="item.href"
                        :href="item.href"
                        :target="item.target || null"
                        :download="item.download || null"
                        class="group flex items-center justify-between rounded-xl px-3 py-2.5 text-xs font-semibold transition"
                        :class="isActiveRoute(item.href)
                            ? 'bg-orange-500 text-white shadow-xs font-bold'
                            : 'text-blue-100/70 hover:bg-blue-900 hover:text-white'"
                    >
                        <div class="flex items-center gap-2.5 min-w-0">
                            <svg
                                class="h-4 w-4 shrink-0 transition"
                                :class="isActiveRoute(item.href) ? 'text-white' : 'text-blue-300 group-hover:text-orange-300'"
                                fill="none"
                                viewBox="0 0 24 24"
                                stroke="currentColor"
                            >
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="item.icon" />
                            </svg>
                            <span class="truncate">{{ item.label }}</span>
                        </div>
                    </a>
                </div>
            </nav>

            <!-- Bottom User Profile Row & Logout Button -->
            <div class="p-3 border-t border-blue-900 bg-blue-950">
                <div class="flex items-center justify-between gap-2.5 p-2 rounded-xl bg-blue-900 border border-blue-800 shadow-2xs">
                    <div class="flex items-center gap-2.5 min-w-0">
                        <div class="flex h-8 w-8 items-center justify-center rounded-lg bg-orange-500 text-white font-bold text-xs shrink-0 shadow-2xs">
                            {{ (user?.name || 'A').charAt(0).toUpperCase() }}
                        </div>
                        <div class="min-w-0">
                            <p class="text-xs font-bold text-white truncate">{{ user?.name || 'Superadmin' }}</p>
                            <p class="text-[10px] text-blue-300 truncate">{{ user?.email }}</p>
                        </div>
                    </div>

                    <button
                        type="button"
                        @click="logout"
                        class="h-8 w-8 rounded-lg text-blue-300 hover:text-rose-300 hover:bg-white/10 flex items-center justify-center transition shrink-0"
                        title="Keluar Akun"
                        aria-label="Keluar Akun"
                    >
                        <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" />
                        </svg>
                    </button>
                </div>
            </div>
        </aside>

        <!-- ========================================== -->
        <!-- 2. MOBILE SIDEBAR DRAWER (Slide-in on HP)  -->
        <!-- ========================================== -->
        <transition
            enter-active-class="transition-opacity duration-300 ease-out"
            enter-from-class="opacity-0"
            enter-to-class="opacity-100"
            leave-active-class="transition-opacity duration-200 ease-in"
            leave-from-class="opacity-100"
            leave-to-class="opacity-0"
        >
            <div
                v-if="isMobileSidebarOpen"
                class="fixed inset-0 z-50 bg-blue-950/60 backdrop-blur-xs lg:hidden"
                @click="isMobileSidebarOpen = false"
            ></div>
        </transition>

        <transition
            enter-active-class="transition duration-300 ease-out transform"
            enter-from-class="-translate-x-full"
            enter-to-class="translate-x-0"
            leave-active-class="transition duration-200 ease-in transform"
            leave-from-class="translate-x-0"
            leave-to-class="-translate-x-full"
        >
            <aside
                v-if="isMobileSidebarOpen"
                class="fixed inset-y-0 left-0 z-50 w-72 bg-blue-950 text-blue-100 flex flex-col border-r border-blue-900 shadow-2xl lg:hidden"
            >
                <div class="flex h-16 shrink-0 items-center justify-between px-5 border-b border-blue-900 bg-blue-950">
                    <div class="flex items-center gap-2.5">
                        <div class="flex h-8 w-8 items-center justify-center rounded-xl bg-orange-500 text-white font-bold shadow-xs">
                            <svg class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4" />
                            </svg>
                        </div>
                        <div>
                            <span class="font-bold text-sm text-white block leading-tight">SIMRS Gateway</span>
                            <span class="text-[11px] text-blue-300">Setiawandev</span>
                        </div>
                    </div>

                    <button
                        type="button"
                        @click="isMobileSidebarOpen = false"
                        class="p-1.5 rounded-lg text-blue-300 hover:text-white hover:bg-blue-900"
                    >
                        <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                        </svg>
                    </button>
                </div>

                <nav class="flex-1 overflow-y-auto px-4 py-5 space-y-6">
                    <div v-for="(group, gIdx) in navigationGroups" :key="gIdx" class="space-y-1">
                        <p class="text-[11px] font-bold text-blue-300/70 uppercase tracking-wider px-3 mb-1.5">
                            {{ group.title }}
                        </p>

                        <a
                            v-for="item in group.items"
                            :key="item.href"
                            :href="item.href"
                            :target="item.target || null"
                            :download="item.download || null"
                            @click="isMobileSidebarOpen = false"
                            class="group flex items-center justify-between rounded-xl px-3.5 py-2.5 text-xs font-semibold transition"
                            :class="isActiveRoute(item.href)
                                ? 'bg-orange-500 text-white shadow-xs font-bold'
                                : 'text-blue-100/70 hover:bg-blue-900 hover:text-white'"
                        >
                            <div class="flex items-center gap-3">
                                <svg
                                    class="h-4 w-4 shrink-0"
                                    :class="isActiveRoute(item.href) ? 'text-white' : 'text-blue-300 group-hover:text-orange-300'"
                                    fill="none"
                                    viewBox="0 0 24 24"
                                    stroke="currentColor"
                                >
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="item.icon" />
                                </svg>
                                <span>{{ item.label }}</span>
                            </div>
                        </a>
                    </div>
                </nav>

                <div class="p-4 border-t border-blue-900 bg-blue-950">
                    <button
                        type="button"
                        @click="logout"
                        class="w-full flex items-center justify-center gap-2 rounded-xl bg-rose-500/10 text-rose-200 hover:bg-rose-500/20 border border-rose-400/30 py-2.5 text-xs font-bold transition"
                    >
                        <span>Keluar dari Admin Panel</span>
                    </button>
                </div>
            </aside>
        </transition>

        <!-- ========================================== -->
        <!-- 3. MAIN CONTENT AREA (Offset by Sidebar)   -->
        <!-- ========================================== -->
        <div class="flex-1 lg:pl-64 flex flex-col min-h-screen w-full min-w-0">
            <!-- Top Header on Content Area -->
            <header class="sticky top-0 z-30 border-b border-stone-200 bg-white/95 shadow-xs backdrop-blur-md h-16 flex items-center justify-between gap-3 px-4 sm:px-6 lg:px-8">
                <!-- Left: Hamburger (Mobile) + Breadcrumb Context -->
                <div class="flex items-center gap-3 min-w-0">
                    <button
                        type="button"
                        class="inline-flex lg:hidden items-center justify-center h-9 w-9 rounded-lg border border-stone-200 bg-white text-stone-700 hover:bg-stone-100 transition shadow-2xs shrink-0"
                        @click="isMobileSidebarOpen = true"
                        aria-label="Buka Menu Sidebar"
                    >
                        <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
                        </svg>
                    </button>

                    <div class="min-w-0">
                        <div class="flex items-center gap-2">
                            <span class="text-xs font-semibold text-stone-400 uppercase tracking-wider hidden sm:inline">Admin /</span>
                            <h1 class="text-sm sm:text-base font-bold text-stone-900 tracking-tight truncate max-w-[200px] sm:max-w-md">{{ title }}</h1>
                        </div>
                    </div>
                </div>

                <!-- Center: Global Search -->
                <div class="hidden md:flex flex-1 max-w-md">
                    <div class="relative w-full">
                        <input
                            v-model="topSearch"
                            @keyup.enter="submitTopSearch"
                            type="text"
                            placeholder="Cari endpoint, faskes, IP..."
                            class="w-full rounded-xl border border-stone-200 bg-stone-100 pl-9 pr-3 py-2 text-xs text-stone-800 placeholder-stone-400 focus:border-blue-800 focus:bg-white focus:outline-hidden transition"
                        />
                        <svg class="h-4 w-4 text-stone-400 absolute left-3 top-2.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                        </svg>
                    </div>
                </div>

                <!-- Right: Quick Status, User & Actions -->
                <div class="flex items-center gap-2.5 sm:gap-3 shrink-0">
                    <div class="hidden sm:flex items-center gap-2 rounded-md bg-blue-50 px-2.5 py-1 text-xs font-semibold text-blue-800 ring-1 ring-inset ring-blue-800/20">
                        <span class="h-1.5 w-1.5 rounded-full bg-blue-800 animate-pulse"></span>
                        Bridging Ready
                    </div>

                    <div class="hidden lg:flex items-center gap-2.5 rounded-xl border border-stone-200 bg-stone-50 pl-1.5 pr-3 py-1">
                        <div class="flex h-7 w-7 items-center justify-center rounded-lg bg-blue-800 text-white font-bold text-[11px] shrink-0">
                            {{ (user?.name || 'A').charAt(0).toUpperCase() }}
                        </div>
                        <div class="min-w-0 leading-tight">
                            <p class="text-[11px] font-bold text-stone-900 truncate max-w-[120px]">{{ user?.name || 'Superadmin' }}</p>
                            <p class="text-[10px] text-stone-400 truncate max-w-[120px]">{{ user?.email }}</p>
                        </div>
                    </div>

                    <a
                        href="/downloads/postman-collection"
                        download="simrs-gateway-bpjs-postman-collection.json"
                        class="hidden md:inline-flex items-center gap-1.5 rounded-lg border border-orange-300 bg-orange-50 px-2.5 py-1.5 text-xs font-bold text-orange-900 hover:bg-orange-100 transition shadow-2xs"
                        title="Unduh Postman Collection JSON"
                    >
                        <span class="flex h-3.5 w-3.5 items-center justify-center rounded bg-orange-500 text-white font-bold text-[8px]">P</span>
                        <span>Postman</span>
                    </a>

                    <a
                        href="/"
                        class="inline-flex items-center gap-1.5 rounded-lg border border-stone-200 bg-white px-3 py-1.5 text-xs font-semibold text-stone-700 hover:bg-stone-50 transition shadow-2xs"
                        title="Lihat Halaman Depan"
                    >
                        <svg class="h-3.5 w-3.5 text-stone-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6" />
                        </svg>
                        <span class="hidden sm:inline">Beranda</span>
                    </a>
                </div>
            </header>

            <!-- Main Page Workspace -->
            <main class="flex-1 w-full max-w-[1920px] px-3 py-4 sm:px-6 lg:px-8 bg-stone-100">
                <slot />
            </main>
        </div>
    </div>
</template>
