<template>
  <div class="contenedorPrincipal">
    <div
      class="encabezado"
      :style="{
        padding: '20px',
        'background-color':
          colorEncabezado === '1'
            ? '#F1B300'
            : colorEncabezado === '2'
            ? '#78A036'
            : colorEncabezado === '3'
            ? '#BD5288'
            : colorEncabezado === '4'
            ? '#F6B363'
            : '#AEDBB7',
      }"
    >
      <h1 class="titulo">GlowRifas💫</h1>
      <div class="frase">Donde ganar es seguro y justo</div>
    </div>

    <!-- Mensaje de bienvenida -->
    <div v-if="mostrarPantallaBienvenida" class="bienvenida">
      <div class="alert alert-success text-center" role="alert">
        ¡Bienvenido a GlowRifas! Configura tu talonario para comenzar 🎉
      </div>
    </div>

    <!-- Modal Talonario -->
    <div
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
      data-bs-backdrop="static"
      data-bs-keyboard="false"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div
            class="modal-header"
            :style="{
              backgroundColor:
                colorEncabezado === '1'
                  ? '#F1B300'
                  : colorEncabezado === '2'
                  ? '#78A036'
                  : colorEncabezado === '3'
                  ? '#BD5288'
                  : colorEncabezado === '4'
                  ? '#F6B363'
                  : '#a5d6a7',
              color: 'white',
            }"
          >
            <h1 class="modal-title fs-5" id="exampleModalLabel">
              CONFIGURA TU TALONARIO
            </h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <form>
              <div class="mb-3">
                <input
                  v-model="formularioTalonario.premioRifa"
                  type="text"
                  class="form-control"
                  placeholder="Ingrese el premio de la rifa"
                />
              </div>
              <div class="mb-3">
                <input
                  v-model="formularioTalonario.valorPorBoleta"
                  type="text"
                  class="form-control"
                  placeholder="Ingrese el valor de la boleta"
                />
              </div>
              <div class="mb-3">
                <select
                  v-model="formularioTalonario.nombreLoteria"
                  class="form-control"
                >
                  <option value="" disabled hidden>
                    Seleccione la lotería
                  </option>
                  <option value="Baloto">Baloto</option>
                  <option value="Cruz Roja">Cruz Roja</option>
                  <option value="La Perla">La Perla</option>
                  <option value="Santander">Santander</option>
                  <option value="Lotería de Bogotá">Lotería de Bogotá</option>
                  <option value="Lotería de Medellín">
                    Lotería de Medellín
                  </option>
                  <option value="Lotería de Boyacá">Lotería de Boyacá</option>
                </select>
              </div>
              <div class="mb-3">
                <select
                  v-model="formularioTalonario.totalBoletas"
                  class="form-control"
                  :disabled="estaEnModoEdicion"
                >
                  <option value="" disabled hidden>Cantidad de boletas</option>
                  <option value="100">100</option>
                  <option value="1000">1000</option>
                </select>
              </div>
              <div class="mb-3">
                <input
                  v-model="formularioTalonario.fechaDelSorteo"
                  :min="fechaMinimaFormatoInput"
                  type="date"
                  class="form-control"
                  placeholder="Fecha de sorteo"
                />
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              Cerrar
            </button>
            <button
              type="button"
              class="btn btn botonGuardar"
              @click="guardarTalonario"
            >
              {{ estaEnModoEdicion ? "Actualizar" : "Guardar" }}
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="contenedor">
      <div class="informacion">
        <h4 class="titulo-info">Información del Talonario</h4>
        <div class="cuerpo-acciones">
          <div class="talonario-info" v-if="datosTalonarioGuardado">
            <div class="info-talonario">
              <strong>🏆Premio:</strong> {{ datosTalonarioGuardado.premioRifa }}
            </div>
            <div class="info-talonario">
              <strong>💲Valor Boleta:</strong>
              {{ datosTalonarioGuardado.valorPorBoleta }}
            </div>
            <div class="info-talonario">
              <strong>🏦Lotería:</strong>
              {{ datosTalonarioGuardado.nombreLoteria }}
            </div>
            <div class="info-talonario">
              <strong>🗓️Fecha de Sorteo:</strong>
              {{ datosTalonarioGuardado.fechaDelSorteo }}
            </div>
            <div class="contenedor-boton">
              <button
                class="btn-editar"
                @click="editarTalonario"
                :style="estiloBotones"
              >
                <i class="bi bi-pencil-square icono-animado"></i> Editar
              </button>
            </div>
          </div>
          <div v-else>
            <p>
              <i class="bi bi-exclamation-circle me-1"></i> No hay datos del
              talonario aún.
            </p>
          </div>
        </div>
      </div>


      <div class="acciones">
        <h4 class="titulo-info">Acciones</h4>
        <div class="cuerpo-accion">
          <button
            class="btn-acciones"
            data-bs-toggle="modal"
            data-bs-target="#listadoModal"
            :style="estiloBotones"
          >
            <i class="bi bi-journal-text icono-animado"></i>Listar Boletas
          </button>
          <button
            class="btn-acciones"
            data-bs-toggle="modal"
            data-bs-target="#personalizarModal"
            :style="estiloBotones"
          >
            <i class="bi bi-brush icono-animado"></i>Personalizar
          </button>
        </div>
      </div>
    </div>

    <div class="boleta-grid">
        <template v-if="listaDeBoletas.length">
          <div
            v-for="(boleta, index) in listaDeBoletas"
            :key="index"
            class="boleta-item"
          >
            <button
              class="rounded-circle boleta"
              type="button"
              @click="seleccionarBoleta(boleta)"
              data-bs-toggle="modal"
              data-bs-target="#boletaModal"
              :style="{
                backgroundColor: colorBoleta(boleta.estado),
                color: 'white',
                fontWeight: '900',
                border: '1px solid black',
                width: '45px',
                height: '45px',
              }"
            >
              {{ boleta.numero }}
            </button>
          </div>
        </template>
        <template v-else>
          <div class="espacio-placeholder">
            <div class="spinner"></div>
            <p>Cargando boletas...</p>
          </div>
        </template>
      </div>

    <!-- Modal de Boleta -->
    <div
      class="modal fade"
      id="boletaModal"
      tabindex="-1"
      aria-labelledby="boletaModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
          <div
            class="modal-header"
            :style="{
              backgroundColor:
                colorEncabezado === '1'
                  ? '#F1B300'
                  : colorEncabezado === '2'
                  ? '#78A036'
                  : colorEncabezado === '3'
                  ? '#BD5288'
                  : colorEncabezado === '4'
                  ? '#F6B363'
                  : '#a5d6a7',
              color: 'white',
            }"
          >
            <h5 class="modal-title" id="boletaModalLabel">
              Boleta #{{ numeroSeleccionado }}
            </h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div v-if="estadoSeleccionado === 0" class="text-center">
              <h6 class="mb-3">Estado: Disponible</h6>
              <button
                type="button"
                class="btn btn-primary mb-2"
                data-bs-toggle="modal"
                data-bs-target="#staticBackdrop2"
                data-bs-dismiss="modal"
                :style="{ backgroundColor: colorBotones }"
              >
                <i class="bi bi-person-plus"></i> Adquirir Boleta
              </button>
            </div>

            <div v-else>
              <h6 class="mb-3">
                Estado:
                <span v-if="estadoSeleccionado === 1">Apartado</span>
                <span v-if="estadoSeleccionado === 2">Pagado</span>
                <span v-if="estadoSeleccionado === 3">Ganador</span>
              </h6>

              <div class="d-grid gap-2">
                <button
                  type="button"
                  class="btn btn-primary mb-2"
                  data-bs-toggle="modal"
                  data-bs-target="#participante"
                  data-bs-dismiss="modal"
                  :style="{ backgroundColor: colorBotones }"
                >
                  <i class="bi bi-eye"></i> Ver Participante
                </button>

                <button
                  v-if="estadoSeleccionado !== 3"
                  type="button"
                  class="btn btn-danger mb-2"
                  @click="liberarBoleta"
                  data-bs-dismiss="modal"
                >
                  <i class="bi bi-x-circle"></i> Liberar Boleta
                </button>

                <button
                  v-if="estadoSeleccionado === 1"
                  type="button"
                  class="btn btn-success mb-2"
                  @click="marcarComoPagada"
                  data-bs-dismiss="modal"
                  :style="{ backgroundColor: colorBotones }"
                >
                  <i class="bi bi-cash"></i> Marcar como Pagada
                </button>

                <button
                  v-if="estadoSeleccionado === 2"
                  type="button"
                  class="btn btn-warning mb-2"
                  @click="elegirGanador"
                  data-bs-dismiss="modal"
                >
                  <i class="bi bi-trophy"></i> Elegir como Ganador
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal para adquirir boleta -->
    <div
      class="modal fade"
      id="staticBackdrop2"
      data-bs-backdrop="static"
      data-bs-keyboard="false"
      tabindex="-1"
      aria-labelledby="staticBackdropLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div
            class="modal-header"
            :style="{
              backgroundColor:
                colorEncabezado === '1'
                  ? '#F1B300'
                  : colorEncabezado === '2'
                  ? '#78A036'
                  : colorEncabezado === '3'
                  ? '#BD5288'
                  : colorEncabezado === '4'
                  ? '#F6B363'
                  : '#a5d6a7',
              color: 'white',
            }"
          >
            <h1 class="modal-title fs-5" id="staticBackdropLabel">
              Datos de Boleta {{ numeroSeleccionado }}
            </h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="mb-3">
              <input
                type="text"
                class="form-control"
                placeholder="Nombre del comprador"
                v-model="nombreCliente"
              />
            </div>
            <div class="mb-3">
              <input
                type="text"
                class="form-control"
                placeholder="Dirección del comprador"
                v-model="direccionCliente"
              />
            </div>
            <div class="mb-3">
              <input
                type="tel"
                class="form-control"
                placeholder="Teléfono (10 dígitos)"
                v-model="telefonoCliente"
                maxlength="10"
              />
            </div>
            <div class="mb-3">
              <select class="form-select" v-model="estadoCompra">
                <option disabled selected value="">Estado de la boleta</option>
                <option value="1">Apartado</option>
                <option value="2">Pagado</option>
              </select>
            </div>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              Cancelar
            </button>
            <button
              type="button"
              class="btn btn-primary"
              @click="validarCliente"
              :style="estiloBotones"
            >
              Registrar
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal de participante -->
    <div
      class="modal fade"
      id="participante"
      tabindex="-1"
      aria-labelledby="participanteLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div
            class="modal-header"
            :style="{
              backgroundColor:
                colorEncabezado === '1'
                  ? '#F1B300'
                  : colorEncabezado === '2'
                  ? '#78A036'
                  : colorEncabezado === '3'
                  ? '#BD5288'
                  : colorEncabezado === '4'
                  ? '#F6B363'
                  : '#a5d6a7',
              color: 'white',
            }"
          >
            <h5 class="modal-title" id="participanteLabel">
              Participante - Boleta #{{ numeroSeleccionado }}
            </h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="row mb-3">
              <div class="col-md-4 fw-bold">Nombre:</div>
              <div class="col-md-8">{{ nombreParticipante || "N/A" }}</div>
            </div>
            <div class="row mb-3">
              <div class="col-md-4 fw-bold">Dirección:</div>
              <div class="col-md-8">{{ direccionParticipante || "N/A" }}</div>
            </div>
            <div class="row mb-3">
              <div class="col-md-4 fw-bold">Teléfono:</div>
              <div class="col-md-8">{{ telefonoParticipante || "N/A" }}</div>
            </div>
            <div class="row mb-3">
              <div class="col-md-4 fw-bold">Fecha:</div>
              <div class="col-md-8">{{ fechaCompraParticipante || "N/A" }}</div>
            </div>
            <div class="row mb-3">
              <div class="col-md-4 fw-bold">Estado:</div>
              <div class="col-md-8">
                <span v-if="estadoParticipante === 1" class="badge bg-danger"
                  >Apartado</span
                >
                <span v-if="estadoParticipante === 2" class="badge bg-success"
                  >Pagado</span
                >
                <span
                  v-if="estadoParticipante === 3"
                  class="badge bg-warning text-dark"
                  >Ganador</span
                >
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-primary"
              data-bs-toggle="modal"
              data-bs-target="#participanteEdit"
              data-bs-dismiss="modal"
              :style="estiloBotones"
            >
              <i class="bi bi-pencil"></i> Editar
            </button>
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              Cerrar
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal para editar participante -->
    <div
      class="modal fade"
      id="participanteEdit"
      tabindex="-1"
      aria-labelledby="participanteEditLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div
            class="modal-header"
            :style="{
              backgroundColor:
                colorEncabezado === '1'
                  ? '#F1B300'
                  : colorEncabezado === '2'
                  ? '#78A036'
                  : colorEncabezado === '3'
                  ? '#BD5288'
                  : colorEncabezado === '4'
                  ? '#F6B363'
                  : '#a5d6a7',
              color: 'white',
            }"
          >
            <h5 class="modal-title" id="participanteEditLabel">
              Editar Participante - Boleta #{{ numeroSeleccionado }}
            </h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="mb-3">
              <label class="form-label">Nombre</label>
              <input
                type="text"
                class="form-control"
                v-model="nombreParticipante"
              />
            </div>
            <div class="mb-3">
              <label class="form-label">Dirección</label>
              <input
                type="text"
                class="form-control"
                v-model="direccionParticipante"
              />
            </div>
            <div class="mb-3">
              <label class="form-label">Teléfono</label>
              <input
                type="tel"
                class="form-control"
                v-model="telefonoParticipante"
                maxlength="10"
              />
            </div>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              Cancelar
            </button>
            <button
              type="button"
              class="btn btn-primary"
              @click="editarParticipante"
              :style="{ backgroundColor: colorBotones }"
            >
              Guardar Cambios
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Listado de boletas -->
    <div class="modal fade" id="listadoModal" tabindex="-1" aria-hidden="true">
      <div class="modal-dialog modal-xl">
        <div class="modal-content">
          <div
            class="modal-header"
            :style="{
              backgroundColor:
                colorEncabezado === '1'
                  ? '#F1B300'
                  : colorEncabezado === '2'
                  ? '#78A036'
                  : colorEncabezado === '3'
                  ? '#BD5288'
                  : colorEncabezado === '4'
                  ? '#F6B363'
                  : '#a5d6a7',
              color: 'white',
            }"
          >
            <h5 class="modal-title">Listado de Boletas Vendidas</h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="table-responsive">
              <table class="table table-striped" id="tab">
                <thead>
                  <tr>
                    <th>N° Boleta</th>
                    <th>Nombre</th>
                    <th>Dirección</th>
                    <th>Teléfono</th>
                    <th>Fecha</th>
                    <th>Estado</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(item, i) in listaDeRegistros" :key="i">
                    <td>{{ item.boleta }}</td>
                    <td>{{ item.nombre }}</td>
                    <td>{{ item.direccion }}</td>
                    <td>{{ item.telefono }}</td>
                    <td>{{ item.fecha }}</td>
                    <td>
                      <span v-if="item.estado === 1" class="badge bg-danger"
                        >Apartado</span
                      >
                      <span v-if="item.estado === 2" class="badge bg-success"
                        >Pagado</span
                      >
                      <span
                        v-if="item.estado === 3"
                        class="badge bg-warning text-dark"
                        >Ganador</span
                      >
                    </td>
                  </tr>
                </tbody>
                <tfoot>
                  <tr>
                    <td colspan="6" class="text-end fw-bold">
                      Total recaudado: {{ valorTotalPagado }}
                    </td>
                  </tr>
                  <tr>
                    <td colspan="6" class="text-end fw-bold">
                      Total por cobrar: {{ valorTotalDeuda }}
                    </td>
                  </tr>
                </tfoot>
              </table>
            </div>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-primary"
              @click="exportarPDF"
              :style="{ backgroundColor: colorBotones }"
            >
              <i class="bi bi-download"></i> Exportar PDF
            </button>
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              Cerrar
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Personalizar colores -->
    <div
      class="modal fade"
      id="personalizarModal"
      tabindex="-1"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div
            class="modal-header"
            :style="{
              backgroundColor:
                colorEncabezado === '1'
                  ? '#F1B300'
                  : colorEncabezado === '2'
                  ? '#78A036'
                  : colorEncabezado === '3'
                  ? '#BD5288'
                  : colorEncabezado === '4'
                  ? '#F6B363'
                  : '#a5d6a7',
              color: 'white',
            }"
          >
            <h5 class="modal-title">Personalizar Colores</h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="mb-4">
              <h6>Color de Encabezado</h6>
              <div class="d-flex gap-3">
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorEncabezado"
                    value="1"
                    id="headerAmarillo"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="headerAmarillo"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #f1b300"
                    ></span>
                    Amarillo
                  </label>
                </div>
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorEncabezado"
                    value="2"
                    id="headerVerde"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="headerVerde"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #78a036"
                    ></span>
                    Verde
                  </label>
                </div>
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorEncabezado"
                    value="3"
                    id="headerRosa"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="headerRosa"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #bd5288"
                    ></span>
                    Rosa
                  </label>
                </div>
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorEncabezado"
                    value="4"
                    id="headerNaranja"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="headerNaranja"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #f6b363"
                    ></span>
                    Naranja
                  </label>
                </div>
              </div>
            </div>

            <div class="mb-4">
              <h6>Color de Pie de Página</h6>
              <div class="d-flex gap-3">
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorPieDePagina"
                    value="1"
                    id="footerAmarillo"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="footerAmarillo"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #f1b300"
                    ></span>
                    Amarillo
                  </label>
                </div>
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorPieDePagina"
                    value="2"
                    id="footerVerde"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="footerVerde"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #78a036"
                    ></span>
                    Verde
                  </label>
                </div>
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorPieDePagina"
                    value="3"
                    id="footerRosa"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="footerRosa"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #bd5288"
                    ></span>
                    Rosa
                  </label>
                </div>
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorPieDePagina"
                    value="4"
                    id="footerNaranja"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="footerNaranja"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #f6b363"
                    ></span>
                    Naranja
                  </label>
                </div>
              </div>
            </div>

            <div class="mb-4">
              <h6>Color de Botones</h6>
              <div class="d-flex gap-3">
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorBotones"
                    value="1"
                    id="botonAmarillo"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="botonAmarillo"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #f1b300"
                    ></span>
                    Amarillo
                  </label>
                </div>
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorBotones"
                    value="2"
                    id="botonVerde"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="botonVerde"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #78a036"
                    ></span>
                    Verde
                  </label>
                </div>
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorBotones"
                    value="3"
                    id="botonRosa"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="botonRosa"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #bd5288"
                    ></span>
                    Rosa
                  </label>
                </div>
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="radio"
                    v-model="colorBotones"
                    value="4"
                    id="botonNaranja"
                  />
                  <label
                    class="form-check-label d-flex align-items-center"
                    for="botonNaranja"
                  >
                    <span
                      class="color-box me-2"
                      style="background-color: #f6b363"
                    ></span>
                    Naranja
                  </label>
                </div>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              @click="restaurarColoresPersonalizados"
            >
              Restaurar colores
            </button>
            <button
              type="button"
              class="btn btn-primary"
              data-bs-dismiss="modal"
              :style="{ backgroundColor: colorBotones }"
            >
              Aplicar cambios
            </button>
          </div>
        </div>
      </div>
    </div>

    <footer
      class="footer mt-5 py-3"
      :style="{
        backgroundColor:
          colorPieDePagina === '1'
            ? '#F1B300'
            : colorPieDePagina === '2'
            ? '#78A036'
            : colorPieDePagina === '3'
            ? '#BD5288'
            : colorPieDePagina === '4'
            ? '#F6B363'
            : '#a5d6a7',
        color: 'white',
        width: '100%',
      }"
    >
      <div class="container text-center">
        <p class="mb-0">
          Copyright ©2025 GlowRifas. Todos los derechos reservados.
        </p>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import jsPDF from "jspdf";
import autoTable from "jspdf-autotable";
import Swal from "sweetalert2";

// Datos del talonario
const formularioTalonario = ref({
  premioRifa: "",
  valorPorBoleta: "",
  nombreLoteria: "",
  totalBoletas: "",
  fechaDelSorteo: "",
});

const datosTalonarioGuardado = ref(null);
const estaEnModoEdicion = ref(false);
const mostrarPantallaBienvenida = ref(true);

// Listas principales
const listaDeRegistros = ref([]);
const listaDeBoletas = ref([]);

// Personalización visual
const historialColores = ref([]);
const colorEncabezado = ref("");
const colorPieDePagina = ref("");
const colorBotones = ref("");

// Control de formularios
const numeroSeleccionado = ref(0);
const estadoSeleccionado = ref(0);
const indiceSeleccionado = ref(0);

// Datos cliente
const nombreCliente = ref("");
const direccionCliente = ref("");
const telefonoCliente = ref("");
const estadoCompra = ref("");

// Datos para vista participante
const nombreParticipante = ref("");
const direccionParticipante = ref("");
const telefonoParticipante = ref("");
const fechaCompraParticipante = ref("");
const estadoParticipante = ref(null);
const textoEstadoParticipante = ref(null);

// Control de modales
const mostrarModalPersonalizar = ref(false);
const mostrarModalListado = ref(false);

// Finanzas
const valorTotalPagado = ref(0);
const valorTotalDeuda = ref(0);
const acumuladoPagos = ref(0);
const acumuladoDeudas = ref(0);

// Control de UI
const estaDeshabilitadoSelect = ref(false);
const estaEditandoCliente = ref(true);
const estaEditandoParticipante = ref(true);
let indiceEdicionCliente = null;
let indiceEdicionParticipante = null;

const estiloBotones = computed(() => {
  return {
    backgroundColor:
      colorBotones.value === "1"
        ? "#F1B300"
        : colorBotones.value === "2"
        ? "#78A036"
        : colorBotones.value === "3"
        ? "#BD5288"
        : colorBotones.value === "4"
        ? "#F6B363"
        : "#6ec4b1ff",
    color: "white",
    border: "none",
  };
});

onMounted(() => {
  setTimeout(() => {
    mostrarPantallaBienvenida.value = false;
    const modalElement = document.getElementById("exampleModal");
    const modalInstance = new bootstrap.Modal(modalElement);

    modalElement.addEventListener("hide.bs.modal", (event) => {
      if (!camposValidos()) {
        event.preventDefault(); // ← aquí estaba el error
        Swal.fire(
          "Formulario incompleto",
          "Por favor completa todos los campos correctamente antes de cerrar.",
          "warning"
        );
      }
    });
    modalInstance.show();
  }, 2500);
});

function camposValidos() {
  const {
    premioRifa,
    valorPorBoleta,
    nombreLoteria,
    totalBoletas,
    fechaDelSorteo,
  } = formularioTalonario.value;
  if (
    !premioRifa ||
    !valorPorBoleta ||
    !nombreLoteria ||
    !totalBoletas ||
    !fechaDelSorteo
  )
    return false;
  const premio = parseFloat(premioRifa);
  const valor = parseFloat(valorPorBoleta);
  if (isNaN(premio) || isNaN(valor)) return false;
  if (premio <= valor) return false;
  return true;
}

function guardarTalonario() {
  const {
    premioRifa,
    valorPorBoleta,
    nombreLoteria,
    totalBoletas,
    fechaDelSorteo,
  } = formularioTalonario.value;

  // Validaciones de campos vacíos
  if (!premioRifa)
    return Swal.fire(
      "Campo requerido",
      "Por favor ingresa el premio de la rifa.",
      "warning"
    );
  if (!valorPorBoleta)
    return Swal.fire(
      "Campo requerido",
      "Por favor ingresa el valor de la boleta.",
      "warning"
    );
  if (!nombreLoteria)
    return Swal.fire(
      "Campo requerido",
      "Por favor selecciona una lotería.",
      "warning"
    );
  if (!totalBoletas)
    return Swal.fire(
      "Campo requerido",
      "Por favor selecciona la cantidad de boletas.",
      "warning"
    );
  if (!fechaDelSorteo)
    return Swal.fire(
      "Campo requerido",
      "Por favor selecciona una fecha de sorteo.",
      "warning"
    );

  // Validaciones numéricas
  const premio = parseFloat(premioRifa);
  const valor = parseFloat(valorPorBoleta);
  if (isNaN(premio))
    return Swal.fire(
      "Valor inválido",
      "El premio debe ser un número.",
      "error"
    );
  if (isNaN(valor))
    return Swal.fire(
      "Valor inválido",
      "El valor de la boleta debe ser un número.",
      "error"
    );
  if (premio <= valor)
    return Swal.fire(
      "Valor incorrecto",
      "El premio debe ser mayor al valor de la boleta.",
      "error"
    );

  // Validación de fecha mínima
  const fechaSeleccionada = new Date(fechaDelSorteo);
  const hoy = new Date();
  hoy.setHours(0, 0, 0, 0);
  const fechaMinima = new Date(hoy);
  fechaMinima.setDate(hoy.getDate() + 8);
  fechaSeleccionada.setHours(0, 0, 0, 0);
  if (fechaSeleccionada < fechaMinima) {
    return Swal.fire({
      icon: "warning",
      title: "Fecha inválida",
      text: "La fecha del sorteo debe ser al menos dentro de 8 días.",
    });
  }

  // Si pasa todas las validaciones
  datosTalonarioGuardado.value = { ...formularioTalonario.value };

  const cantidad = parseInt(totalBoletas);
  listaDeBoletas.value = Array.from({ length: cantidad }, (_, i) => ({
    numero: i,
    estado: 0,
    comprador: {},
  }));

  Swal.fire(
    "¡Guardado!",
    "El talonario se ha configurado correctamente.",
    "success"
  );

  const modalElement = document.getElementById("exampleModal");
  const modalInstance = bootstrap.Modal.getInstance(modalElement);
  modalInstance.hide();

  estaEnModoEdicion.value = false;
}

function editarTalonario() {
  if (!datosTalonarioGuardado.value) {
    Swal.fire("Error", "No hay datos del talonario para editar", "error");
    return;
  }

  // Llenar el formulario con los datos guardados
  formularioTalonario.value = { ...datosTalonarioGuardado.value };
  estaEnModoEdicion.value = true;

  // Mostrar el modal
  const modalElement = document.getElementById("exampleModal");
  const modalInstance = new bootstrap.Modal(modalElement);
  modalInstance.show();
}

function restaurarColor() {
  const color = {
    colorheader: colorheader.value,
    colorfooter: colorfooter.value,
    colorbotones: colorbotones.value,
  };
  colores.value.push(color);
  colorheader.value = "";
  colorfooter.value = "";
  colorbotones.value = "";
}

function download() {
  const doc = new jsPDF("landscape");
  let bodyData = [];
  let tableElement = document.getElementById("tab");
  let rows = tableElement.querySelectorAll("tr");

  for (let i = 1; i < rows.length; i++) {
    let row = rows[i];
    let cols = row.querySelectorAll("td");
    let rowData = [];
    for (let j = 0; j < cols.length; j++) {
      let col = cols[j];
      let we = col.innerText;
      if (col.id === "r") {
        let rowData = [];
        rowData.push({
          content: we,
          colSpan: 6,
        });
        bodyData.push(rowData);
      } else {
        rowData.push(col.innerText);
      }
    }
    bodyData.push(rowData);
  }

  let headers = [
    "Nombre Comprador",
    "Direccion",
    "Numero Telefonico",
    "Fecha Compra Boleta",
    "Estado Boleta",
    "N. Boleta",
  ];
  autoTable(doc, {
    head: [headers],
    body: bodyData,
    styles: {
      halign: "center",
    },
  });
  doc.save("Registro.pdf");
}

function exportarPDF() {
  try {
    // Validar que hay datos
    if (listaDeRegistros.value.length === 0) {
      Swal.fire("Error", "No hay datos para exportar", "warning");
      return;
    }

    const doc = new jsPDF({
      orientation: "landscape",
    });

    // Título
    doc.setFontSize(18);
    doc.text("Reporte de Boletas Vendidas - GlowRifas", 15, 15);
    doc.setFontSize(12);
    doc.text(`Fecha de generación: ${new Date().toLocaleDateString()}`, 15, 25);

    // Encabezados (incluyendo dirección que faltaba)
    const headers = [
      "N° Boleta",
      "Nombre",
      "Dirección",
      "Teléfono",
      "Fecha",
      "Estado",
    ];

    // Datos completos
    const data = listaDeRegistros.value.map((r) => [
      r.boleta,
      r.nombre,
      r.direccion, // Añadido dirección que estaba en tu tabla HTML
      r.telefono,
      r.fecha,
      r.estado === 1 ? "Apartado" : r.estado === 2 ? "Pagado" : "Ganador",
    ]);

    // Tabla
    autoTable(doc, {
      head: [headers],
      body: data,
      startY: 30,
      styles: {
        halign: "center",
        fontSize: 9,
      },
      headStyles: {
        fillColor: [41, 128, 185],
        textColor: 255,
        fontSize: 10,
      },
      margin: { top: 30 },
    });

    // Totales
    const finalY = doc.lastAutoTable.finalY || 30;
    doc.setFontSize(12);
    doc.text(`Total recaudado: ${valorTotalPagado.value}`, 15, finalY + 15);
    doc.text(`Total por cobrar: ${valorTotalDeuda.value}`, 15, finalY + 25);

    // Guardar
    const fecha = new Date().toISOString().slice(0, 10);
    doc.save(`boletas_vendidas_${fecha}.pdf`);

    Swal.fire("Éxito", "PDF generado correctamente", "success");
  } catch (error) {
    console.error("Error al generar PDF:", error);
    Swal.fire("Error", "No se pudo generar el PDF", "error");
  }
}

function validarCliente() {
  const texto = /^[A-Za-zÁÉÍÓÚáéíóúñÑüÜ\s]+$/;

  // Validaciones comunes
  if (nombreCliente.value === "") {
    Swal.fire({
      icon: "error",
      title: "Oops...",
      text: "El nombre del comprador es requerido",
      timer: 3500,
    });
    return;
  }

  if (!texto.test(nombreCliente.value)) {
    Swal.fire({
      icon: "error",
      title: "Oops...",
      text: "El nombre no puede contener números",
      timer: 3500,
    });
    return;
  }

  if (direccionCliente.value === "") {
    Swal.fire({
      icon: "error",
      title: "Oops...",
      text: "La dirección del comprador es requerida",
      timer: 3500,
    });
    return;
  }

  if (telefonoCliente.value === "") {
    Swal.fire({
      icon: "error",
      title: "Oops...",
      text: "El teléfono del comprador es requerido",
      timer: 3500,
    });
    return;
  }

  if (isNaN(telefonoCliente.value)) {
    Swal.fire({
      icon: "error",
      title: "Oops...",
      text: "El teléfono debe ser numérico",
      timer: 3500,
    });
    return;
  }

  if (telefonoCliente.value.length !== 10) {
    Swal.fire({
      icon: "error",
      title: "Oops...",
      text: "El teléfono debe tener 10 dígitos",
      timer: 3500,
    });
    return;
  }

  if (estadoCompra.value === "") {
    Swal.fire({
      icon: "error",
      title: "Oops...",
      text: "Debe seleccionar un estado para la boleta",
      timer: 3500,
    });
    return;
  }

  // Registrar la boleta
  registrarBoleta();

  // Limpiar campos y cerrar modal
  limpiarCamposCliente();

  Swal.fire({
    icon: "success",
    title: "Boleta registrada",
    showConfirmButton: false,
    timer: 1500,
  });

  const modal = bootstrap.Modal.getInstance(
    document.getElementById("staticBackdrop2")
  );
  modal.hide();
}

function registrarBoleta() {
  const estadoNum = parseInt(estadoCompra.value);
  let estadoTexto = "";

  switch (estadoNum) {
    case 1:
      estadoTexto = "Apartado";
      break;
    case 2:
      estadoTexto = "Pagado";
      break;
    case 3:
      estadoTexto = "Ganador";
      break;
    default:
      estadoTexto = "Disponible";
  }

  const cliente = {
    nombre: nombreCliente.value,
    direccion: direccionCliente.value,
    telefono: telefonoCliente.value,
    fecha: new Date().toISOString().split("T")[0],
    estado: estadoNum,
    estadoTexto: estadoTexto,
    boleta: numeroSeleccionado.value,
  };

  // Agregar a registros
  listaDeRegistros.value.push(cliente);

  // Actualizar estado de la boleta
  const boletaIndex = listaDeBoletas.value.findIndex(
    (b) => b.numero === numeroSeleccionado.value
  );
  if (boletaIndex !== -1) {
    listaDeBoletas.value[boletaIndex].estado = estadoNum;
    listaDeBoletas.value[boletaIndex].comprador = cliente;
  }

  // Actualizar totales
  actualizarTotales();
}

function limpiarCamposCliente() {
  nombreCliente.value = "";
  direccionCliente.value = "";
  telefonoCliente.value = "";
  estadoCompra.value = "";
}

function actualizarTotales() {
  if (!datosTalonarioGuardado.value) return;

  const valorBoleta = parseFloat(datosTalonarioGuardado.value.valorPorBoleta);

  // Calcular total pagado
  const totalPagado = listaDeRegistros.value
    .filter((r) => r.estado === 2)
    .reduce((total, r) => total + valorBoleta, 0);

  valorTotalPagado.value = totalPagado.toLocaleString("es-CO", {
    style: "currency",
    currency: "COP",
  });

  // Calcular total por cobrar
  const totalPorCobrar = listaDeRegistros.value
    .filter((r) => r.estado === 1)
    .reduce((total, r) => total + valorBoleta, 0);

  valorTotalDeuda.value = totalPorCobrar.toLocaleString("es-CO", {
    style: "currency",
    currency: "COP",
  });
}

function descontarD() {
  if (!talonarioGuardado.value) return;

  const numero = parseFloat(talonarioGuardado.value.valorBoleta);
  acumm.value -= numero;
  vdeuda.value = acumm.value.toLocaleString("es-CO", {
    style: "currency",
    currency: "COP",
  });
}

function editarParticipante() {
  // Validaciones
  const texto = /^[A-Za-zÁÉÍÓÚáéíóúñÑüÜ\s]+$/;

  if (!nombreParticipante.value) {
    Swal.fire("Error", "El nombre es requerido", "error");
    return;
  }

  if (!texto.test(nombreParticipante.value)) {
    Swal.fire("Error", "El nombre no puede contener números", "error");
    return;
  }

  if (!direccionParticipante.value) {
    Swal.fire("Error", "La dirección es requerida", "error");
    return;
  }

  if (
    !telefonoParticipante.value ||
    isNaN(telefonoParticipante.value) ||
    telefonoParticipante.value.length !== 10
  ) {
    Swal.fire("Error", "Teléfono debe tener 10 dígitos numéricos", "error");
    return;
  }

  // Actualizar en lista de boletas
  const boletaIndex = listaDeBoletas.value.findIndex(
    (b) => b.numero === numeroSeleccionado.value
  );
  if (boletaIndex !== -1 && listaDeBoletas.value[boletaIndex].comprador) {
    listaDeBoletas.value[boletaIndex].comprador = {
      ...listaDeBoletas.value[boletaIndex].comprador,
      nombre: nombreParticipante.value,
      direccion: direccionParticipante.value,
      telefono: telefonoParticipante.value,
    };
  }

  // Actualizar en registros
  const registroIndex = listaDeRegistros.value.findIndex(
    (r) => r.boleta === numeroSeleccionado.value
  );
  if (registroIndex !== -1) {
    listaDeRegistros.value[registroIndex] = {
      ...listaDeRegistros.value[registroIndex],
      nombre: nombreParticipante.value,
      direccion: direccionParticipante.value,
      telefono: telefonoParticipante.value,
    };
  }

  // Cerrar modal y mostrar confirmación
  const modal = bootstrap.Modal.getInstance(
    document.getElementById("participanteEdit")
  );
  modal.hide();

  Swal.fire("Éxito", "Participante actualizado", "success");
}

function marcarComoPagada() {
  // Actualizar en boletas
  const boletaIndex = listaDeBoletas.value.findIndex(
    (b) => b.numero === numeroSeleccionado.value
  );
  if (boletaIndex !== -1) {
    listaDeBoletas.value[boletaIndex].estado = 2;
    if (listaDeBoletas.value[boletaIndex].comprador) {
      listaDeBoletas.value[boletaIndex].comprador.estado = 2;
    }
  }

  // Actualizar en registros
  listaDeRegistros.value.forEach((r) => {
    if (r.boleta === numeroSeleccionado.value) {
      r.estado = 2;
      r.estadoTexto = "Pagado";
    }
  });

  // Actualizar totales
  actualizarTotales();

  // Cerrar modal y mostrar confirmación
  const modal = bootstrap.Modal.getInstance(
    document.getElementById("boletaModal")
  );
  modal.hide();

  Swal.fire("Éxito", "Boleta marcada como pagada", "success");
}

function liberarBoleta() {
  Swal.fire({
    title: "¿Liberar boleta?",
    text: "El participante perderá su compra",
    icon: "warning",
    showCancelButton: true,
    confirmButtonColor: "#3085d6",
    cancelButtonColor: "#d33",
    confirmButtonText: "Sí, liberar",
  }).then((result) => {
    if (result.isConfirmed) {
      const index = listaDeBoletas.value.findIndex(
        (b) => b.numero === numeroSeleccionado.value
      );
      if (index !== -1) {
        listaDeBoletas.value[index].estado = 0; // Estado 0 = disponible
        listaDeBoletas.value[index].comprador = null;
      }

      // Eliminar de registros
      listaDeRegistros.value = listaDeRegistros.value.filter(
        (r) => r.boleta !== numeroSeleccionado.value
      );

      actualizarTotales(); // Actualiza los totales

      Swal.fire(
        "¡Liberada!",
        "La boleta está disponible nuevamente.",
        "success"
      );
      const modal = bootstrap.Modal.getInstance(
        document.getElementById("boletaModal")
      );
      modal.hide();
    }
  });
}
function elegirGanador() {
  // 1. Verificar si ya hay ganador
  const yaHayGanador = listaDeRegistros.value.some((r) => r.estado === 3);
  if (yaHayGanador) {
    Swal.fire({
      icon: "error",
      title: "Error",
      text: "Ya existe una boleta ganadora",
      timer: 3000,
    });
    return;
  }

  // 2. Mostrar confirmación
  Swal.fire({
    title: "¿Marcar como ganadora?",
    text: `¿Confirmas que la boleta #${numeroSeleccionado.value} es la ganadora?`,
    icon: "question",
    showCancelButton: true,
    confirmButtonColor: "#3085d6",
    cancelButtonColor: "#d33",
    confirmButtonText: "Sí, marcar como ganadora",
    cancelButtonText: "Cancelar",
  }).then((result) => {
    if (result.isConfirmed) {
      // 3. Actualizar la boleta
      const index = listaDeBoletas.value.findIndex(
        (b) => b.numero === numeroSeleccionado.value
      );
      if (index !== -1) {
        listaDeBoletas.value[index].estado = 3; // 3 = Ganador
        if (listaDeBoletas.value[index].comprador) {
          listaDeBoletas.value[index].comprador.estado = 3;
        }
      }

      // 4. Actualizar registros
      const registroIndex = listaDeRegistros.value.findIndex(
        (r) => r.boleta === numeroSeleccionado.value
      );
      if (registroIndex !== -1) {
        listaDeRegistros.value[registroIndex].estado = 3;
        listaDeRegistros.value[registroIndex].estadoTexto = "Ganador";
      }

      // 5. Forzar reactividad
      listaDeBoletas.value = [...listaDeBoletas.value];
      listaDeRegistros.value = [...listaDeRegistros.value];

      // 6. Cerrar modal
      const modal = bootstrap.Modal.getInstance(
        document.getElementById("boletaModal")
      );
      if (modal) modal.hide();

      // 7. Mostrar confirmación
      Swal.fire({
        icon: "success",
        title: "¡Éxito!",
        text: `La boleta #${numeroSeleccionado.value} ha sido marcada como ganadora`,
        timer: 2000,
      });
    }
  });
}

// Función para seleccionar una boleta
function seleccionarBoleta(boleta) {
  numeroSeleccionado.value = boleta.numero;
  estadoSeleccionado.value = boleta.estado;
  indiceSeleccionado.value = listaDeBoletas.value.findIndex(
    (b) => b.numero === boleta.numero
  );

  // Si la boleta tiene comprador, cargar sus datos
  if (boleta.comprador) {
    nombreParticipante.value = boleta.comprador.nombre;
    direccionParticipante.value = boleta.comprador.direccion;
    telefonoParticipante.value = boleta.comprador.telefono;
    fechaCompraParticipante.value = boleta.comprador.fecha;
    estadoParticipante.value = boleta.comprador.estado;
  } else {
    // Limpiar datos si no hay comprador
    nombreParticipante.value = "";
    direccionParticipante.value = "";
    telefonoParticipante.value = "";
    fechaCompraParticipante.value = "";
    estadoParticipante.value = null;
  }
}

// Computed para el color de las boletas según estado
const colorBoleta = computed(() => {
  return (estado) => {
    switch (estado) {
      case 0:
        return "#4ac487"; // Disponible
      case 1:
        return "#CF1635"; // Apartado
      case 2:
        return "#18b9bfff"; // Pagado
      case 3:
        return "#BA8C34"; // Ganador
      default:
        return "#4ac487";
    }
  };
});
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap");

* {
  font-family: "Poppins", sans-serif;
  box-sizing: border-box;
  transition: all 0.3s ease-in-out;
}

body {
  min-height: 100vh;
  margin: 0;
  overflow: hidden; 
}

.contenedorPrincipal {
  max-width: 100vw;
  overflow-x: hidden;
  background: linear-gradient(180deg, #e0f7fa, #def7e0);
  animation: fadeIn 1s ease-in-out;
}

.encabezado {
  text-align: center;
  margin-bottom: 30px;
  border-bottom-left-radius: 30px;
  border-bottom-right-radius: 30px;
}

.titulo {
  font-size: 48px;
  color: #1b5e20;
  margin-bottom: 10px;
  font-weight: 700;
}

.frase {
  font-size: 18px;
  color: #388e3c;
}

.bienvenida .alert {
  max-width: 500px;
  margin: 0 auto 20px;
  font-size: 16px;
  border-radius: 12px;
}



.modal-content {
  border-radius: 16px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
  border: none;
}

.modal-header {
  background: #a5d6a7;
  border-bottom: none;
  border-radius: 16px 16px 0 0;
  justify-content: center;
}

.modal-title {
  color: #1b5e20;
  font-weight: 600;
}

input.form-control,
select.form-control {
  padding: 12px;
  border-radius: 12px;
  border: 1px solid #cfd8dc;
  font-size: 16px;
  margin-bottom: 10px;
}

input.form-control:focus,
select.form-control:focus {
  border-color: #66bb6a;
  box-shadow: 0 0 0 0.15rem rgba(102, 187, 106, 0.3);
}

.botonGuardar {
  background-color: #43a047;
  color: white;
  font-weight: 600;
  padding: 10px 24px;
  border-radius: 8px;
}

.botonGuardar:hover {
  background-color: #388e3c;
}

.contenedor {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 50px;
  padding: 20px;
  margin-top: 80px;
}

.contenedor-boton {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 15px;
}

.informacion,
.acciones {
  background: #e7e7e7;
  width: 380px;
  height: 450px;
  border-radius: 20px;
  padding: 15px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.05);
  transition: transform 0.3s ease;
}

.informacion:hover,
.acciones:hover {
  transform: translateY(-5px);
}

.titulo-info {
  text-align: center;
  font-size: 20px;
  font-weight: 600;
  color: #2e7d32;
  margin-bottom: 16px;
}

.cuerpo-acciones {
  background-color: #f5f5f5;
  border-radius: 16px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 15px;
  justify-content: center;
  align-content: center;
}

.cuerpo-accion {
  background-color: #f5f5f5;
  border-radius: 16px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 15px;
  justify-content: center;
  height: 85%;
}

.talonario-info {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.info-talonario {
  background: #e8f5e9;
  color: #1b5e20;
  padding: 12px;
  border-radius: 12px;
  text-align: center;
  font-weight: 600;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.08);
}

.btn-editar {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 10px;
  cursor: pointer;
  font-weight: 500;
}

.btn-editar:hover {
  background-color: #2e7d32;
}

.icono-animado {
  display: inline-block;
  transition: transform 0.3s ease;
}

.btn-editar:hover .icono-animado {
  transform: translateY(-2px);
  transform: scale(1.2);
}

.btn-acciones:hover .icono-animado {
  transform: translateY(-2px);
  transform: scale(1.2);
}

.btn-acciones {
  background-color: #26a69a;
  color: white;
  border: none;
  padding: 12px;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  box-shadow: 0 3px 8px rgba(0, 150, 136, 0.3);
}

.btn-acciones:hover {
  background-color: #00796b;
}

.boleta-grid {
  display: flex; 
  flex-wrap: wrap; 
  justify-content: center; 
  gap: 10px; 
  padding: 20px;
}

.espacio-placeholder {
  grid-column: span 10;
  text-align: center;
  padding: 40px 0;
  color: #555;
  font-weight: 500;
  font-size: 16px;
}

.spinner {
  border: 4px solid #eee;
  border-top: 4px solid #1e88e5;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  margin: 0 auto 10px;
  animation: spin 1s linear infinite;
}

.footer {
  width: 100%;
  margin: 0;
  padding: 0;
  left: 0;
  right: 0;
  border-top-left-radius: 30px;
  border-top-right-radius: 30px;
}

@keyframes fadeIn {
  0% {
    opacity: 0;
    transform: translateY(20px);
  }

  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

@media (max-width: 768px) {
  .contenedor {
    flex-direction: column;
    align-items: center;
  }

  .informacion,
  .acciones {
    width: 90%;
    height: auto;
  }

  .cuerpo-accion {
    height: auto;
  }
}

@media (max-width: 360px) {
  .contenedor {
    padding: 10px;
  }

  .boleta-grid {
    padding: 10px;
    gap: 6px;
  }
}


</style>
