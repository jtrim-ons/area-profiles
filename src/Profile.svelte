<script>
	import { getHeadline, paragraphify } from './robo_utils.js';
	import { adjectify, plusminus, ordinal_suffix_of } from './robo_utils_pure_functions.js';
	import Select from './Select.svelte';
	import { data, regiondata, metadata } from './data.js';
	
	console.log(data) 
	
	var countryRank = [];
	
	export let options;
	export let selected;
	export let place;
	
	let total = options.length; 

	let breaks = [];
	for (let i=0; i<10; i++)
		breaks.push(Math.round((i * total) / 10));
	
    // TODO label is just a string in an array.  Do something nicer.
    let label = [""];
</script>

<style>
</style>

{#if selected != null}
<h1 style="color:red">This is a prototype using old data, and might not be accurate</h1>
<h2>{getHeadline(place, total, breaks).headline}</h2>
<p>
	<strong>{getHeadline(place, total, breaks).subhead}</strong>
</p>
{#each paragraphify(place, 4, data, regiondata, countryRank, label, breaks) as paragraph}
<p>{paragraph}</p>
{/each}
{:else}
<p>
	This demo illustrates different ways of presenting local authority level data from the 2011 Census and how it changed from 2001, making use of robo-journalism and automation techniques.
</p>
<p>
	<strong>Select a place from the dropdown above to get started, and click on the tabs to explore different kinds of outputs.</strong>
</p>
{/if}
