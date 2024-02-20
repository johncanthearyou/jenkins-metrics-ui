<script>
    import { onMount } from "svelte";

    var job = null
    var builds = []
    onMount(async () => {
		const selectedJob = new URLSearchParams(window.location.search).get("job")
        if (selectedJob == null) {
            alert("No job selected! Redirecting to jobs page...")
            window.location = "/"
        }

        const response = await fetch(`http://localhost:8000/job/${selectedJob}`)
        job = await response.json()
        builds = job["builds"].sort(buildSorter)
	});

    function buildSorter(a, b) {
        if (parseInt(a["build"]) < parseInt(b["build"])) {
            return -1
        }
        if (parseInt(a["build"]) > parseInt(b["build"])) {
            return 1
        }
        return 0
    }
</script>

<section>
    {#if job == null}
        <p>loading job...</p>
    {:else}
        <h2>Job: {job["name"]}</h2>
        <p>mean duration (min): {job["mean"]}</p>
        <p>duration st. deviation (min): {job["st_dev"]}</p>
        <p>median duration (min): {job["median"]}</p>
        <p>build samples: {job["build_samples"]}</p>
        <ul>
        {#each builds as build}
            <li><a href={`build?job=${encodeURIComponent(build["job"])}&number=${build["build"]}`}>{build["id"]}</a></li>
        {/each}
        </ul>
    {/if}
    <button on:click={() => window.location = "/"}> back to jobs</button>
</section>