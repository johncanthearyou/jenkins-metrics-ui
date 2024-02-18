<script>
    import { onMount } from 'svelte'
    var jobs = []
    onMount(async () => {
        const response = await fetch("http://localhost:8000")
        jobs = await response.json()
    })
</script>

<section>
    <h2>Jobs:</h2>
    <ul>
    {#each jobs as job}
        <li><a href={`/job?job=${encodeURIComponent(job)}`}>{job}</a></li>
    {/each}
    </ul>
</section>