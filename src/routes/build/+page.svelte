<script>
    import { onMount } from "svelte";

    var build = null
    var job = "job"
    var stages = []
    onMount(async () => {
		const selectedJob = new URLSearchParams(window.location.search).get("job")
        if (selectedJob == null) {
            alert("No job selected! Redirecting to jobs page...")
            window.location = "/"
        }
		
        const selectedBuild = new URLSearchParams(window.location.search).get("number")
        if (selectedBuild == null) {
            alert(`No build selected! Redirecting to ${selectedJob}...`)
            window.location = `/job?job=${encodeURIComponent(selectedJob)}`
        }

        const response = await fetch(`http://localhost:8000/build/${selectedBuild}/job/${selectedJob}`)
        build = await response.json()
        job = build["job"]
        stages = build["stages"]
	});
</script>

<section>
    {#if build == null}
        <p>loading build...</p>
    {:else}
        <h2>Build: {build["id"].replace("#", " #")}</h2>
        <ul>
            <p><b>Job:</b> {build["job"]}</p>
            <p><b>Build:</b> {build["build"]}</p>
            <p><b>Status:</b> {build["status"]}</p>
            <p><b>Queued For:</b> {(build["queueDurationMillis"]/60000).toFixed(3)}min</p>
            <p><b>Start:</b>{new Date(build["startTimeMillis"]).toLocaleTimeString()}, {new Date(build["startTimeMillis"]).toDateString()}</p>
            <p><b>Duration:</b> {(build["durationMillis"]/60000).toFixed(3)}min</p>
            <p><b>End:</b>{new Date(build["endTimeMillis"]).toLocaleTimeString()}, {new Date(build["endTimeMillis"]).toDateString()}</p>
            <p><b>Paused For:</b> {(build["pauseDurationMillis"]/60000).toFixed(3)}min</p>
        </ul>
        <h3>Stage Data</h3>
        <ul>
            {#each stages as stage}
                <p><b>{stage["name"]}</b></p>
                <ul>
                    <p><b>Start:</b>{new Date(stage["startTimeMillis"]).toLocaleTimeString()}, {new Date(stage["startTimeMillis"]).toDateString()}</p>
                    <p><b>Duration:</b> {(stage["durationMillis"]/60000).toFixed(3)}min</p>
                    <p><b>End:</b>{new Date(parseInt(stage["startTimeMillis"])+parseInt(stage["durationMillis"])).toLocaleTimeString()}, {new Date(parseInt(stage["startTimeMillis"])+parseInt(stage["durationMillis"])).toDateString()}</p>
                    <p><b>Paused For:</b> {(stage["pauseDurationMillis"]/60000).toFixed(3)}min</p>
                </ul>
            {/each}
        </ul>

    {/if}
    <button on:click={() => window.location=`/job?job=${build["job"]}`}> back to {job}</button>
</section>