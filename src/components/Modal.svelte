<script>
    import CardAnime from './CardAnime.svelte'
    import {createEventDispatcher, onMount} from 'svelte'
    import { fly} from 'svelte/transition'
    import {animes} from '../data/animes.js'

    export let genreSelected = ""

    const dispatch = createEventDispatcher()

    let fillterAnime = (genreSelected === "" ) ? [] : [...genreSelected.split(', ')]
    let animeselected = []
    let m = 4.5;
    let sortBy = 'score'

    const getWeighScore = (S, v, m, C) => {
        let a = (v/(v+m)) * S;
        let b = (m/(m+v)) * C;
        return a + b
    }

    const handleClose = () => {
        dispatch('close', {
            showModal : false
        })
    }

    const getMean = (animes) => {
        let result = 0
        animes.forEach(anime => {
            result += anime.scores.reduce((acc, val) => acc + val)/anime.views
        })
        return result/animes.length
    }

    const getAnime = (genres, animes) => {
        let mean = getMean(animes)
        let result = []
        if (genres.length !== 0) { 
            for (let i = 0; i < animes.length; i++) {
                let indeks = 0
                for (let j = 0; j < genres.length; j ++) {
                    for (let x = 0; x < animes[i].genres.length; x++) {
                        if (animes[i].genres[x] === genres[j]) indeks++
                    }
                }
                if (indeks === genres.length) {
                    let avgScore = animes[i].scores.reduce((acc, val) => acc + val)/animes[i].views
                    
                    animes[i].score = getWeighScore(avgScore, animes[i].views, m, mean)
                    if (animes[i].score > 7.5) {
                        result.push(animes[i])
                    }

                }
            }
        }
        return result

    }
        
    const sorting = (animes) => {
        let result = []
        if (sortBy === "title") {
            result = [...animes.sort((a,b) =>  (a.title > b.title )? 1 : -1)]
        } else if (sortBy === "views") {
            result = [...animes.sort((a, b) => b.views- a.views) ]
        } else result = [...animes.sort((a, b) => b.score - a.score) ]

        return result
    }
    const handleChange = () => {
        animeselected = [...sorting(animeselected)]
    }
    
    onMount(() => {
        animeselected = [...getAnime(fillterAnime, animes)]
        handleChange()
    })
    
</script>
<div class="h-screen absolute inset-0 bg-purple-600 px-20 py-10" >
    <div class="flex flex-col bg-gray-100 w-full h-full rounded shadow-lg p-5" in:fly={{y : -100, duration:300}} out:fly={{y : -100, duration:300}}> 
        <div class="flex items-center justify-between mb-2">
            <h1 class="font-semibold ">Genre : <span class="italic text-sm text-gray-400">{genreSelected} </span></h1>
            <div class="flex">
                <div class="mr-10">
                    <span class="font-semibold ">Sort By :</span>
                    <!-- svelte-ignore a11y-no-onchange -->
                    <select class="px-4 py-2 rounded-sm outline-none" disabled={animeselected.length === 0} bind:value={sortBy} on:change={handleChange}>
                        <option value="score" selected>Score</option>
                        <option value="views">Views</option>
                        <option value="title">Title</option>
                    </select>
                </div>
                <button class="text-gray-400 hover:text-black" on:click={handleClose}>
                    <svg class="w-5 h-5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 19L5 5M19 5L5 19"/></svg>
                </button>
            </div>
            
        </div>
        <div class="overflow-y-auto h-full space-y-2"> 
            {#each animeselected as anime, i}
                <CardAnime title={anime.title} score={anime.score} genres={anime.genres} views={anime.views} episode={anime.episode} ranking={i+1} />
                {:else} 
                <div  class="flex flex-col h-full justify-center items-center px-24">
                    <h1 class="text-2xl ">Maaf, kami tidak punya rekomendasi anime genre</h1>
                    <h4 class="italic text-gray-500 text-2xl">{genreSelected}</h4>
                </div>
                
            {/each}
        </div>
    </div>
</div>