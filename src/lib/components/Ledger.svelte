<script>
    import { onMount } from 'svelte';

    let live = $state(false);
    let sectionEl = $state();
    let activeEntry = $state(null);

    onMount(() => {
        if (typeof IntersectionObserver === 'undefined') {
            live = true;
            return;
        }

        const io = new IntersectionObserver(
            (entries) => {
                for (const entry of entries) {
                    if (entry.isIntersecting) {
                        live = true;
                        io.disconnect();
                    }
                }
            },
            { threshold: 0.08 }
        );

        io.observe(sectionEl);
        return () => io.disconnect();
    });

    const entries = [
        {
            id: 'edms',
            num: '01',
            name: 'EDMS',
            category: 'Enterprise / Document Management',
            url: 'https://edmslandingpage.vercel.app/',
            brief: 'Electronic document management system centralizing storage, versioning, and controlled access to organizational records with full audit trails.',
            metrics: [
                { label: 'ACCESS CONTROL', value: 'RBAC' },
                { label: 'VERSIONING', value: 'FULL' },
                { label: 'AUDIT TRAIL', value: '100%' }
            ]
        }
    ];

    const tally = [
        { label: 'SYSTEMS SHIPPED', value: entries.length.toString().padStart(2, '0') },
        { label: 'AVG UPTIME', value: '99.9%' },
        { label: 'STATUS', value: 'OPERATIONAL' }
    ];
</script>

<svelte:head>
    <noscript>
        <style>
            .ledger-root .animate-ledger-row { opacity: 1 !important; transform: none !important; }
        </style>
    </noscript>
</svelte:head>

<section
    bind:this={sectionEl}
    class:is-live={live}
    id="ledger"
    class="ledger-root w-full bg-black text-zinc-300 border-t border-zinc-900 select-none font-mono"
>
    <!-- Section Header -->
    <div class="h-24 border-b border-zinc-900/40 flex items-center justify-between px-8 md:px-16 bg-black z-10">
        <div class="flex items-center gap-2">
            <span class="w-1.5 h-1.5 rounded-full bg-zinc-100 opacity-80 shadow-[0_0_8px_rgba(255,255,255,0.5)]"></span>
            <span class="w-1.5 h-1.5 rounded-full bg-zinc-100 opacity-50"></span>
            <span class="w-1.5 h-1.5 rounded-full bg-zinc-100 opacity-30"></span>
        </div>
        <span class="text-lg md:text-2xl font-mono tracking-widest text-white uppercase">
            System Ledger
        </span>
    </div>

    <div class="max-w-6xl mx-auto px-4 md:px-8 pt-20 pb-8">
        <div class="px-4 md:px-4 mb-12 flex items-center justify-between">
            <span class="text-[10px] md:text-xs text-zinc-500 tracking-widest">// CASE STUDIES &amp; DEPLOYED METRICS</span>
            <span class="hidden md:inline text-[10px] text-zinc-600 tracking-widest">[ {entries.length} ENTRIES LOGGED ]</span>
        </div>

        <!-- Ledger Rows -->
        <div class="flex flex-col">
            {#each entries as entry, i (entry.id)}
                <svelte:element
                    this={entry.url ? 'a' : 'div'}
                    href={entry.url}
                    target={entry.url ? '_blank' : undefined}
                    rel={entry.url ? 'noopener noreferrer' : undefined}
                    aria-label={entry.url ? `${entry.name} — visit project site` : undefined}
                    role={entry.url ? undefined : 'contentinfo'}
                    class="animate-ledger-row group relative block border-b border-zinc-900/60 first:border-t transition-all duration-500 ease-out cursor-none"
                    class:bg-white={activeEntry === entry.id}
                    style="animation-delay: {i * 150}ms;"
                    onmouseenter={() => (activeEntry = entry.id)}
                    onmouseleave={() => (activeEntry = null)}
                >
                    <div class="grid grid-cols-1 md:grid-cols-12 gap-4 md:gap-8 items-start px-4 md:px-4 py-8">
                        <!-- Index / Category -->
                        <div class="md:col-span-3 flex md:flex-col gap-3 md:gap-1">
                            <span
                                class="text-2xl font-light tracking-widest transition-colors duration-500"
                                class:text-black={activeEntry === entry.id}
                                class:text-zinc-700={activeEntry !== entry.id}
                            >{entry.num}</span>
                            <span
                                class="text-[10px] tracking-wider transition-colors duration-500 self-center md:self-auto"
                                class:text-zinc-600={activeEntry === entry.id}
                                class:text-zinc-500={activeEntry !== entry.id}
                            >// {entry.category}</span>
                        </div>

                        <!-- Name / Brief -->
                        <div class="md:col-span-5">
                            <h3
                                class="font-sans text-lg md:text-xl font-medium tracking-tight transition-colors duration-500"
                                class:text-black={activeEntry === entry.id}
                                class:text-white={activeEntry !== entry.id}
                            >{entry.name}{#if entry.url}<span class="ml-2 text-sm font-mono" aria-hidden="true">↗</span>{/if}</h3>
                            <p
                                class="font-sans text-sm font-light mt-2 leading-relaxed transition-colors duration-500"
                                class:text-zinc-600={activeEntry === entry.id}
                                class:text-zinc-500={activeEntry !== entry.id}
                            >{entry.brief}</p>
                        </div>

                        <!-- Metrics -->
                        <div class="md:col-span-4 grid grid-cols-3 gap-2 md:gap-4">
                            {#each entry.metrics as metric (metric.label)}
                                <div class="flex flex-col gap-1 border-l pl-3 transition-colors duration-500"
                                    class:border-zinc-300={activeEntry === entry.id}
                                    class:border-zinc-800={activeEntry !== entry.id}
                                >
                                    <span
                                        class="text-base md:text-lg font-sans font-medium transition-colors duration-500"
                                        class:text-black={activeEntry === entry.id}
                                        class:text-white={activeEntry !== entry.id}
                                    >{metric.value}</span>
                                    <span
                                        class="text-[9px] tracking-wider transition-colors duration-500"
                                        class:text-zinc-600={activeEntry === entry.id}
                                        class:text-zinc-500={activeEntry !== entry.id}
                                    >{metric.label}</span>
                                </div>
                            {/each}
                        </div>
                    </div>
                </svelte:element>
            {/each}
        </div>

        <!-- Ledger Tally Frame -->
        <div class="mt-16 border border-zinc-900 bg-white text-black p-6 max-w-3xl mx-auto flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
            <div>
                <span class="text-[9px] text-zinc-500 block tracking-widest">// AGGREGATE LEDGER TOTALS</span>
                <h6 class="font-sans font-medium text-sm tracking-wide text-black">RUNNING SYSTEM TALLY</h6>
            </div>
            <div class="flex gap-6 text-[11px] text-zinc-500 border-t sm:border-t-0 sm:border-l border-zinc-300 pt-3 sm:pt-0 sm:pl-6 w-full sm:w-auto">
                {#each tally as item (item.label)}
                    <div><span class="text-black block">{item.value}</span>{item.label}</div>
                {/each}
            </div>
        </div>
    </div>
</section>

<style>
    .ledger-root :global(.animate-ledger-row) {
        opacity: 0;
        transform: translateY(16px);
    }

    .is-live :global(.animate-ledger-row) {
        animation: ledgerRowIn 600ms cubic-bezier(0.16, 1, 0.3, 1) forwards;
    }

    @keyframes ledgerRowIn {
        to {
            opacity: 1;
            transform: translateY(0);
        }
    }

    @media (prefers-reduced-motion: reduce) {
        .ledger-root :global(.animate-ledger-row) {
            animation: none !important;
            opacity: 1 !important;
            transform: none !important;
        }
    }
</style>
