<template>
  <div class="hotel-app">
    <div class="app-shell">
      <section class="hero">
        <h1>{{ appTitle }}</h1>
        <p>{{ appDescription }}</p>
      </section>

      <section class="dashboard">
        <article class="card">
          <div class="card-header">
            <h2>Nueva reserva</h2>
            <p>Registra huéspedes, habitaciones y estados de hospedaje.</p>
          </div>

          <div class="card-body">
            <div class="stats">
              <div class="stat-box">
                <span>Total reservas</span>
                <strong>{{ reservations.length }}</strong>
              </div>

              <div class="stat-box">
                <span>Confirmadas</span>
                <strong>{{ confirmedCount }}</strong>
              </div>

              <div class="stat-box">
                <span>Check-in</span>
                <strong>{{ checkedInCount }}</strong>
              </div>

              <div class="stat-box">
                <span>Check-out</span>
                <strong>{{ checkedOutCount }}</strong>
              </div>
            </div>

            <div
              v-if="feedback.message"
              :class="[
                'message',
                feedback.type === 'success' ? 'message-success' : 'message-error'
              ]"
            >
              {{ feedback.message }}
            </div>

            <form @submit.prevent="handleSubmit">
              <div class="field-group">
                <label for="guest">Nombre del huésped</label>
                <input
                  id="guest"
                  type="text"
                  v-model.trim="form.guest"
                  placeholder="Ejemplo: Carlos Ramírez"
                />
                <span class="field-error" v-if="errors.guest">{{ errors.guest }}</span>
              </div>

              <div class="field-row">
                <div class="field-group">
                  <label for="room">Número de habitación</label>
                  <input
                    id="room"
                    type="text"
                    v-model.trim="form.room"
                    placeholder="Ejemplo: 204"
                  />
                  <span class="field-error" v-if="errors.room">{{ errors.room }}</span>
                </div>

                <div class="field-group">
                  <label for="type">Tipo de habitación</label>
                  <select id="type" v-model="form.roomType">
                    <option value="">Seleccione una opción</option>
                    <option value="Individual">Individual</option>
                    <option value="Doble">Doble</option>
                    <option value="Suite">Suite</option>
                    <option value="Familiar">Familiar</option>
                  </select>
                  <span class="field-error" v-if="errors.roomType">{{ errors.roomType }}</span>
                </div>
              </div>

              <div class="field-row">
                <div class="field-group">
                  <label for="entry">Fecha de entrada</label>
                  <input id="entry" type="date" v-model="form.checkInDate" />
                  <span class="field-error" v-if="errors.checkInDate">{{ errors.checkInDate }}</span>
                </div>

                <div class="field-group">
                  <label for="exit">Fecha de salida</label>
                  <input id="exit" type="date" v-model="form.checkOutDate" />
                  <span class="field-error" v-if="errors.checkOutDate">{{ errors.checkOutDate }}</span>
                </div>
              </div>

              <div class="field-row">
                <div class="field-group">
                  <label for="guests">Cantidad de personas</label>
                  <input
                    id="guests"
                    type="number"
                    min="1"
                    v-model.number="form.people"
                    placeholder="Ejemplo: 2"
                  />
                  <span class="field-error" v-if="errors.people">{{ errors.people }}</span>
                </div>

                <div class="field-group">
                  <label for="status">Estado de la reserva</label>
                  <select id="status" v-model="form.status">
                    <option value="">Seleccione una opción</option>
                    <option value="Confirmada">Confirmada</option>
                    <option value="Check-in">Check-in</option>
                    <option value="Check-out">Check-out</option>
                  </select>
                  <span class="field-error" v-if="errors.status">{{ errors.status }}</span>
                </div>
              </div>

              <div class="field-group">
                <label for="phone">Teléfono de contacto</label>
                <input
                  id="phone"
                  type="text"
                  v-model.trim="form.phone"
                  placeholder="Ejemplo: 7777-8888"
                />
                <span class="field-error" v-if="errors.phone">{{ errors.phone }}</span>
              </div>

              <div class="actions">
                <button type="submit" class="btn-primary">
                  {{ isEditing ? 'Actualizar reserva' : 'Guardar reserva' }}
                </button>

                <button type="button" class="btn-secondary" @click="resetForm">
                  Limpiar formulario
                </button>
              </div>
            </form>
          </div>
        </article>

        <article class="card">
          <div class="card-header">
            <h2>Listado de reservas</h2>
            <p>Consulta, filtra, actualiza estado o elimina registros del hotel.</p>
          </div>

          <div class="card-body">
            <div class="toolbar">
              <input
                type="text"
                v-model.trim="searchText"
                placeholder="Buscar por huésped, habitación o teléfono..."
              />

              <select v-model="statusFilter">
                <option value="Todos">Todos los estados</option>
                <option value="Confirmada">Confirmada</option>
                <option value="Check-in">Check-in</option>
                <option value="Check-out">Check-out</option>
              </select>
            </div>

            <div v-if="filteredReservations.length > 0" class="table-wrap">
              <table>
                <thead>
                  <tr>
                    <th>#</th>
                    <th>Huésped</th>
                    <th>Habitación</th>
                    <th>Tipo</th>
                    <th>Entrada</th>
                    <th>Salida</th>
                    <th>Personas</th>
                    <th>Teléfono</th>
                    <th>Estado</th>
                    <th>Acciones</th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="(reservation, index) in filteredReservations"
                    :key="reservation.id"
                  >
                    <td>{{ index + 1 }}</td>
                    <td>{{ reservation.guest }}</td>
                    <td>{{ reservation.room }}</td>
                    <td>{{ reservation.roomType }}</td>
                    <td>{{ formatDate(reservation.checkInDate) }}</td>
                    <td>{{ formatDate(reservation.checkOutDate) }}</td>
                    <td>{{ reservation.people }}</td>
                    <td>{{ reservation.phone }}</td>
                    <td>
                      <span
                        class="badge"
                        :class="{
                          'badge-confirmada': reservation.status === 'Confirmada',
                          'badge-checkin': reservation.status === 'Check-in',
                          'badge-checkout': reservation.status === 'Check-out'
                        }"
                      >
                        {{ reservation.status }}
                      </span>
                    </td>
                    <td>
                      <div class="row-actions">
                        <button
                          class="btn-small btn-secondary"
                          @click="editReservation(reservation.id)"
                        >
                          Editar
                        </button>

                        <button
                          v-if="reservation.status === 'Confirmada'"
                          class="btn-small btn-success"
                          @click="markCheckIn(reservation.id)"
                        >
                          Hacer check-in
                        </button>

                        <button
                          v-if="reservation.status === 'Check-in'"
                          class="btn-small btn-warning"
                          @click="markCheckOut(reservation.id)"
                        >
                          Hacer check-out
                        </button>

                        <button
                          class="btn-small btn-danger"
                          @click="deleteReservation(reservation.id)"
                        >
                          Eliminar
                        </button>
                      </div>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>

            <div v-else class="empty-state">
              No hay reservas que coincidan con la búsqueda o el filtro seleccionado.
            </div>
          </div>
        </article>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  name: "RegistroHotel",
  data() {
    return {
      appTitle: "Sistema Inteligente de Reservas de Hotel",
      appDescription:
        "Aplicación desarrollada en Vue.js para registrar, buscar, filtrar y administrar reservas de habitaciones dentro de un hotel.",

      form: {
        guest: "",
        room: "",
        roomType: "",
        checkInDate: "",
        checkOutDate: "",
        people: 1,
        status: "",
        phone: ""
      },

      errors: {
        guest: "",
        room: "",
        roomType: "",
        checkInDate: "",
        checkOutDate: "",
        people: "",
        status: "",
        phone: ""
      },

      feedback: {
        message: "",
        type: "success"
      },

      searchText: "",
      statusFilter: "Todos",
      editingReservationId: null,

      reservations: [
        {
          id: 1,
          guest: "María López",
          room: "101",
          roomType: "Doble",
          checkInDate: "2026-03-18",
          checkOutDate: "2026-03-20",
          people: 2,
          status: "Confirmada",
          phone: "7123-4567"
        },
        {
          id: 2,
          guest: "José Ramírez",
          room: "305",
          roomType: "Suite",
          checkInDate: "2026-03-16",
          checkOutDate: "2026-03-19",
          people: 3,
          status: "Check-in",
          phone: "7890-1122"
        }
      ]
    };
  },

  computed: {
    confirmedCount() {
      return this.reservations.filter(item => item.status === "Confirmada").length;
    },

    checkedInCount() {
      return this.reservations.filter(item => item.status === "Check-in").length;
    },

    checkedOutCount() {
      return this.reservations.filter(item => item.status === "Check-out").length;
    },

    isEditing() {
      return this.editingReservationId !== null;
    },

    filteredReservations() {
      return this.reservations.filter(item => {
        const text = this.searchText.toLowerCase();

        const matchesText =
          item.guest.toLowerCase().includes(text) ||
          item.room.toLowerCase().includes(text) ||
          item.phone.toLowerCase().includes(text);

        const matchesStatus =
          this.statusFilter === "Todos" || item.status === this.statusFilter;

        return matchesText && matchesStatus;
      });
    }
  },

  methods: {
    clearErrors() {
      this.errors = {
        guest: "",
        room: "",
        roomType: "",
        checkInDate: "",
        checkOutDate: "",
        people: "",
        status: "",
        phone: ""
      };
    },

    showFeedback(message, type = "success") {
      this.feedback.message = message;
      this.feedback.type = type;

      setTimeout(() => {
        this.feedback.message = "";
      }, 2500);
    },

    validateForm() {
      this.clearErrors();
      let isValid = true;

      if (!this.form.guest || this.form.guest.length < 3) {
        this.errors.guest = "Ingresa un nombre válido del huésped.";
        isValid = false;
      }

      if (!this.form.room || this.form.room.length < 1) {
        this.errors.room = "Debes ingresar el número de habitación.";
        isValid = false;
      }

      if (!this.form.roomType) {
        this.errors.roomType = "Selecciona el tipo de habitación.";
        isValid = false;
      }

      if (!this.form.checkInDate) {
        this.errors.checkInDate = "Selecciona la fecha de entrada.";
        isValid = false;
      }

      if (!this.form.checkOutDate) {
        this.errors.checkOutDate = "Selecciona la fecha de salida.";
        isValid = false;
      }

      if (
        this.form.checkInDate &&
        this.form.checkOutDate &&
        this.form.checkOutDate < this.form.checkInDate
      ) {
        this.errors.checkOutDate =
          "La fecha de salida no puede ser menor que la entrada.";
        isValid = false;
      }

      if (!this.form.people || this.form.people < 1) {
        this.errors.people =
          "La cantidad de personas debe ser mayor o igual a 1.";
        isValid = false;
      }

      if (!this.form.status) {
        this.errors.status = "Selecciona el estado de la reserva.";
        isValid = false;
      }

      if (!this.form.phone || this.form.phone.length < 8) {
        this.errors.phone = "Ingresa un teléfono válido.";
        isValid = false;
      }

      return isValid;
    },

    handleSubmit() {
      if (!this.validateForm()) {
        this.showFeedback("Corrige los campos marcados antes de continuar.", "error");
        return;
      }

      if (this.isEditing) {
        const index = this.reservations.findIndex(
          item => item.id === this.editingReservationId
        );

        if (index !== -1) {
          this.reservations[index] = {
            ...this.reservations[index],
            ...this.form,
            people: Number(this.form.people)
          };
          this.showFeedback("La reserva fue actualizada correctamente.");
        }

        this.editingReservationId = null;
      } else {
        const newReservation = {
          id: Date.now(),
          ...this.form,
          people: Number(this.form.people)
        };

        this.reservations.unshift(newReservation);
        this.showFeedback("La reserva fue registrada correctamente.");
      }

      this.resetForm(false);
    },

    resetForm(clearMessage = true) {
      this.form = {
        guest: "",
        room: "",
        roomType: "",
        checkInDate: "",
        checkOutDate: "",
        people: 1,
        status: "",
        phone: ""
      };

      this.editingReservationId = null;
      this.clearErrors();

      if (clearMessage) {
        this.feedback.message = "";
      }
    },

    deleteReservation(id) {
      this.reservations = this.reservations.filter(item => item.id !== id);
      this.showFeedback("La reserva fue eliminada.");
    },

    markCheckIn(id) {
      const reservation = this.reservations.find(item => item.id === id);
      if (reservation) {
        reservation.status = "Check-in";
        this.showFeedback("La reserva fue actualizada a check-in.");
      }
    },

    markCheckOut(id) {
      const reservation = this.reservations.find(item => item.id === id);
      if (reservation) {
        reservation.status = "Check-out";
        this.showFeedback("La reserva fue actualizada a check-out.");
      }
    },

    editReservation(id) {
      const reservation = this.reservations.find(item => item.id === id);
      if (!reservation) return;

      this.form = {
        guest: reservation.guest,
        room: reservation.room,
        roomType: reservation.roomType,
        checkInDate: reservation.checkInDate,
        checkOutDate: reservation.checkOutDate,
        people: reservation.people,
        status: reservation.status,
        phone: reservation.phone
      };

      this.editingReservationId = id;
      this.clearErrors();
      this.showFeedback("Ahora puedes editar la reserva seleccionada.");
    },

    formatDate(date) {
      if (!date) return "";
      const [year, month, day] = date.split("-");
      return `${day}/${month}/${year}`;
    }
  }
};
</script>

<style scoped>
.hotel-app {
  --bg-main: #f4f7fb;
  --bg-soft: #edf3f8;
  --card: #ffffff;
  --primary: #1f4e5f;
  --primary-dark: #163946;
  --primary-soft: #dceef2;
  --accent: #2c7a7b;
  --text: #1f2937;
  --muted: #5f6b7a;
  --border: #d7e2ea;
  --shadow: 0 12px 28px rgba(18, 52, 77, 0.08);
  --radius: 18px;
  --success-bg: #e8f7ee;
  --success-text: #167c45;
  --warning-bg: #fff4e5;
  --warning-text: #b4690e;
  --danger-bg: #fdeaea;
  --danger-text: #c53030;
  --info-bg: #e7f1fb;
  --info-text: #1f6fb2;

  min-height: 100vh;
  background: linear-gradient(135deg, #eef4f8, #f7fafc);
  color: var(--text);
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
}

* {
  box-sizing: border-box;
}

.app-shell {
  max-width: 1250px;
  margin: 0 auto;
  padding: 32px 20px 50px;
}

.hero {
  background: linear-gradient(135deg, var(--primary), #2d6d83);
  color: #ffffff;
  padding: 32px;
  border-radius: 24px;
  box-shadow: var(--shadow);
  margin-bottom: 26px;
}

.hero h1 {
  margin: 0 0 10px;
  font-size: 2.2rem;
  font-weight: 800;
}

.hero p {
  margin: 0;
  color: rgba(255, 255, 255, 0.92);
  font-size: 1rem;
  line-height: 1.6;
  max-width: 850px;
}

.dashboard {
  display: grid;
  grid-template-columns: 1.1fr 1.9fr;
  gap: 24px;
  align-items: start;
}

.card {
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: 22px;
  box-shadow: var(--shadow);
  overflow: hidden;
}

.card-header {
  padding: 24px 24px 10px;
}

.card-header h2 {
  margin: 0;
  font-size: 1.8rem;
  color: var(--primary-dark);
}

.card-header p {
  margin: 8px 0 0;
  color: var(--muted);
  font-size: 1rem;
  line-height: 1.5;
}

.card-body {
  padding: 20px 24px 24px;
}

.stats {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 14px;
  margin-bottom: 22px;
}

.stat-box {
  background: #f8fbfd;
  border: 1px solid var(--border);
  border-radius: 16px;
  padding: 16px;
}

.stat-box span {
  display: block;
  font-size: 0.95rem;
  color: var(--muted);
  margin-bottom: 8px;
}

.stat-box strong {
  font-size: 2rem;
  color: var(--primary-dark);
  font-weight: 800;
}

form {
  display: grid;
  gap: 16px;
}

.field-group {
  display: grid;
  gap: 7px;
}

.field-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 16px;
}

label {
  font-weight: 700;
  font-size: 0.98rem;
  color: var(--primary-dark);
}

input,
select {
  width: 100%;
  padding: 13px 14px;
  border: 1px solid var(--border);
  border-radius: 14px;
  outline: none;
  font-size: 1rem;
  background: #ffffff;
  color: var(--text);
  transition: all 0.2s ease;
}

input::placeholder {
  color: #8a97a6;
}

input:focus,
select:focus {
  border-color: var(--accent);
  box-shadow: 0 0 0 4px rgba(44, 122, 123, 0.14);
}

.field-error {
  color: var(--danger-text);
  font-size: 0.85rem;
  font-weight: 600;
}

.actions {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  margin-top: 4px;
}

button {
  border: none;
  border-radius: 12px;
  padding: 11px 16px;
  font-size: 0.95rem;
  font-weight: 700;
  cursor: pointer;
  transition: 0.2s ease;
}

.btn-primary {
  background: var(--primary);
  color: #fff;
}

.btn-primary:hover {
  background: var(--primary-dark);
}

.btn-secondary {
  background: #eef3f7;
  color: #334155;
}

.btn-secondary:hover {
  background: #dde7ee;
}

.toolbar {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 14px;
  margin-bottom: 18px;
}

.message {
  padding: 14px 16px;
  border-radius: 14px;
  margin-bottom: 16px;
  font-weight: 700;
  font-size: 0.94rem;
}

.message-success {
  background: var(--success-bg);
  color: var(--success-text);
  border: 1px solid #b8e3c8;
}

.message-error {
  background: var(--danger-bg);
  color: var(--danger-text);
  border: 1px solid #f3b6b6;
}

.table-wrap {
  overflow-x: auto;
  border: 1px solid var(--border);
  border-radius: 16px;
}

table {
  width: 100%;
  border-collapse: collapse;
  min-width: 980px;
  background: #fff;
}

thead {
  background: #f4f8fb;
}

th,
td {
  padding: 15px 12px;
  text-align: left;
  border-bottom: 1px solid #e7eef4;
  font-size: 0.96rem;
  vertical-align: middle;
}

th {
  color: var(--primary-dark);
  font-weight: 800;
}

tbody tr:hover {
  background: #fafcfe;
}

.badge {
  display: inline-block;
  padding: 8px 13px;
  border-radius: 999px;
  font-size: 0.82rem;
  font-weight: 800;
}

.badge-confirmada {
  background: var(--info-bg);
  color: var(--info-text);
}

.badge-checkin {
  background: var(--success-bg);
  color: var(--success-text);
}

.badge-checkout {
  background: #eef2f7;
  color: #4b5563;
}

.row-actions {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
}

.btn-small {
  padding: 8px 12px;
  border-radius: 10px;
  font-size: 0.82rem;
  font-weight: 700;
}

.btn-success {
  background: var(--success-bg);
  color: var(--success-text);
}

.btn-success:hover {
  background: #d9f0e2;
}

.btn-warning {
  background: var(--warning-bg);
  color: var(--warning-text);
}

.btn-warning:hover {
  background: #ffe8c7;
}

.btn-danger {
  background: var(--danger-bg);
  color: var(--danger-text);
}

.btn-danger:hover {
  background: #f9d4d4;
}

.empty-state {
  text-align: center;
  padding: 30px 18px;
  color: var(--muted);
  border: 1px dashed var(--border);
  border-radius: 16px;
  background: #fbfdff;
  font-weight: 600;
}

@media (max-width: 980px) {
  .dashboard {
    grid-template-columns: 1fr;
  }

  .toolbar,
  .field-row,
  .stats {
    grid-template-columns: 1fr;
  }

  .hero h1 {
    font-size: 1.7rem;
  }
}
</style>