<script>
  import { csvParse } from 'd3-dsv';
  import wellknown from 'wellknown';
  import { bbox } from '@turf/turf';
  import Map from "./map/Map.svelte";
  import MapSource from "./map/MapSource.svelte";
  import MapLayer from "./map/MapLayer.svelte";
  import AreasWithinSelector from "./AreasWithinSelector.svelte";

  export let selected;

  const apiurl = 'https://pmd3-production-drafter-onsgeo.publishmydata.com/v1/sparql/live?query=';
  const colors = {
    grey: "#999999",
    leafGreen: "#0F8243"
  };
  const geogroups = ['E05', 'E06', 'E07', 'E08', 'E09', 'E10', 'W05', 'W06'];

  let map = null;
  let bounds;
  let geometry = {
    self: null,
    children: null,
    siblings: null
  };
  let hovered = null;

  async function getData(url) {
    let response = await fetch(url);
    let string = await response.text();
    let data = await csvParse(string);
    return data;
  }

  function getGeometry() {
    const queries = [
      {
        key: 'children',
        q: `
        PREFIX geosparql: <http://www.opengis.net/ont/geosparql#>
        PREFIX foi: <http://publishmydata.com/def/ontology/foi/>
        PREFIX statid: <http://statistics.data.gov.uk/id/statistical-geography/>
        PREFIX statdef: <http://statistics.data.gov.uk/def/statistical-geography#>
        PREFIX st: <http://ns.inria.fr/sparql-template/>
        SELECT DISTINCT ?code ?name ?geometry
        WHERE {
          ?child foi:parent statid:${selected} ;
                 foi:code ?code ;
                 statdef:officialname ?name ;
                 geosparql:hasGeometry ?geom .
          ?geom geosparql:asWKT ?geometry .
        } 
        `
      },
      {
        key: 'siblings',
        q: `
        PREFIX geosparql: <http://www.opengis.net/ont/geosparql#>
        PREFIX foi: <http://publishmydata.com/def/ontology/foi/>
        PREFIX statid: <http://statistics.data.gov.uk/id/statistical-geography/>
        PREFIX statdef: <http://statistics.data.gov.uk/def/statistical-geography#>
        PREFIX st: <http://ns.inria.fr/sparql-template/>
        SELECT ?code ?name ?geometry
        WHERE {
          statid:${selected} foi:parent ?parent .
          ?sibling foi:parent ?parent ;
                   foi:code ?code ;
                   statdef:officialname ?name ;
                   geosparql:hasGeometry ?geom .
          ?geom geosparql:asWKT ?geometry .
        }
        `
      }
    ];
    let result = {};
    queries.forEach(query => {
      getData(apiurl + encodeURIComponent(query.q))
      .then(arr => {
        let codes = [];
        let geojson = {
					"type": "FeatureCollection",
					"features": []
				};
        let selfgeojson = JSON.parse(JSON.stringify(geojson));
        arr.forEach(d => {
          if (!codes.includes(d.code) && geogroups.includes(d.code.substring(0,3))) {
            codes.push(d.code);
            let feature = {
              "type": "Feature",
              "geometry": wellknown.parse(d.geometry),
              "properties": {
                "code": d.code,
                "name": d.name
              }
            };
            if (query.key == "siblings" && d.code == selected) {
              selfgeojson.features.push(feature);
              geometry.self = selfgeojson;

              bounds = bbox(feature);
              if (map) {
                map.fitBounds(bounds, {padding: 100});
              }
            } else {
              geojson.features.push(feature);
            }
          }
        });
        geometry[query.key] = geojson;
      });
    });
  }

  $: map && selected && getGeometry();
</script>


<section class="mb-8">
{#if geometry.self}
<header class="sr-only">
  <h2>Map of {geometry.self.features[0].properties.name}</h2>
</header>
{/if}
<div class="relative" style="height:650px">
<!-- <Map bind:map>
  {#if geometry.children}
  <MapSource
    id="children"
    type="geojson"
    data={geometry.children}
    promoteId="code"
    maxzoom={12}>
    <MapLayer
      id="children-fill"
      source="children"
      type="fill"
      paint={{
        'fill-color': colors.leafGreen,
        'fill-opacity': ['case',
          ['==', ['feature-state', 'hovered'], true], 0.3,
					0.1
				]
      }}
      hover={true}
      {hovered}
      order="place_other"/>
    <MapLayer
      id="children-line"
      source="children"
      type="line"
      paint={{
        'line-color': colors.leafGreen,
        'line-width': 1
      }}
      order="place_other"/>
  </MapSource>
  {/if}
  {#if geometry.siblings}
  <MapSource
    id="siblings"
    type="geojson"
    data={geometry.siblings}
    promoteId="code"
    maxzoom={12}>
    <MapLayer
      id="siblings-fill"
      source="siblings"
      type="fill"
      paint={{
        'fill-color': colors.grey,
        'fill-opacity': ['case',
          ['==', ['feature-state', 'hovered'], true], 0.3,
					0.1
				]
      }}
      click={true}
      bind:selected
      hover={true}
      {hovered}
      order="place_other"/>
    <MapLayer
      id="siblings-line"
      source="siblings"
      type="line"
      paint={{
        'line-color': colors.grey,
        'line-width': 2
      }}
      order="place_other"/>
  </MapSource>
  {/if}
  {#if geometry.self}
  <MapSource
    id="self"
    type="geojson"
    data={geometry.self}
    promoteId="code"
    maxzoom={12}>
    <MapLayer
      id="self-line"
      source="self"
      type="line"
      paint={{
        'line-color': colors.leafGreen,
        'line-width': 2
      }}
      order="place_other"/>
  </MapSource>
  {/if}
</Map>
-->
</div>
</section>

{#if geometry.self && geometry.children}
<AreasWithinSelector self={geometry.self.features[0]} children={geometry.children.features} />
{/if}
