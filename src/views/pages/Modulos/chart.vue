<script>

import DataTable from 'primevue/datatable';
import { useToast } from 'primevue/usetoast';
import { ref, onMounted, watch } from 'vue';
import axios from 'axios';

import Chart from 'primevue/chart';

export default {
    data() {

		const toast = useToast();
        const datos = ref([]);

        const chartData = ref();
        const chartOptions = ref();

        watch(datos, () => {
            chartData.value = setChartData();
        });

        async function llenarTabla() {
                const response = await axios.get(`https://eodhistoricaldata.com/api/eod/MCD.US?from=2017-01-05&to=2017-02-05&period=d&fmt=json&api_token=OeAFFmMliFG5orCUuwAKQ8l4WWFQ67YX`);
                datos.value = response.data;
                console.log("Response: ", datos.value);
                toast.add({ severity: 'success', summary: 'Exito', detail: 'Los datos de la API cargaron correctamente.', life: 3000 });
        }

        const setChartData = () => {
            const documentStyle = getComputedStyle(document.documentElement);

            return {
                labels: datos.value.map(item => item.date),
                datasets: [
                    {
                        label: 'Cambio porcentual trimestral',
                        data: datos.value.map(item => item.open),
                        fill: true,
                        backgroundColor: '#2F486080',
                        borderColor: '#2F4860',
                        tension: 0.4
                    },
                ]
            };
        };


        const setChartOptions = () => {
            const documentStyle = getComputedStyle(document.documentElement);
            const textColor = documentStyle.getPropertyValue('--text-color');
            const textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
            const surfaceBorder = documentStyle.getPropertyValue('--surface-border');

            return {
                maintainAspectRatio: false,
                aspectRatio: 0.6,
                plugins: {
                    legend: {
                        labels: {
                            color: textColor
                        }
                    }
                },
                scales: {
                    x: {
                        ticks: {
                            color: textColorSecondary
                        },
                        grid: {
                            color: surfaceBorder
                        }
                    },
                    y: {
                        ticks: {
                            color: textColorSecondary
                        },
                        grid: {
                            color: surfaceBorder
                        }
                    }
                }
            };
        }

        onMounted(() => {
            llenarTabla();
        });

        return { 
            llenarTabla,
            datos, 
            chartData,
            chartOptions
		}
}
}
</script>

<template>
    <div class="grid">
        <Toast/>
        <div class="col-12">
            <div class="card a text-center">
                <div class="card col-10 carta">
                    <h3>API de datos hitóricos del mercado de valores de Lyon, Francia.</h3>
                    <Chart type="line" :data="chartData" :options="chartOptions" class="h-30rem" />
                </div>
                <h4>Cambio porcentual por dia de acciones AAPL (2017-01-05/2017-02-03)</h4>
                <DataTable class="mt-4" :value="datos" paginator :rows="5" :rowsPerPageOptions="[5, 10, 20, 50]" tableStyle="min-width: 50rem"
                        paginatorTemplate="RowsPerPageDropdown FirstPageLink PrevPageLink CurrentPageReport NextPageLink LastPageLink"
                        currentPageReportTemplate="{first} al {last} de {totalRecords}">
                    <template #paginatorstart>
                        <Button type="button" icon="pi pi-refresh" text @click="llenarTabla()"/>
                    </template>
                    <Column field="fecha" header="Fecha" style="width: 30%">
                        <template #body="slotProps">
                            {{ slotProps.data.date }}
                        </template>
                    </Column>
                    <Column field="apertura" header="Valor Apertura" style="width: 35%">
                        <template #body="slotProps">
                            {{ slotProps.data.open }}
                        </template>
                    </Column>
                    <Column field="cierre" header="Valor Cierre" style="width: 35%">
                        <template #body="slotProps">
                            {{ slotProps.data.close }}
                        </template>
                    </Column>
                </DataTable>
            </div>
        </div>
    </div>
</template>

<style scoped>
.carta {
height: 700px;
margin-left: 10%;
}
</style>