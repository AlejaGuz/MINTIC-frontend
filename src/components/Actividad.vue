<template>
    <div v-if="loaded" class="information">
        <ul>
           <!--<li v-for="act in actividades">
               {{ act.nomAct }}
               {{ act.capacidad }}
               {{ act.horario }}
               </li> -->
        </ul>
    </div>
</template>
<script>
import jwt_decode from "jwt-decode";
import axios from 'axios';

export default {
    data: {
        actividades:[],
        loaded: true,
    },

    methods: {
        getActividades : function(){
            alert("entró a getActividdes");

            axios.get("http://127.0.0.1:8000/fusionreadall/",{headers: {}})

            .then((result) => {
                alert("entró a result axios fusion read all");
                let array = result.data
                for(const a of array){
                    this.getInfoActividad(a.actividad,a.horario);
                }
                
            })
            .catch(() => {
                alert("Error en getActividades");
            });
        },
        getInfoActividad(act,hor){
            alert("entró a get info, actividad: "+ act + " horario: "+ hor);

            let token = localStorage.getItem("token_access");
            let userId = jwt_decode(token).user_id.toString();

            let infoAct = {
                nomAct : "",
                capacidad : 0,
                horario : ""
            }


            axios.get(`http://127.0.0.1:8000/actividadread/${act}/`, 
            {headers: {'Authorization': `Bearer ${token}`}},{data:{'id_user':userId}})

            .then((result)=>{
                alert("entró axios activdad read");
                this.infoAct.nomAct = result.data.nombre;
                this.infoAct.capacidad = result.data.capacidad;
            })
            .catch(() => {
                alert("Error en getInfo Act");
            });

            axios.get(`http://127.0.0.1:8000/horariodetail/${hor}/`, 
            {headers: {'Authorization': `Bearer ${token}`}},{data:{'id_user':userId}})

            .then((result)=>{
                alert("entró axios horario read");
                this.infoAct.horario = result.data.dia +"-"+result.data.hora;
            })
            .catch(() => {
                alert("Error en getInfo Hor");
            });

            alert("insertar info Act");
            this.actividades.push(infoAct);
        }
    },
    created: function(){
        this.getActividades();
    },
}
</script>
