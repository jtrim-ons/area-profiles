<script>
    import BarChart from './BarChart.svelte';
    import Multiline from './svelte-components/Multiline.svelte';

    export let chartData;
    export let hidden = false;
</script>

<div class="wrapper padding-top--5 padding-bottom--5" class:hidden={hidden}>
    <div class="col-wrap">
        <div class="chart-div col col--md-half col--lg-half padding-left--1 padding-right--1">
            <BarChart {chartData}></BarChart>
        </div>
        <div class="chart-div col col--md-half col--lg-half padding-left--1 padding-right--1">
            <Multiline {chartData}></Multiline>
        </div>
    </div>
</div>

<style>
    .chart-div {
        height: 300px;
    }
</style>
