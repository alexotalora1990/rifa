<template>
  <div class="mainContainer" :class="currentTheme">
    <header>
      <h1>TALONARIO</h1>
    </header>
    <h3 id="alerta" v-if="error != ''">{{ error }}</h3>
    <div>
      <!-- div datos iniciales __________________-->
      <div id="datosIniciales" v-show="mostrarInformacionInicial" class="generalClass">
        <button class="buttonCerrarComprador" @click="cerrarInformacionIncial()">❌</button>
        <h2 class="mainText">CONFIGURA TU TALONARIO</h2>
        <input type="text" v-model="premio" placeholder="premio"> <br>
        <input type="text" v-model="valorBoleta" placeholder="valor Boleta"><br>
        <select class="loteria" id="loteria" v-model="loteria">
          <option value="" disabled selected>Seleccione la loteria </option>
          <option value="cundinamarca">Cundinamarca</option>
          <option value="cruzRoja">Cruz Roja </option>
          <option value="manizales">Manizales </option>
          <option value="bogota">Bogota</option>
          <option value="santander">Santander </option>
          <option value="boyaca">Boyaca </option>
          <option value="culona">Culona</option>
        </select> <br>
        <select class="cantidadBoletas" v-model="cantidadBoletas">
          <option value="" disabled selected>Cantidad de Boletas</option>
          <option value="100">0-100</option>
          <option value="1000">0-1000 </option>
          <option value="10000">0-10000</option>
        </select> <br>
        <input type="date" v-model="fechaSorteo" placeholder="Fecha sorteo"> <br>

        <br>
        <button class="buttonGeneral" @click="validarInformacionInicial()">Guardar </button>
      </div>

      <!-- numero de loteria -->
      <div v-if="mostrarNumeroLoteriaBool" id="numeroLoteria" class="generalClass">
        <button type="button" class="buttonCerrarComprador" @click="cerrarLoteria()">❌</button>
        <p class="mainText">
          Loteria: {{ loteria }}</p>
        <input type="number" v-model="numeroLoteria" placeholder="Número de 4 cifras"> <br>
        <button @click="guardarYBuscar()" class="buttonGeneral">Guardar</button>
      </div>

      <!-- Editar comprador -->
      <div id="datosBoleta" v-show="mostrarDatosBoleta" class="generalClass">
        <button class="buttonCerrarComprador" @click="cerrarDatosComprador()">❌</button>
        <div class="datosBoletaP">
          <p>Numero: {{ compradorSeleccionado.id }} </p>
          <p>Nombre: {{ compradorSeleccionado.nombre }} </p>
          <p>Direccion: {{ compradorSeleccionado.direccion }} </p>
          <p>Telefono: {{ compradorSeleccionado.telefono }} </p>
          <p>Estado: {{ compradorSeleccionado.estado }} </p>
        </div>
        <button class="buttonGeneral" v-if="compradorSeleccionado.estado !== 'disponible'"
          @click="liberarBoleta()">Liberar</button>
        <button class="buttonGeneral" @click="EditarComprador()">Editar</button>
      </div>

      <!-- formulario comprador -->
      <div id="comprador" class="generalClass">
        <div class="datosComprador" v-show="mostrarInformacionComprador">


          <h2 class="mainText"> Datos Comprador </h2>
          <button class="buttonCerrarComprador" @click="cerrarFormularioComprador()">❌</button>
          <p class="mainText">Boleta: {{ compradorSeleccionado.id }}</p>
          <div class="datos">
            <label for="nombre">Nombre:</label><br>
            <input type="text" v-model="nombre" id="nombre">

            <label for="direccion">Dirección:</label><br>
            <input type="text" v-model="direccion" id="direccion">

            <label for="telefono">Teléfono:</label><br>
            <input type="text" v-model="telefono" id="telefono"><br>

            <label for="estado">Estado:</label> <br>
            <input type="radio" id="pagada" name="estado" v-model="estado" value="pagada">
            <label for="pagada">Pagada</label><br>
            <input type="radio" id="porPagar" name="estado" v-model="estado" value="porPagar">
            <label for="porPagar">Por Pagar</label><br>
            <input type="radio" id="disponible" name="estado" v-model="estado" checked value="disponible">
            <label for="disponible">Disponible</label><br>

            <button class="buttonGeneral" @click="validarComprador()">Guardar</button>
          </div>
        </div>
      </div>
    </div>
    <!-- temas  -->
    <div v-show="seleccionarTema" id="seleccionarTema" class="generalClass">
      <button class="buttonCerrarComprador" @click="cerrarSelecionarTema()">❌</button>
      <h2 class="mainText">Seleccionar Tema</h2>
      <button @click="changeTheme('defaultTheme')" class="buttonOriginal">Original</button>
      <button @click="changeTheme('themeOne')" class="buttonVioleta">Violeta</button>
      <button @click="changeTheme('themeTwo')" class="buttonVerde">verde</button>
      <button @click="changeTheme('themeThree')" class="buttonNaranja">Naranja</button>
    </div>


    <div class=" principal">



      <!-- boletas -->
      <div id="principalBoleteria" >
        <h2 class="mainText"> Boleteria</h2>
        <div class="boleteria">
          <div v-for="(comprador, index) in arrayRifa" :key="index" class="boleta" v-show="mostrarBoletas" :class="{
                                  'estado-pagada': comprador.estado === 'pagada', 'estado-porPagar': comprador.estado === 'porPagar',
                                   'estado-disponible': comprador.estado === 'disponible', 'estado-ganador': comprador.estado === 'ganador'
                                   }" @click="mostrarDatosComprador(comprador, index)">
            <p>{{ comprador.id }}</p>

          </div>
        </div>
        <!-- tabla listar boletas -->

    <div v-if="mostrarListadoBoletas" class="listaBoletas">
      <button type="button" class="buttonCerrarComprador" @click="cerrarTabla()">❌</button>
      <h2>Listado de Boletas</h2>
      <table id="element-to-pdf" style="width: 10.5in; text-align: center;" class="mainTable">
        <thead>
          <tr class="tableHeader">
            <th >Número </th>
            <th >Comprador</th>
            <th>Dirección</th>
            <th>Teléfono</th>
            <th>Pagada</th>
            <th>Por Pagar</th>
            <th>Ganador</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(comprador, index) in boletasPagadasYPorPagar" :key="index">
            <td>{{ comprador.id }}</td>
            <td>{{ comprador.nombre }}</td>
            <td>{{ comprador.direccion }}</td>
            <td>{{ comprador.telefono }}</td>
            <td v-if="comprador.estado === 'pagada'">X</td>
            <td v-else></td>
            <td v-if="comprador.estado === 'porPagar'">X</td>
            <td v-else></td>
            <td v-if="comprador.estado === 'ganador'">X</td>
            <td v-else></td>
          </tr>
        </tbody>
        <tfoot>
          <tr>
            <td colspan="4">Total Boletas</td>
            <td>{{ contarBoletasPagadas }}</td>
            <td>{{ contarBoletasPorPagar }}</td>
            <td>{{ }}</td>

          </tr>
          <tr>
            <td colspan="4">Total Dinero</td>
            <td>{{ UnidadesMil(totalPagadas) }}</td>
            <td>{{ UnidadesMil(totalPorPagar) }}</td>
            <td>{{ }}</td>
          </tr>
        </tfoot>
      </table>
    </div>
      </div>


      <div class="principal2">
        <!-- acciones -->

        <div class="acciones">

          <h2 class="mainText">ACCIONES</h2>
          <div class="acciones2">

            <button class="buttonAcciones" @click="listarBoletas()">LISTAR
              BOLETAS</button> <br>
            <button class="buttonAcciones" @click="seleccionTema()">PERSONALIZAR TALONARIO</button> <br>
            <button class="buttonAcciones" @click="exportarTablaPDF()">GENERAR ARCHIVO DE DATOS</button><br>
            <button class="buttonAcciones" @click="mostrarNumeroLoteria()">NUMERO DE LOTERIA</button>


          </div>
        </div>
        <!-- informacion -->
        <div class="informacion">

          <h2 class="mainText">INFORMACIÓN</h2>
          <div class="informacion2">
            <P>🏆{{ UnidadesMil(premio1) }}</P>
            <P>💰{{ UnidadesMil(valorBoleta1) }}</P>
            <P>🎰{{ InformacionInicial.loteria }}</P>
            <P>🗓️{{ InformacionInicial.fechaSorteo }}</P>
            <button class="buttonGeneral" @click="editarInformacionInicial(item, i)">Editar✒️</button>
          </div>
        </div>
      </div>
    </div>

   <footer>
      <p>&copy; 2023. Todos los derechos reservados.</p>
    </footer>

  </div>
</template>




<script setup>
import html2pdf from 'html2pdf.js';

import { ref, computed } from "vue";

//variables formulario inicial
let premio = ref("")
let premio1 = ref("")
let valorBoleta = ref("")
let valorBoleta1 = ref("")
let loteria = ref("")
let cantidadBoletas = ref("100")
let fechaSorteo = ref("")
let index = null
let mostrarInformacionInicial = ref(true)
let InformacionInicial = ref({})
let bd = true
let mostrarBoletas = ref(false)

let error = ref("")
let arrayRifa = ref([])

//variables comprador
let nombre = ref("")
let direccion = ref("")
let telefono = ref("")
let estado = ref("")
let mostrarInformacionComprador = ref(false)
let numeroBoleta = ref("")
let mostrarDatosBoleta = ref(false)
let comprador = ref({})
let compradorSeleccionado = ref({})

let mostrarListadoBoletas = ref(false);

// numero loteria 
let numeroLoteria = ref("");
let mostrarNumeroLoteriaBool = ref(false);

let seleccionarTema = ref(false)
let currentTheme = ref("defaultTheme");







// Informacion Incial
function guardarInformacionInicial() {
  InformacionInicial = {
    premio: (premio.value),
    valorBoleta: valorBoleta.value,
    loteria: loteria.value,
    fechaSorteo: fechaSorteo.value
  }
  premio1.value = InformacionInicial.premio
  valorBoleta1.value = InformacionInicial.valorBoleta

  mostrarInformacionInicial.value = false;
  mostrarBoletas.value = true;

  console.log(InformacionInicial)
}

function editarInformacionInicial(item, i) {

  mostrarInformacionInicial.value = true;
  if (item) {

    premio.value = item.premio;
    valorBoleta.value = item.valorBoleta;
    loteria.value = item.loteria;
    fechaSorteo.value = item.fechaSorteo;

    index = i;
    editarInfoInicial = false;
  }
}


/* unidades de 1000 */

function UnidadesMil(numero) {
  return numero.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
}
function validarInformacionInicial() {
  if (bd) {
    const fechaActual = new Date();
    const fechaSeleccionada = new Date(fechaSorteo.value);
    if (premio.value === "") {
      error.value = "Digite el valor del premio"
      setTimeout(() => {
        error.value = ""
      }, 3000)
    }
    else if (valorBoleta.value == "") {
      error.value = "Digite el valor de la boleta"
      setTimeout(() => {
        error.value = ""
      }, 3000)
    }
    else if (isNaN(valorBoleta.value)) {
      error.value = "valor de boleta de ser numero"
      setTimeout(() => {
        error.value = ""
      }, 3000)
    }
    else if (valorBoleta.value <= 0) {
      error.value = "valor de boleta debe ser mayor a 0"
      setTimeout(() => {
        error.value = ""
      }, 3000)
    }
    else if (loteria.value == "") {
      error.value = "seleccione una loteria"
      setTimeout(() => {
        error.value = ""
      }, 3000)
    }
    else if (fechaSorteo.value == "") {
      error.value = "Debes seleccionar una fecha"
      setTimeout(() => {
        error.value = ""
      }, 3000)
    }
    else if (fechaActual.setHours(0, 0, 0, 0) > fechaSeleccionada) {
      error.value = 'fecha incorrecta'
      setTimeout(() => {
        error.value = ""
      }, 3000);
    }
    else {
      guardarInformacionInicial()
    }
  }
  else {
    arrayInformacionInical.value[index].premio = premio.value
    arrayInformacionInical.value[index].valorBoleta = valorBoleta.value
    arrayInformacionInical.value[index].loteria = loteria.value
    arrayInformacionInical.value[index].fechaSorteo = fechaSorteo.value

    bd = true;
    index = null;
  }

}

//Rifa
for (let i = 0; i < 100; i++) {
  comprador = {
    id: i < 10 ? `0${i}` : `${i}`,
    nombre: "",
    direccion: "",
    telefono: "",
    estado: "disponible",

  }
  arrayRifa.value.push(comprador);
}



//datos comprador

function mostrarFormularioComprador() {
  mostrarInformacionComprador.value = true

}

function validarComprador() {
  if (nombre.value == "") {

    error.value = "Digite el nombre del comprador"
    setTimeout(() => {

      error.value = ""
    }, 3000)
    console.log(error.value);
  }
  else if (direccion.value == "") {
    error.value = "Digite dirección del comprador"
    setTimeout(() => {

      error.value = ""
    }, 3000)
    console.log(error.value);

  }
  else if (telefono.value == "") {
    error.value = "Digite telefono del comprador"
    setTimeout(() => {

      error.value = ""
    }, 3000)
    console.log(error.value);
  }
  else if (isNaN(telefono.value)) {
    error.value = "telefono solo recibe numeros"
    setTimeout(() => {

      error.value = ""
    }, 3000)
    console.log(error.value);
  }
  else if (estado.value == "" || (estado.value == "disponible")) {
    error.value = "seleccione un estado diferente a disponible"
    setTimeout(() => {

      error.value = ""
    }, 3000)
    console.log(error.value);
  }
  else {
    guardarDatosComprador()

    nombre.value = ""
    direccion.value = ""
    telefono.value = ""
  }


}



function mostrarDatosComprador(comprador, index) {
  compradorSeleccionado.value = arrayRifa.value[index]
  mostrarDatosBoleta.value = true;
  console.log(`compradorSeleccionado ${compradorSeleccionado}`)
}
function EditarComprador() {

  // Obtener el índice del comprador seleccionado en el arrayRifa
  const index = arrayRifa.value.findIndex(comprador => comprador.id === compradorSeleccionado.value.id);

  // Establecer las variables reactivas con los valores del comprador seleccionado
  nombre.value = arrayRifa.value[index].nombre;
  direccion.value = arrayRifa.value[index].direccion;
  telefono.value = arrayRifa.value[index].telefono;
  estado.value = arrayRifa.value[index].estado;

  // Mostrar el formulario del comprador
  mostrarFormularioComprador();
  mostrarDatosBoleta.value = false
}

function guardarDatosComprador() {
  const index = arrayRifa.value.findIndex(comprador => comprador.id === compradorSeleccionado.value.id);

  // Actualizar los valores del comprador seleccionado en el arrayRifa
  arrayRifa.value[index].nombre = nombre.value;
  arrayRifa.value[index].direccion = direccion.value;
  arrayRifa.value[index].telefono = telefono.value;
  arrayRifa.value[index].estado = estado.value;

  // Cerrar el formulario del comprador
  cerrarFormularioComprador();
}
function liberarBoleta() {
  // Obtener el índice del comprador seleccionado en el arrayRifa
  const index = arrayRifa.value.findIndex(comprador => comprador.id === compradorSeleccionado.value.id);

  // Limpiar la información del comprador seleccionado y restaurar el estado a "disponible"
  arrayRifa.value[index] = {
    id: compradorSeleccionado.value.id,
    nombre: "",
    direccion: "",
    telefono: "",
    estado: "disponible"
  };

  // Cerrar el panel de detalles del comprador
  cerrarDatosComprador();
}
function cerrarFormularioComprador() {
  mostrarInformacionComprador.value = false;
}
function cerrarDatosComprador() {
  mostrarDatosBoleta.value = false;
}
function cerrarLoteria() {
  mostrarNumeroLoteriaBool.value = false
}
function cerrarInformacionIncial() {
  mostrarInformacionInicial.value = false
}

// listar boletas 

function listarBoletas() {
  mostrarListadoBoletas.value = true;
}
function cerrarTabla() {
  mostrarListadoBoletas.value = false;
}

// Función para contar boletas pagadas
const contarBoletasPagadas = computed(() => {
  return arrayRifa.value.filter(comprador => comprador.estado === 'pagada').length;
});

// Función para contar boletas por pagar
const contarBoletasPorPagar = computed(() => {
  return arrayRifa.value.filter(comprador => comprador.estado === 'porPagar').length;
});
// Función para contar boleta ganadora
const contarBoletaGanadora = computed(() => {
  return arrayRifa.value.filter(comprador => comprador.estado === 'ganador').length;
});



const boletasPagadasYPorPagar = computed(() => {
  return arrayRifa.value.filter(comprador => comprador.estado === 'pagada' || comprador.estado === 'porPagar' || comprador.estado === 'ganador');
});
// Función para calcular el total de boletas
const totalBoletas = computed(() => {
  return boletasPagadasYPorPagar.value.length;
});

// Función para calcular el total de dinero
const totalPagadas = computed(() => {
  return contarBoletasPagadas.value * valorBoleta.value;
});
const totalPorPagar = computed(() => {
  return contarBoletasPorPagar.value * valorBoleta.value;
});
const totalGanadora = computed(() => {
  return contarBoletaGanadora.value * valorBoleta.value;
});


// mostrar numero loteria 
function mostrarNumeroLoteria() {
  mostrarNumeroLoteriaBool.value = true;
  numeroLoteria.value = ""
}

function guardarNumeroLoteria() {
  // Simulación de una operación asincrónica con setTimeout
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("Número de lotería guardado:", numeroLoteria.value);
      resolve();
    }, 0); // 
  });
}

async function guardarYBuscar() {
  await guardarNumeroLoteria();
  buscarComprador();
}

function buscarComprador() {
  if (!numeroLoteria.value || isNaN(numeroLoteria.value) || numeroLoteria.value < 0 || numeroLoteria.value > 9999) {
    console.error("El número de lotería no es válido");
    return;
  }

  const ultimasCifras = numeroLoteria.value.toString().slice(-2);
  console.log("Numero ganador:", ultimasCifras);

  const compradorGanador = arrayRifa.value.find(comprador => comprador.id === ultimasCifras);

  if (compradorGanador) {
    if (compradorGanador.estado === 'disponible') {
      error.value = "Numero no fue vendido"
      setTimeout(() => {

        error.value = ""
      }, 3000)
      console.log(error.value);
      compradorGanador.estado = 'ganador';
      compradorSeleccionado.value = compradorGanador;
      mostrarDatosBoleta.value = true;
      mostrarNumeroLoteriaBool.value = false


    } else if (compradorGanador.estado === 'porPagar') {
      totalPorPagar.value + valorBoleta.value
      error.value = "Numero fue vendido pero boleta no fue cancelada por lo tanto no se entrega el premio"
      setTimeout(() => {

        error.value = ""
      }, 3000)
      console.log(error.value);

      compradorGanador.estado = 'ganador';

      compradorSeleccionado.value = compradorGanador;
      mostrarDatosBoleta.value = true;
      mostrarNumeroLoteriaBool.value = false

    }
    else {

      compradorGanador.estado = 'ganador';

      compradorSeleccionado.value = compradorGanador;
      mostrarDatosBoleta.value = true;
      mostrarNumeroLoteriaBool.value = false
      totalPagadas.value = totalPagadas.value + totalGanadora.value
    }
  }

}

/* personalizar Temas*/
function seleccionTema() {
  seleccionarTema.value = true
}
function cerrarSelecionarTema() {
  seleccionarTema.value = false
}

function changeTheme(theme) {
  currentTheme.value = theme;
}

/*pdf */

function exportarTablaPDF() {
  let element = document.getElementById('element-to-pdf');
  let opt = {
    margin: 0.3,
    filename: 'Rifa.pdf',
    image: { type: 'jpeg', quality: 0.98 },
    html2canvas: { scale: 3 },
    jsPDF: { unit: 'in', format: 'letter', orientation: 'landscape' },
    overflow: 'auto'
  };
 

  html2pdf().from(element).set(opt).save();
}


</script>
