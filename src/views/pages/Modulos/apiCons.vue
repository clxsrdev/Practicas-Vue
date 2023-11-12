<script>
import { ref, onMounted } from 'vue';
import Button from 'primevue/button';
import axios from 'axios';
import DataTable from 'primevue/datatable';
import Column from 'primevue/column';

export default {
    data() {
        return {
            empleados: [] ,
            idEmp:'',
            nombre:'',
            salario:'',
            edad:''
        };
    },
    mounted() {
        this.consultarAPIEmpleados();
    },
    methods: {
        async consultarAPI() {
            const apiUrl = `https://dummy.restapiexample.com/api/v1/employee/`+this.idEmp;
            // const apiUrl = `https://jsonplaceholder.typicode.com/users/1`;
            // const apiUrl = `https://dummyjson.com/users/${this.idEmp}`;
            try{
                const response = await axios.get(apiUrl);
                    // console.log(response); visualizando respuesta completa
                this.nombre = response.data.data.employee_name;
                this.salario = response.data.data.employee_salary;
                this.edad = response.data.data.employee_age;
                }catch(error) {
                    console.error('Hubo un error al consultar la API:', error);
                };
        },
        async consultarAPIEmpleados() {
            try{
                const response = await axios.get('https://dummy.restapiexample.com/api/v1/employees');
                const empleados = response.data.data;

                this.empleados = empleados.map(empleado => ({
                nomemp: empleado.employee_name,
                salario: empleado.employee_salary,
                edad: empleado.employee_age
                }));
            }catch(error) {
                console.error('Hubo un error al consultar la API:', error);
            };
        }

    }
}

</script>

<template>
    <div class="grid p-fluid">
        <div class="col-12 xl:col-4">
            <div class="card">
                <h5>Consumir API REST Empleado</h5>
                    <div class="col-12 md:col-12">
                        <div class="field">
                            <label for="inputId">Id empleado:</label>
                            <InputText id="inputId" type="number" v-model="idEmp" />
                        </div>
                       
                        <div class="field">
                            <label for="nombre">Nombre</label>
                            <InputText id="nombre" v-model="nombre" field="name" readonly/>
                        </div>
                       
                        <div class="field">
                            <label for="salario">Salario</label>
                            <InputText id="salario"  type="number" v-model="salario" readonly />
                        </div>
                        <div class="field">
                            <label for="edad">Edad</label>
                            <InputText id="edad" type="number" v-model="edad" readonly/>
                        </div>
                        <Button label="Consultar API" icon="pi pi-search"  @click="consultarAPI"/>
                    </div>

                    
            </div>
        </div>
        <div class="col-12 xl:col-8">
            <div class="card">
                <h5>Lista de Empleados</h5>
                <div class="field">
                    <DataTable :value="empleados" showGridlines tableStyle="min-width: 50rem">
                        <Column field="nomemp" header="Nombre del empleado"></Column>
                        <Column field="salario" header="Salario"></Column>
                        <Column field="edad" header="Edad"></Column>
                    </DataTable>
                </div>

            </div>
        </div>
    </div>
</template>