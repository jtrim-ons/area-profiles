<script>
  import "../assets/app.bundle.css";
  import "./sixteens_main.css";
  import "./roboto_slab.css";
  import { data, metadata } from './data.js';
  import Profile from './Profile.svelte';
  import ONSHeader from './ONSHeader.svelte';
  import ONSFooter from './ONSFooter.svelte';
  import Breadcrumbs from './Breadcrumbs.svelte';
  import TodoSections from './TodoSections.svelte';
  import KeyStats from './KeyStats.svelte';
  import PrototypeWarning from './PrototypeWarning.svelte';
  import Select from './Select.svelte';
  import MapWidget from './MapWidget.svelte';

  let place, place_name;

  let keyStats = {
      population: 0,
      popIncrease: 0,
      sizeHectares: 0,
      popDensity: 0 
  };
  $: {
      place = data[selected];
      if (place) {
          console.log(place);
          keyStats = {
              population: place.data.population.val.c2011.all.toLocaleString(),
              popIncrease: Math.floor(place.data.population.val.change.all * place.data.population.val.c2001.all / 100).toLocaleString(),
              sizeHectares: Math.floor(place.data.population.val.c2011.all / place.data.density.val.c2011.density).toLocaleString(),
              popDensity: place.data.density.val.c2011.density.toLocaleString()
          };
      }
      place_name = place?.name || "Place Name";
  };

  let options = [];
  for (const i in data) {
  	let option = {};
  	option.value = i;
  	option.label = data[i].name;
  	options.push(option);
  }
  options.sort((a, b) => a.label.localeCompare(b.label, 'en', {ignorePunctuation: true}));
  let selected = "E09000002";

  console.log(selected);
</script>

<style>
:global(body) {
  margin: 0;
  font-family:
		system-ui,
		-apple-system, /* Firefox supports this but not yet `system-ui` */
		'Segoe UI',
		Roboto,
		Helvetica,
		Arial,
		sans-serif,
		'Apple Color Emoji',
		'Segoe UI Emoji';
}
</style>

<PrototypeWarning></PrototypeWarning>
<ONSHeader></ONSHeader>

    <div class="wrapper" id="top">

    <Breadcrumbs {place_name}></Breadcrumbs>

    <header class="mb-8" >
        <Select {options} bind:selected message='Select a place' />
        <h1 class="mb-1 p-0 text-3xl font-bold">{place_name}</h1>
        <h2 class="mt-0 text-lg">Local Authority
             ({place.code}) 
        </h2>
    </header>

    <div class="md:grid gap-4 grid-cols-3 mb-4">
        <div class="col-span-1 mb-6">
            <h2 class="text-base font-bold mb-2">Contents</h2>
            <ul class="m-0 mb-4 p-0 leading-tight">
                
                    <li class="m-0 mb-2 p-0 relative">
                        <span class="absolute">—</span>
                        <a class="pl-8 inline-block" href="#summary">Summary</a>
                    </li>
                
                    <li class="m-0 mb-2 p-0 relative">
                        <span class="absolute">—</span>
                        <a class="pl-8 inline-block" href="#areas-within-yorkshire-and-the-humber">Areas within {place_name}</a>
                    </li>
                
                    <li class="m-0 mb-2 p-0 relative">
                        <span class="absolute">—</span>
                        <a class="pl-8 inline-block" href="#population">Population</a>
                    </li>
                
                    <li class="m-0 mb-2 p-0 relative">
                        <span class="absolute">—</span>
                        <a class="pl-8 inline-block" href="#households">Households</a>
                    </li>
                
                    <li class="m-0 mb-2 p-0 relative">
                        <span class="absolute">—</span>
                        <a class="pl-8 inline-block" href="#housing">Housing</a>
                    </li>
                
                    <li class="m-0 mb-2 p-0 relative">
                        <span class="absolute">—</span>
                        <a class="pl-8 inline-block" href="#economic-activity">Economic Activity</a>
                    </li>
                
                    <li class="m-0 mb-2 p-0 relative">
                        <span class="absolute">—</span>
                        <a class="pl-8 inline-block" href="#education">Education</a>
                    </li>
                
                    <li class="m-0 mb-2 p-0 relative">
                        <span class="absolute">—</span>
                        <a class="pl-8 inline-block" href="#health">Health</a>
                    </li>
                
            </ul>
        </div>
        <div class="col-span-2 mb-6" id="summary">
            <h2 class="text-md font-bold mb-2">Summary</h2>
            <Profile {options} {selected} {place}></Profile>
            <!-- <p>This area profile covers the ... of {place_name} is compiled of data from the 2021 Census.</p>
            <p>The area profile covers areas within Yorkshire and The Humber and topics such as the population, households, housing, economy, education and health. </p>
            <p>The profile also contains all area related datasets.</p> -->
        </div>
    </div>
</div>

<section class="mb-8">
    <div class="wrapper">
<!--        <div data-hello></div>
        <div data-robojournalism></div>
        <div data-chart style="height: 100px; width: 250px"></div>
-->
    </div>
</section>

<KeyStats {keyStats}></KeyStats>

<MapWidget bind:selected/>

<section class="wrapper">
    <div>

    <TodoSections {place}></TodoSections>

    </div>
</section>

<ONSFooter></ONSFooter>            
