<script>
    import CardAnime from './CardAnime.svelte'
    import {createEventDispatcher} from 'svelte'
    import { fly} from 'svelte/transition'
    import {animes} from '../data/animes.js'
    const dispatch = createEventDispatcher()
    export let genreSelected = ""

    let fillterAnime = (genreSelected === "" ) ? [] : [...genreSelected.split(', ')]
    let animeselected = []
    let meanScore = 0
    let m = 4.5;
    const searchGenre = (a, k) => {
        let result = []
        k.forEach(e => {
            if (a.includes(e)) result.push(e)
        })
        return result.length === k.length
    }
    const getWeighScore = (S, v, m, C) => {
        let a = (v/(v+m)) * S;
        let b = (m/(m+v)) * C;
        return a + b
    }
    animes.forEach(e => {
        meanScore +=e.scores.reduce((acc, currentValue) => acc + currentValue)/e.views
    })
    meanScore = meanScore/animes.length
    animes.forEach(e => {
        if (searchGenre(e.genres, fillterAnime)) {
            let avg = e.scores.reduce((acc, currentValue) => acc + currentValue)/e.views
            e.score = getWeighScore(avg, e.views, m, meanScore)
            animeselected.push(e)
        }
    })
    //n
    animeselected = animeselected.sort((a,b) => b.score - a.score )
    const handleClose = () => {
        dispatch('close', {
            showModal : false
        })
    }

</script>
<div class="h-screen absolute inset-0 bg-purple-600 px-20 py-10" >
    <div class="flex flex-col bg-gray-100 w-full h-full rounded shadow-lg p-5" in:fly={{y : -100, duration:300}} out:fly={{y : -100, duration:300}}> 
        <div class="flex items-center justify-between mb-2">
            <h1 class="font-semibold ">Genre : <span class="italic text-sm text-gray-400">{genreSelected} </span></h1>
            <button class="text-gray-400 hover:text-black" on:click={handleClose}>
                <svg class="w-5 h-5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path fill="none" stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 19L5 5M19 5L5 19"/></svg>
            </button>
        </div>
        <div class="overflow-y-auto h-full space-y-2"> 
            {#each animeselected as anime, i}
                <CardAnime title={anime.title} score={anime.score} genres={anime.genres} views={anime.views} episode={anime.episode} ranking={i+1} />
            {/each}
        </div>
    </div>
</div>