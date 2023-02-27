<template>
    <div>
        <h1>COVID-19 Dashboard</h1>
        <div v-if="loading">Loading...</div>
        <div v-else>
            <div>
                <label for="date">Select date:</label>
                <input type="date" id="date" v-model="showDate" @change="updateData" />
            </div>
            <ChartCountry :data="data" :selectedDate="selectedDate" />
            <div class="table-container">
                <b-table striped hover class="styled-table" :fields="fields">
                    <thead>
                        <tr>
                            <th>Country</th>
                            <th>Cases</th>
                            <th>Deaths</th>
                            <th>Recovered</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="item in data" :key="item.country">
                            <td>{{ item.country }}</td>
                            <td>{{ item.timeline.cases[selectedDate] }}</td>
                            <td>{{ item.timeline.deaths[selectedDate] }}</td>
                            <td>{{ item.timeline.recovered[selectedDate] }}</td>
                        </tr>
                    </tbody>
                </b-table>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment';
import ChartCountry from './ChartCountry';
export default {
    name: 'DataDisplay',
    components: {
        ChartCountry
    },
    data() {
        return {
            data: [],
            loading: true,
            selectedDate: '',
            showDate: '',
            fields: [
                {
                    key: 'Country',
                    sortable: true
                },
                {
                    key: 'Cases',
                    sortable: false
                },
                {
                    key: 'Deaths',
                    sortable: true,
                    // Variant applies to the whole column, including the header and footer
                    variant: 'danger'
                },
                {
                    key: 'Recovered',
                    sortable: true,
                    // Variant applies to the whole column, including the header and footer
                    variant: 'primary'
                }
            ],
        }
    },
    mounted() {
        this.getData();
        this.showDate = moment(this.selectedDate, 'M/D/YY').format('YYYY-MM-DD');
    },
    methods: {
        getData() {
            axios.get('https://disease.sh/v3/covid-19/historical?lastdays=31')
                .then(response => {
                    this.data = response.data;
                    this.selectedDate = Object.keys(this.data[0].timeline.cases)[Object.keys(this.data[0].timeline.cases).length - 1];
                    this.loading = false;
                })
                .catch(error => {
                    console.log(error);
                });
        },
        updateData() {
            this.selectedDate = moment(this.showDate).format('M/D/YY');
        }
    }
}
</script>
<style>
.table-container {
    justify-content: center !important;
    align-items: center !important;
    height: 100vh;
    overflow-x: auto;
}


.styled-table th {
    background-color: #333;
    color: #fff;
    font-weight: bold;
}

.styled-table th,
.styled-table td {
    padding: 12px 15px;
}

.styled-table tbody tr:nth-of-type(even) {
    background-color: #f2f2f2;
}

.styled-table tbody tr:hover {
    background-color: #ddd;
}

@media screen and (max-width: 767px) {

    /* adjust table styles for smaller screens */
    .table-responsive {
        overflow-x: auto;
    }

    .styled-table {
        font-size: 14px;
    }

    .styled-table th,
    .styled-table td {
        padding: 8px;
    }
}
</style>