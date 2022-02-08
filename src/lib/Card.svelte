<div class="border rounded-2xl w-80 text-left shadow-md m-2 inline-block overflow-hidden">    
    {#if source}
        <img class="border-b object-cover h-[5em] object-center transition-all w-[100%] hover:h-[10em]" alt={title} src={source}/>
    {/if}
    <div class="py-4 p-4">
        <h3 class="text-lg underline decoration-wavy decoration-[#3282B8]">{title}</h3>
        <p class="py-2"><slot></slot></p>
        
        {#if link}
            <a target="_blank" class="text-center text-[#3282B8] hover:text-[#EE6F57] transition-colors" href={link}>Visiter ↗</a>
        {/if}
        {#if github}
            <a target="_blank" class="text-center text-[#3282B8] hover:text-[#EE6F57] transition-colors" href={github}>Github ↗</a>
        {/if}
    </div>
</div>

<script>
    export let title;
    export let source;
    export let link;
    export let github;
</script>