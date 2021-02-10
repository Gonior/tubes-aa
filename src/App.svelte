<script>

	import Modal from './components/Modal.svelte'
	import CardAnime from './components/CardAnime.svelte'
	import dataGenres from './data/data.js'
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';
	import {animes} from './data/animes.js'
	let genres = [...dataGenres]
	let showModal = false
	let showNotif = false
	let strSelected = ''
	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),

		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 100,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});

	
	const handleProcess = () => {
		
		strSelected = ''
		//pencarian genre yang dipilih
		let selected = genres.filter(e => e.selected)
		if (selected.length != 0) {
			for(let i = 0; i < selected.length ; i++) {
				strSelected += selected[i].name
				if (selected[i+1]!= undefined) strSelected += ", "
			}
			showModal = !showModal
			showNotif = false
		} else showNotif = true
	}
	const handleClose= () => {
		showModal = !showModal
	}
	const remove = (genre) =>  {
		genres= genres.filter(g => g !== genre);
	}

	const mark = (g, s) => {
		g.selected = s;
		remove(g);
		genres = genres.concat(g);
		showNotif = false
	}
</script>

<main class="bg-gray-200 p-2">
	{#if showModal}
		<Modal on:close={handleClose} genreSelected={strSelected} />
	{/if}
	<h1 class="font-semibold text-2xl">Algoritma Pengurutan Rekomendasi Anime berdasarkan Genre</h1>
  	<div class="flex flex-col lg:flex-row lg:space-x-2 space-y-2 ">
		<div class="bg-gray-300 rounded p-2 shadow-md mt-2 lg:w-1/2 flex-1">
			<h1 class="font-semibold mb-1">Daftar Genre</h1>
      		<div class="flex flex-wrap">
				{#each genres.filter(e => !e.selected).sort() as genre (genre.id)}
					<button 
						in:receive="{{key: genre.id}}"
						out:send="{{key: genre.id}}"
						animate:flip={{duration : 300}}
						on:click={() => mark(genre, true)} class="group mr-2 mb-2 focus:outline-none bg-gray-100 shadow-lg px-4 py-1 rounded-full ">
						{genre.name}
					</button>
				{/each}
      		</div>
    	</div>
    	<div class="bg-gray-300 rounded shadow py-3 px-2 lg:w-1/2 flex-none lg:max-h-full">
			<h1 class="font-semibold mb-1">Genre yang dipilih</h1>
			<div class="flex overflow-x-auto space-x-3 w-full py-1">
				{#each genres.filter(e => e.selected) as genre (genre.id)}
					<div
						in:receive="{{key: genre.id}}"
						out:send="{{key: genre.id}}"
						animate:flip={{duration : 300}}
						class="bg-gray-100 rounded flex items-center px-2 py-1 space-x-3 text-gray-600 hover:text-black flex-none">
						<div class="font-semibold">{genre.name}</div>
						<button on:click={() => mark(genre, false)} class="focus:outline-none ">
							<svg class="h-5 w-5"  xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
								<circle cx="12" cy="12" r="9" fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"/>
								<path fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 15L9 9M15 9l-6 6"/>
							</svg>
						</button>
					</div>
				{/each}
				
      		</div>
			<button on:click={handleProcess} class="transition duration-300 ease-in-out bg-purple-400 hover:bg-purple-600 font-semibold w-full py-2 mt-2 rounded text-white ">
				Tampilkan Anime
			</button>
			
			{#if showNotif}
				<h1 class="mt-1 text-base font-semibold text-red-500">Silakan pilih genre!</h1>
			{/if}
    	</div>
	  </div>
	  <div class="mt-2">
		  
		  <div class="space-y-2 lg:px-52"> 
			<h1 class="text-base font-semibold">List Anime (dalam database kami)</h1>
			{#each animes as anime, i}
                <CardAnime title={anime.title}  genres={anime.genres} views={anime.views} episode={anime.episode} ranking={i+1} />
            {/each}
        </div>
	  </div>
</main>

