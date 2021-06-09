<template>
    <div>
        <v-card class="main-card" :loading="loading">
            <v-select v-model="countryModel" @input="updatePlot" item-text="country" item-value="value" return-object
                      :items=countryItems>
                Change
            </v-select>
            <highcharts :constructorType="'mapChart'" class="hc" :options="chartOptions" ref="chart"></highcharts>
        </v-card>
    </div>
</template>

<script>
    import worldMap from '@highcharts/map-collection/custom/world.geo.json'
    import axios from 'axios'

    export default {
        data() {
            return {
                countrySelectOpen: [],
                countrySelectSemi: [],
                countrySelectClosed: [],
                countryModel: {country: 'France', value: 'FR'},
                countryItems: [{country: 'France', value: 'FR'}, {country: 'Germany', value: 'DE'}, {
                    country: 'China',
                    value: 'CN'
                }],
                loading: false,
            };
        },
        computed: {
            chartOptions() {
                return {
                    chart: {
                        map: worldMap
                    },
                    title: {
                        text: 'Highmaps basic demo'
                    },
                    subtitle: {
                        text: 'Source map: <a href="http://code.highcharts.com/mapdata/custom/world.js">World, Miller projection, medium resolution</a>'
                    },
                    legend: {
                        enabled: true,
                    },
                    mapNavigation: {
                        enabled: true,
                        buttonOptions: {
                            alignTo: 'spacingBox'
                        }
                    },
                    colorAxis: {
                        min: 0
                    },
                    plotOptions: {
                        map: {
                            allAreas: false,
                            joinBy: ['iso-a2', 'code'],
                            dataLabels: {
                                enabled: true,
                                format: '{point.name}'
                            },
                            tooltip: {
                                headerFormat: '',
                                pointFormat: '{point.name}: <b>{series.name}</b>'
                            }
                        }
                    },
                    series: [
                        {
                            allAreas: true,
                            showInLegend: false,
                        },
                        {
                            name: 'Open',
                            color: '#06d6a0',
                            data: this.countrySelectOpen.map(function (code) {
                                return {code: code};
                            })
                        }, {
                            name: 'Semi-open',
                            color: '#ffd166',
                            data: this.countrySelectSemi.map(function (code) {
                                return {code: code};
                            })
                        }, {
                            name: 'Closed',
                            color: '#ef476f',
                            data: this.countrySelectClosed.map(function (code) {
                                return {code: code};
                            })
                        }, {
                            name: 'Current',
                            color: '#8ecae6',
                            data: [{
                                code: String(this.countryModel.value)
                            }]
                        }]
                }
            }
        },
        methods: {
            async updatePlot() {
                this.loading = true
                await 20
                this.countrySelectOpen = []
                this.countrySelectSemi = []
                this.countrySelectClosed = []
                await this.getCountryCodes(this.countryModel.value, 'OP')
                await this.getCountryCodes(this.countryModel.value, 'SEMI')
                await this.getCountryCodes(this.countryModel.value, 'CLOSED')
                console.log(this.loading)
                this.loading = false
            },
            getCountryCodes(originCountry, status) {
                return axios.get(`http://localhost:8080/api/originlist/?origin_country__iso_code=${originCountry}&b_status=${status}`)
                    .then(response => {
                        console.log(response.data[0])
                        if (response.data[0] != undefined)
                            if (status === 'OP')
                                this.countrySelectOpen = response.data[0]
                            else if (status === 'SEMI')
                                this.countrySelectSemi = response.data[0]
                            else if (status === 'CLOSED')
                                this.countrySelectClosed = response.data[0]
                            else if (status === 'OP')
                                this.countrySelectOpen = []
                            else if (status === 'SEMI')
                                this.countrySelectSemi = []
                            else if (status === 'CLOSED')
                                this.countrySelectClosed = []
                    })
                    .catch(e => {
                        this.errors.push(e)
                    })
            }
        },
        created() {
            this.getCountryCodes(this.countryModel.value, 'OP')
            this.getCountryCodes(this.countryModel.value, 'SEMI')
            this.getCountryCodes(this.countryModel.value, 'CLOSED')
        }
    };
</script>

<style scoped>
    .hc {
        min-height: 800px;
    }
    .main-card {
        margin-top: 10px;
    }
</style>