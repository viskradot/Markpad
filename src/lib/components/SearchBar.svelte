<script lang="ts">
	import { onMount } from 'svelte';
	import { fly } from 'svelte/transition';

	let {
		query = $bindable(''),
		currentIndex = $bindable(0),
		totalMatches = 0,
		onclose,
		onfindNext,
		onfindPrev,
	} = $props<{
		query: string;
		currentIndex: number;
		totalMatches: number;
		onclose?: () => void;
		onfindNext?: () => void;
		onfindPrev?: () => void;
	}>();

	let inputEl: HTMLInputElement | undefined = $state();

	export function focusInput() {
		inputEl?.focus();
		inputEl?.select();
	}

	onMount(() => {
		inputEl?.focus();
	});

	function handleInputKeyDown(e: KeyboardEvent) {
		if (e.key === 'Enter') {
			e.preventDefault();
			if (e.shiftKey) {
				onfindPrev?.();
			} else {
				onfindNext?.();
			}
		}
		if (e.key === 'Escape') {
			e.preventDefault();
			onclose?.();
		}
	}
</script>

<div class="search-bar" role="search" aria-label="Find in document" in:fly={{ y: -20, duration: 200 }}>
	<input
		bind:this={inputEl}
		bind:value={query}
		type="text"
		placeholder="Find..."
		aria-label="Search text"
		spellcheck="false"
		autocomplete="off"
		onkeydown={handleInputKeyDown}
	/>

	<button class="nav-btn" onclick={onfindPrev} disabled={totalMatches === 0} aria-label="Previous match" title="Previous match (Shift+Enter)">
		<svg width="14" height="14" viewBox="0 0 16 16" fill="currentColor">
			<path d="M3.2 10.26a.75.75 0 0 1 0-1.06L7.44 4.96a.75.75 0 0 1 1.06 0L12.74 9.2a.75.75 0 1 1-1.06 1.06L8 6.54 4.26 10.26a.75.75 0 0 1-1.06 0z"/>
		</svg>
	</button>

	<button class="nav-btn" onclick={onfindNext} disabled={totalMatches === 0} aria-label="Next match" title="Next match (Enter)">
		<svg width="14" height="14" viewBox="0 0 16 16" fill="currentColor">
			<path d="M12.8 5.74a.75.75 0 0 1 0 1.06L8.56 11.04a.75.75 0 0 1-1.06 0L3.26 6.8a.75.75 0 0 1 1.06-1.06L8 9.46l3.74-3.72a.75.75 0 0 1 1.06 0z"/>
		</svg>
	</button>

	<span class="match-count" aria-live="polite">
		{#if totalMatches > 0}
			{Math.min(currentIndex + 1, totalMatches)} / {totalMatches}
		{:else if query.length > 0}
			No results
		{/if}
	</span>

	<button class="nav-btn close-btn" onclick={onclose} aria-label="Close search" title="Close (Escape)">
		<svg width="14" height="14" viewBox="0 0 16 16" fill="currentColor">
			<path d="M3.72 3.72a.75.75 0 0 1 1.06 0L8 6.94l3.22-3.22a.75.75 0 1 1 1.06 1.06L9.06 8l3.22 3.22a.75.75 0 1 1-1.06 1.06L8 9.06l-3.22 3.22a.75.75 0 0 1-1.06-1.06L6.94 8 3.72 4.78a.75.75 0 0 1 0-1.06z"/>
		</svg>
	</button>
</div>

<style>
	.search-bar {
		position: absolute;
		top: 8px;
		right: 20px;
		z-index: 1000;
		display: flex;
		align-items: center;
		gap: 4px;
		padding: 4px 8px;
		background: var(--color-canvas-default);
		border: 1px solid var(--color-border-default);
		border-radius: 6px;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.25);
		font-family: var(--win-font);
	}

	input {
		border: 1px solid var(--color-border-default);
		border-radius: 4px;
		padding: 4px 8px;
		font-size: 13px;
		font-family: var(--win-font);
		background: var(--color-canvas-default);
		color: var(--color-fg-default);
		outline: none;
		width: 180px;
	}

	input:focus {
		border-color: var(--color-accent-fg);
	}

	.nav-btn {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 26px;
		height: 26px;
		border: none;
		border-radius: 4px;
		background: transparent;
		color: var(--color-fg-default);
		cursor: pointer;
		padding: 0;
	}

	.nav-btn:hover:not(:disabled) {
		background: var(--color-neutral-muted);
	}

	.nav-btn:disabled {
		opacity: 0.35;
		cursor: default;
	}

	.match-count {
		font-size: 12px;
		color: var(--color-fg-muted);
		min-width: 60px;
		text-align: center;
		white-space: nowrap;
		user-select: none;
	}

	.close-btn {
		margin-left: 2px;
	}
</style>
