<script lang="ts">
    import * as echarts from 'echarts';
    import { 
        onMount, 
        onDestroy, 
    } from 'svelte';

    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    export let tuple2DData: Array<any>= [];

    let chartContainer: HTMLDivElement;
    let chartInstance: echarts.ECharts;

     // 响应式更新 option
    // $: if (chartInstance && tuple2DData) {
    //     const option = getOptionFromTuple2DData(tuple2DData);
    //     setOption(option);
    // }

    const resize = () => {
        chartInstance?.resize();
    };

    const defaultOption = {
        tooltip: {
            trigger: 'axis',
            axisPointer: {
                type: 'shadow'
            }
        },
        toolbox: {
            feature: {
                saveAsImage: {}
            }
        },
        xAxis: {
            type: 'category',
            data: [],
        },
        yAxis: {
            type: 'value',
        },
        series: [
            {
                name: 'Value',
                type: 'bar',
                data: [],
                label: {
                    show: true,
                    position: 'top',
                    fontSize: 12,
                },
                itemStyle: {
                    color: '#5470C6',
                    borderRadius: [5, 5, 0, 0]
                },
                emphasis: {
                    itemStyle: {
                        color: '#91cc75'
                    }
                },
                animationDuration: 800,
                animationEasing: 'bounceOut'
            },
        ]
    };

    const getOptionFromTuple2DData = (tuple2DData:Array<any>) => {
        const option = JSON.parse(JSON.stringify(defaultOption));

        const xData = tuple2DData.map(item => item[0]);
        const yData = tuple2DData.map(item => item[1]);

        option.xAxis.data = xData;
        option.series[0].data = yData;

        return option;
    };


    const setOption = (
        newOption: echarts.EChartsCoreOption,
        notMerge = false,
        lazyUpdate = false
    ) => {
        try {
            chartInstance?.setOption(newOption, notMerge, lazyUpdate);
        } catch (e) {
            dispatch('chartError',{ message: '图表渲染失败', detail: e })
        } 
    };

    onMount(() => {
        if (chartContainer) {
            window.addEventListener('resize', resize);
            chartInstance = echarts.init(chartContainer);
            if(tuple2DData && tuple2DData.length > 0){
                setOption(getOptionFromTuple2DData(tuple2DData));
            }
        }else{
            dispatch('chartError')
        }
    });
    
    onDestroy(() => {
        window.removeEventListener('resize', resize);
        chartInstance?.dispose();
    });
</script>
    <div class="chart-scroll">
        <div bind:this={chartContainer} class="chart-container"></div>
    </div>
<style>
    .chart-scroll {
        width: 100%;
        overflow-x: auto;
        -webkit-overflow-scrolling: touch;
        background-color: rgba(236, 236, 236, 0.3);
    }
    .chart-container {
      width: 100%;
      min-width: 300px;
      /* height: 500px; */
      aspect-ratio: 2;
    }
    @media (max-width: 900px) {
        .chart-container {
            height: 260px;
            aspect-ratio: auto;
            min-width: 300px;
        }
    }

</style>