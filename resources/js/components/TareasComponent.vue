<template>
    <div>

        <form @submit.prevent="editarTarea(tarea)" v-if="editarActivo">
             <h3>Editar Tarea</h3>
            <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="tarea.nombre">
            <input type="text" placeholder="Descripcion" class="form-control mb-2" v-model="tarea.descripcion">
            <button class="btn btn-success" type="submit">Editar</button>
             <button class="btn btn-danger" @click="cancelarEdicion()" type="submit">Cancelar</button>
        </form>

        <form @submit.prevent="agregar" v-else>
             <h3>Agregar Tareas</h3>
            <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="tarea.nombre">
            <input type="text" placeholder="Descripcion" class="form-control mb-2" v-model="tarea.descripcion">
            <button class="btn btn-primary" type="submit">Agregar</button>


        </form>


        <hr class="mt-3">
        <h3>Listado de Tareas</h3>
        <b-col>
            <b-form-input></b-form-input>
        </b-col>
     <!--    <ul class="list-group my-2">
             <li class="list-group-item" v-for="(item, index) in tareas" :key="index">
                 <span class="badge badge-primary float-right">
                     {{item.updated_at}}
                 </span>
                 <p>{{item.nombre}}</p>
                 <p>{{item.descripcion}}</p>
                 <button @click="eliminarTarea(item, index)" class="btn btn-danger btn-sm">
                     Eliminiar

                 </button>
                 <button class="btn btn-warning btn-sm" @click="editarFormulario(item)">Editar</button>
            </li>
        </ul>
 -->


        <div class="alert alert-primary" role="alert"  v-for="(item, index) in tareas" :key="index">
            <div class="d-flex justify-content-between align-items-center">



                 <div> {{index + 1}} -
                     <strong class="mr-2">{{item.nombre}} </strong>
                      {{item.descripcion}}
                </div>





                 <div>
                      <span class="badge badge-primary ">
                            {{item.updated_at}}
                      </span>
                     <button class="btn btn-warning btn-sm" @click="editarFormulario(item)">
                         Editar
                     </button>
                     <button @click="eliminarTarea(item, index)" class="btn btn-danger btn-sm">
                         X
                     </button>

                 </div>




            </div>


        </div>

    </div>
</template>
<script>

export default {

    data(){
        return{
            tareas:[],
            tarea:{nombre:'', descripcion:''},
            editarActivo:false,
        }
    },
    created(){
        axios.get('/tareas')
            .then(res => {
                this.tareas = res.data;
                console.log(this.tareas)
            })
    },
    methods:{
        agregar(){
            if(this.tarea.nombre.trim() === '' || this.tarea.descripcion.trim() === ''){
            alert('Debes completar todos los campos antes de guardar');
            return;
            }
            const params ={
            nombre:this.tarea.nombre,
            descripcion:this.tarea.descripcion
            }
            this.tarea.nombre = '';
            this.tarea.descripcion = '';

            axios.post('/tareas', params)
                .then(res => {
                    this.tareas.push(res.data)
                })


        },
        eliminarTarea(item, index){
            axios.delete(`/tareas/${item.id}`)
            .then(()=>{
                this.tareas.splice(index, 1)
            })
        },
        editarFormulario(item){
            this.editarActivo = true;
            this.tarea.nombre = item.nombre;
            this.tarea.descripcion = item.descripcion;
            this.tarea.id = item.id;
        },
        editarTarea(item){
            const params = {nombre: item.nombre, descripcion: item.descripcion};

            axios.put(`/tareas/${item.id}`, params)
                .then(res=>{
                    this.editarActivo = false;
                    const index = this.tareas.findIndex(
                      tareaBuscar => tareaBuscar.id === res.data.id)
                    this.tareas[index] = res.data;

                    this.tarea = {nombre:'', descripcion:''},

                      axios.get('/tareas')
                     .then(res => {
                      this.tareas = res.data;
            })

                })
        },

        cancelarEdicion(){
            this.editarActivo = false;
            this.tarea = {nombre:'', descripcion:''}
        },


    }

}
</script>
