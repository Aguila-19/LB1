# Fredys-David-laboratorio
en este repositorio esta nuestro Laboratorio 1 del computo 2 

<img src="https://ugb.edu.sv/wp-content/uploads/2023/06/UGB_LOGOTIPO_HORIZONTAL.png">

 **Docente:Willian Alexis Montes Girón**

 **Estudiante: David Alfonso Alvarenga Bonilla**
 
 **Estudiante: Fredys Alejandro Hernadez Robles**

# Situación problemática

En muchos hoteles pequeños o medianos, el control de reservas todavía se lleva de forma manual, en cuadernos, llamadas telefónicas o archivos poco organizados.
Esto provoca errores como registrar mal los datos del huésped, confundir fechas de entrada y salida, duplicar habitaciones o no llevar un control claro del estado de cada reserva.
Esta situación afecta la atención al cliente, genera desorden administrativo y puede ocasionar pérdidas económicas cuando una habitación se asigna mal o no se actualiza su estado a tiempo.

# Enunciado del problema
El hotel necesita una página web que permita registrar y administrar reservas de habitaciones de manera rápida, ordenada y validada, 
con el fin de reducir errores, mejorar el control de huéspedes y facilitar el seguimiento del estado de cada reserva.

# Sectores a los que va enfocada la solución
Esta solución puede ser utilizada en:
- Hoteles
- Hostales
- Moteles
- Casas de huéspedes
- Negocios de alojamiento turístico

# Cómo el programa resuelve el problema
La aplicación desarrollada permite:
- Registrar el nombre del huésped
- Registrar el número de habitación
- Seleccionar el tipo de habitación
- Registrar fecha de entrada y fecha de salida
- Registrar la cantidad de personas
- Guardar un teléfono de contacto
- Asignar el estado de la reserva
- Validar que no existan datos vacíos o incorrectos
- Buscar reservas registradas
- Filtrar reservas por estado
- Editar reservas
- Marcar check-in y check-out
- Eliminar registros incorrectos

Con estas funciones, el hotel puede llevar un mejor control de sus reservas y reducir errores en el proceso de hospedaje.

# Preguntas 

### ¿Explique con sus propias palabras qué es Vue.js y cuál es su función dentro de la página web desarrollada.?
Vue.js es un framework de JavaScript que sirve para crear interfaces web dinámicas e interactivas. 
En esta aplicación se utilizó para conectar los datos del formulario con la vista, actualizar automáticamente la tabla de reservas,
mostrar mensajes, aplicar filtros y controlar la lógica de la aplicación sin necesidad de recargar la página.

### Describa qué variables reactivas utilizó en su aplicación y cuál es la función de cada una dentro del sistema.

En la aplicación se utilizaron las siguientes variables reactivas:

- appTitle: guarda el título principal del sistema.
- appDescription: muestra una breve descripción de la aplicación.
- form: almacena todos los datos del formulario, como huésped, habitación, tipo, fechas, personas, estado y teléfono.
- errors: guarda los mensajes de error de cada campo cuando la validación falla.
- feedback: muestra mensajes generales de éxito o error.
- searchText: almacena el texto que el usuario escribe para buscar reservas.
- statusFilter: permite filtrar las reservas por estado.
- editingReservationId: guarda el id de la reserva que se está editando.
- reservations: almacena la lista completa de reservas registradas.

### xplique la diferencia entre las siguientes directivas utilizadas en su proyecto: v-bind y v-model

v-bind se utiliza para enlazar atributos o propiedades de elementos HTML con datos dinámicos de Vue. 
En este proyecto se usó en :class para cambiar el estilo visual del estado de la reserva y en :key dentro del recorrido de la tabla.

v-model, en cambio, se utiliza para enlazar directamente lo que el usuario escribe o selecciona en los formularios con las variables reactivas. 
Gracias a eso, los datos de los inputs y selects se guardan automáticamente dentro del objeto form.

### Mencione al menos un ejemplo de evento utilizado dentro de su aplicación.

- @submit.prevent="handleSubmit" para guardar o actualizar una reserva cuando se envía el formulario.
- También se usaron eventos @click en botones como:
  - @click="editReservation(reservation.id)"
  - @click="markCheckIn(reservation.id)"

  ### Explique para qué utilizó la directiva v-for dentro de su aplicación.
La directiva v-for se utilizó para recorrer la lista de reservas almacenadas en filteredReservations y mostrarlas dinámicamente dentro de la tabla. 
Esto permite que cada nueva reserva registrada aparezca automáticamente en pantalla y que también se puedan visualizar las reservas filtradas o buscadas.

### Describa en qué situación utilizó v-if y qué problema resuelve dentro de su interfaz.

La directiva v-if se utilizó en varias situaciones:
- Para mostrar mensajes de éxito o error
- Para mostrar errores debajo de cada campo del formulario
- Para decidir cuándo mostrar ciertos botones según el estado de la reserva
- Para mostrar la tabla solo cuando existen resultados
- Para mostrar un mensaje cuando no hay registros o no hay coincidencias en la búsqueda

Esto resuelve el problema de mostrar información innecesaria y hace que la interfaz sea más clara, ordenada y fácil de entender para el usuario.

### Explique cómo se realiza la validación de datos en su aplicación y por qué es importante validar la información ingresada por el usuario.

La validación se realiza en el método validateForm(). Ahí se revisa que los campos obligatorios no estén vacíos y que los datos tengan sentido. Por ejemplo:
- que el nombre del huésped tenga una longitud válida
- que se ingrese un número de habitación
- que se seleccione un tipo de habitación
- que la fecha de salida no sea menor que la fecha de entrada
- que la cantidad de personas sea mayor o igual a 1
- que el teléfono tenga un formato aceptable
- que el estado de la reserva esté seleccionado

Validar la información es importante porque evita errores, mejora la confiabilidad del sistema y ayuda a que no se registren datos incompletos o incorrectos que después causen problemas en el control del hotel.
