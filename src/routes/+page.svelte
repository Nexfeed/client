<script>
	import { onMount } from 'svelte';

	// Generate 1000 dummy items
	const items = Array.from({ length: 1000 }, (_, i) => ({
		id: i + 1,
		name: `Item ${i + 1}`,
		description: `This is the description for item ${i + 1}`,
		price: Math.floor(Math.random() * 1000) + 10,
		category: ['Electronics', 'Clothing', 'Books', 'Home', 'Sports'][Math.floor(Math.random() * 5)]
	}));

	let container;
	let visibleItems = [];
	let startIndex = 0;
	let endIndex = 0;
	let containerHeight = 600;
	let itemHeight = 80;
	let buffer = 5; // Extra items to render for smooth scrolling

	const visibleCount = Math.ceil(containerHeight / itemHeight) + buffer * 2;

	function updateVisibleItems() {
		if (!container) return;

		const scrollTop = container.scrollTop;
		startIndex = Math.max(0, Math.floor(scrollTop / itemHeight) - buffer);
		endIndex = Math.min(items.length, startIndex + visibleCount);

		visibleItems = items.slice(startIndex, endIndex).map((item, index) => ({
			...item,
			virtualIndex: startIndex + index
		}));
	}

	onMount(() => {
		updateVisibleItems();
	});

	function handleScroll() {
		requestAnimationFrame(updateVisibleItems);
	}

	$: totalHeight = items.length * itemHeight;
	$: offsetY = startIndex * itemHeight;
</script>

<div class="min-h-screen bg-gray-50 p-8">
	<div class="mx-auto max-w-4xl">
		<h1 class="mb-2 text-4xl font-bold text-gray-900">Welcome to SvelteKit</h1>
		<p class="mb-8 text-gray-600">
			Visit <a
				href="https://svelte.dev/docs/kit"
				class="text-blue-600 underline hover:text-blue-800">svelte.dev/docs/kit</a
			> to read the documentation
		</p>

		<div class="rounded-lg bg-white p-6 shadow-lg">
			<p class="mb-4 text-sm text-gray-500">
				Showing items {startIndex + 1}-{endIndex} of {items.length}
			</p>

			<div
				bind:this={container}
				on:scroll={handleScroll}
				class="relative overflow-auto rounded-lg border border-gray-200"
				style="height: {containerHeight}px;"
			>
				<!-- Virtual spacer for total height -->
				<div style="height: {totalHeight}px; position: relative;">
					<!-- Visible items container -->
					<div class="absolute w-full" style="transform: translateY({offsetY}px);">
						{#each visibleItems as item (item.id)}
							<div
								class="flex items-center justify-between border-b border-gray-100 p-4 transition-colors duration-150 hover:bg-gray-50"
								style="height: {itemHeight}px;"
							>
								<div class="flex-1">
									<div class="flex items-center space-x-3">
										<div
											class="flex h-10 w-10 items-center justify-center rounded-full bg-gradient-to-br from-blue-400 to-purple-500 font-semibold text-white"
										>
											{item.id}
										</div>
										<div>
											<h3 class="font-medium text-gray-900">{item.name}</h3>
											<p class="text-sm text-gray-500">{item.description}</p>
										</div>
									</div>
								</div>
								<div class="text-right">
									<div class="text-lg font-semibold text-green-600">${item.price}</div>
									<div class="text-xs tracking-wide text-gray-400 uppercase">{item.category}</div>
								</div>
							</div>
						{/each}
					</div>
				</div>
			</div>

			<!-- Performance stats -->
			<div class="mt-4 text-xs text-gray-400">
				<p>
					Virtual index: {startIndex} - {endIndex} | Rendered items: {visibleItems.length} | Total items:
					{items.length}
				</p>
			</div>
		</div>
	</div>
</div>
