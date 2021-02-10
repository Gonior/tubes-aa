<script>
    export let ranking;
    export let title;
    export let genres;
    export let views;
    export let score = "";
    export let episode;
</script>
<div class="bg-purple-200 w-full rounded flex items-center py-2"> 
    <div class="w-1/12">
    <h1 class="font-bold text-center">{ranking}</h1>     
</div>
    <div class="w-11/12 pr-7 flex flex-col flex-wrap">
        <h1 class="font-semibold text-base">{title}</h1> 
        <div class="flex items-center justify-between w-full">
            <div>
                <h2 class="text-xs text-gray-500">{genres}</h2>
                <h2 class="text-xs text-gray-500">{views} views</h2>
                <h2 class="text-xs text-gray-500">{episode} eps</h2>
            </div>
            {#if score != ""} 
            <h3 class="text-xl"><span class="text-gray-600">Score </span><span class="text-black font-bold"> {parseFloat(score).toFixed(2)}</span></h3>
            {/if}
        </div>
    </div>
</div>