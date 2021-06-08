<script>
    import BarChart from './BarChart.svelte';
    import Multiline from './svelte-components/Multiline.svelte';

    export let chartData;
    export let hidden;
</script>

<div class="wrapper padding-top--5 padding-bottom--5" class:hidden={hidden}>
    <div class="col-wrap" style="height: 300px">
        <div class="col col--md-half col--lg-half padding-left--1 padding-right--1" style="height: 300px">
            <BarChart {chartData}></BarChart>
        </div>
        <div class="col col--md-half col--lg-half padding-left--1 padding-right--1" style="height: 300px">
            <Multiline {chartData}></Multiline>
        </div>
    </div>
</div>
