<script>
    import robojournalist from 'robojournalist';
    import DataTable from './DataTable.svelte';
    import Chart from './Chart.svelte';
    import Tabs from './Tabs.svelte';
    import dataSectionConfig from './data-section-config.json';

	import { data, regiondata } from './data.js';

    let colours = ['#27A0CC', '#F66068'];
    let lineChartColours = ['#206095', '#27A0CC', '#871A5B', '#A8BD3A', '#F66068'];
    let chartTableOptions = ['Chart', 'Table'];

    let chartOrTable = 'Chart';

    export let section;
    export let place;
    let sectionConfig;
    let rows;
    let chartData;
    let regionName;

    $: {
        regionName = regiondata[place.code].RGN18NM;
        sectionConfig = dataSectionConfig[section];
        rows = [...sectionConfig.rows];
        let regionSumAll = {"2001": 0, "2011": 0};
        rows.forEach(row => {
            let regionCode = regiondata[place.code].RGN18CD;
            row.val = place.data[row.var[0]].val.c2011[row.var[1]];
            row.regionSum = {"2001": 0, "2011": 0};
            Object.values(data).forEach(d => {
                if (regiondata[d.code].RGN18CD === regionCode) {
                    for (let year of ["2001", "2011"]) {
                        row.regionSum[year] += d.data[row.var[0]].val["c"+year][row.var[1]];
                        if (!row.excludeFromTotal) {
                            regionSumAll[year] += d.data[row.var[0]].val["c"+year][row.var[1]];
                        }
                    }
                }
            });
        });
        rows.forEach(row => {
            row.regionPct = {"2001": row.regionSum["2001"] / regionSumAll["2001"] * 100, "2011": row.regionSum["2011"] / regionSumAll["2011"] * 100};
        });
        chartData = [];
        for (let row of rows) {
            if (!row.inChart) continue;
            chartData.push({variable: row.name, year: 2001, place: place.name, value: place.data[row.var[0]].perc.c2001[row.var[1]]});
            chartData.push({variable: row.name, year: 2011, place: place.name, value: place.data[row.var[0]].perc.c2011[row.var[1]]});
            chartData.push({variable: row.name, year: 2001, place: regionName, value: row.regionPct["2001"]});
            chartData.push({variable: row.name, year: 2011, place: regionName, value: row.regionPct["2011"]});
        }
        let roboData = {name: place.name};
        rows.filter(row => row.hasOwnProperty("roboName"))
            .forEach(row => {
                roboData[row.roboName + 2001] = place.data[row.var[0]].val.c2001[row.var[1]].toLocaleString();
                roboData[row.roboName + 2011] = place.data[row.var[0]].val.c2011[row.var[1]].toLocaleString();
            });
        chartData = {
            data: chartData,
            groups: rows.filter(d => d.inChart).map(d => d.name),
            items: [place.name, regionName],
            years: [2001, 2011],
            colours: colours,
            lineChartColours: lineChartColours,
            roboData
        };
        console.log(chartData);
    }
</script>

<!-- FIXME why isn't h3 styling working? -->
<h3 class="padding-top--4 padding-bottom--2" style="font-weight: bold; font-size: 150%">{sectionConfig.title}</h3>
<div class="mb-8">
    <p>
        {robojournalist(sectionConfig.roboString, chartData.roboData)}
    </p>

    <Tabs bind:selected={chartOrTable} options="{chartTableOptions}">
        <DataTable title={sectionConfig.title} place={place} rows={rows} hidden={chartOrTable!=='Table'}></DataTable>
        <Chart chartData={chartData} hidden={chartOrTable!=='Chart'}></Chart>
    </Tabs>

    <a href="#" class="text-base">Download this dataset</a>
</div>
