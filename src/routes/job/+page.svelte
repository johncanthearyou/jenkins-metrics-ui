<script>
    import { onMount } from "svelte";

    var job = null
    onMount(async () => {
		const selectedJob = new URLSearchParams(window.location.search).get("job")
        if (selectedJob == null) {
            alert("No job selected! Redirecting to jobs page...")
            window.location = "/"
        }

        const response = await fetch(`http://localhost:8000/job/${selectedJob}`)
        job = await response.json()
	});
</script>

<section>
    {#if job == null}
        <p>loading job...</p>
    {:else}
        <h2>{job["name"]}</h2>
        <p>mean duration (min): {job["mean"]}</p>
        <p>duration st. deviation (min): {job["st_dev"]}</p>
        <p>median duration (min): {job["median"]}</p>
        <p>build samples: {job["build_samples"]}</p>
        <ul>
        {#each job["builds"] as build}
            <li>{build["id"].replace("#", " #")}</li>
        {/each}
        </ul>
    {/if}
    <button on:click={() => window.location = "/"}> back to jobs</button>
</section>