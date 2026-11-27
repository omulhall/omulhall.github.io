<script lang="ts">
    import { slide } from "svelte/transition";
    import { quintOut } from "svelte/easing";

    interface Props {
        items: string[];
        title?: string;
        speed?: number;
    }

    let { items, title, speed = 20 }: Props = $props();
    let expanded = $state(false);

    let trackItems = $derived(Array.isArray(items) && items.length > 0 ? [...items, ...items] : []);
</script>

{#if title}
    <div class="skill-scroller-header">
        <h2>{title}</h2>
    </div>
{/if}

{#if Array.isArray(items) && items.length > 0}
    <div
        class="skill-scroller-wrapper"
        role="group"
        aria-label={title ? `${title} scroller` : "Skill scroller"}
        onmouseenter={() => (expanded = true)}
        onmouseleave={() => (expanded = false)}
    >
        <div class="skill-scroller" class:expanded>
            <div
                class="skill-scroller-track"
                style={`--scroll-duration: ${speed}s;`}
                role="list"
                aria-label={title ? `${title} scroller` : "Skill scroller"}
            >
                {#each trackItems as item, index (item + "-" + index)}
                    <span
                        class="skill-scroller-item"
                        class:duplicate={index >= items.length}
                        role="listitem"
                        aria-hidden={index >= items.length ? "true" : undefined}
                    >
                        {item}
                    </span>
                {/each}
            </div>
        </div>

        {#if expanded}
            <div
                class="skill-grid"
                transition:slide={{ duration: 300, easing: quintOut }}
                role="list"
                aria-label={title ? `${title} list` : "Skill list"}
            >
                {#each items as item (item)}
                    <span class="skill-grid-item" role="listitem">{item}</span>
                {/each}
            </div>
        {/if}
    </div>
{/if}

<style>
    .skill-scroller-header h2 {
        margin-bottom: 0.5rem;
    }

    .skill-scroller-wrapper {
        position: relative;
        width: 100%;
        margin-bottom: 2rem;
    }

    .skill-scroller {
        position: relative;
        overflow: hidden;
        width: 100%;
        padding: 0.5rem 0;
        scrollbar-width: none;
        -ms-overflow-style: none;
    }

    .skill-scroller::-webkit-scrollbar {
        display: none;
    }

    /* Left and right fade overlays */
    .skill-scroller::before,
    .skill-scroller::after {
        content: "";
        position: absolute;
        top: 0;
        bottom: 0;
        width: 2.5rem;
        pointer-events: none;
        z-index: 1;
    }

    .skill-scroller::before {
        left: 0;
        background: linear-gradient(to right, rgb(var(--page)), transparent);
    }

    .skill-scroller::after {
        right: 0;
        background: linear-gradient(to left, rgb(var(--page)), transparent);
    }

    .skill-scroller.expanded::before,
    .skill-scroller.expanded::after {
        display: none;
    }

    .skill-scroller-track {
        display: flex;
        flex-wrap: nowrap;
        width: max-content;
        will-change: transform;
        text-align: center;
        animation: skill-scroll var(--scroll-duration, 20s) linear infinite;
    }

    .skill-scroller-item {
        flex: 0 0 auto;
        padding: 0.35rem 0.9rem;
        margin-right: 0.75rem;
        border: 1px solid rgb(var(--accent));
        border-radius: 1px;
        font-size: 0.85rem;
        white-space: nowrap;
        user-select: none;
    }

    /* Expanded grid floats over the layout — does not affect document flow */
    .skill-grid {
        position: absolute;
        top: 100%;
        left: 0;
        right: 0;
        z-index: 10;
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 0.75rem;
        padding: 0.75rem;
        background: rgb(var(--pastel-blue));
        border: 1px solid rgb(var(--accent));
        border-radius: 2px;
        box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
    }

    .skill-grid-item {
        padding: 0.35rem 0.5rem;
        border: 1px solid rgb(var(--accent));
        border-radius: 1px;
        font-size: 0.85rem;
        text-align: center;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        user-select: none;
    }

    @keyframes skill-scroll {
        0% {
            transform: translate3d(0, 0, 0);
        }
        100% {
            transform: translate3d(-50%, 0, 0);
        }
    }

    @media (prefers-reduced-motion: reduce) {
        .skill-scroller-track {
            animation: none;
            flex-wrap: wrap;
            justify-content: center;
        }

        .skill-scroller-item.duplicate {
            display: none;
        }
    }
</style>
