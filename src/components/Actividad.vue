<template>
    <div v-if="loaded" class="information">
        <ul>
           <li v-for="act in actividades">
               {{ act.nomAct }}
               {{ act.capacidad }}
               {{ act.horario }}
               </li> 
               <li>Prueba</li>
        </ul>
    </div>
</template>
<script>
import jwt_decode from "jwt-decode";
import axios from 'axios';

export default {
    
    data: function(){
        
        return {
            actividades:[],
            loaded: true,
        }
        
    },

    methods: {
        getActividades : function(){
            alert("entró a getActividdes");

            axios.get("https://sportclub-be.herokuapp.com/fusionreadall/",{headers:{}})

            .then((result) => {
                alert("entró a result axios fusion read all");
                let array = result.data
                for(let a of array){
                    this.getInfoActividad(a.actividad,a.horario);
                }
                
            })
            .catch(() => {
                alert("Error en getActividades");
            });
        },
        getInfoActividad: async function (act,hor) {
            
            //alert("entró a get info, actividad: "+ act + " horario: "+ hor);

            let token = localStorage.getItem("token_access");
            let userId = jwt_decode(token).user_id.toString();

            let infoAct = {
                nomAct : "",
                capacidad : 0,
                horario : ""
            }

            alert("id_actividad: "+ act + " userId: "+ userId);

            axios.get("https://sportclub-be.herokuapp.com/actividadread/"+act+"/", 
            {headers: {'Authorization': `Bearer ${token}`},data:{'id_user':userId}})

            .then((result)=>{
                alert("entró axios activdad read");
                this.infoAct.nomAct = result.data.nombre;
                this.infoAct.capacidad = result.data.capacidad;
            })
            .catch(() => {
                alert("Error en getInfo Act");
            });

            axios.get(`https://sportclub-be.herokuapp.com/horariodetail/${hor}/`, 
            {headers: {'Authorization': `Bearer ${token}`}, data:{'id_user':userId}})

            .then((result)=>{
                alert("entró axios horario read");
                this.infoAct.horario = result.data.dia +"-"+result.data.hora;
            })
            .catch(() => {
                alert("Error en getInfo Hor");
            });

            alert("insertar info Act");
            this.actividades.push(infoAct);
            alert("tamaño arreglo: "+ this.actividades.length);
        }
    },
    created: function(){
        alert("entró a created");
        this.getActividades();
    },
}
</script>
