<template>
  <div class="empresas-container">
    <h2>Lista de Empresas</h2>

    <form @submit.prevent="agregarEmpresa" class="empresa-form">
      <div class="form-group">
        <label for="cod_empresa">Código Empresa:</label>
        <input v-model="nuevaEmpresa.cod_empresa" required />
      </div>

      <div class="form-group">
        <label for="ruc">RUC:</label>
        <input v-model="nuevaEmpresa.ruc" required maxlength="20"/>
      </div>

      <div class="form-group">
        <label for="razon_social">Razón Social:</label>
        <input v-model="nuevaEmpresa.razon_social" required />
      </div>

      <div class="form-group">
        <label for="direccion">Dirección:</label>
        <input v-model="nuevaEmpresa.direccion" required />
      </div>

      <div class="form-group">
        <label for="activo">Activo:</label>
        <input type="checkbox" v-model="nuevaEmpresa.activo" />
      </div>

      <button type="submit">Agregar Empresa</button>
    </form>

    <table class="empresa-table">
      <thead>
        <tr>
          <th>Código Empresa</th>
          <th>RUC</th>
          <th>Razón Social</th>
          <th>Dirección</th>
          <th>Activo</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(empresa, index) in empresas" :key="index">
          <td>{{ empresa.cod_empresa }}</td>
          <td>
            <span v-if="empresaEditando !== empresa">{{ empresa.ruc }}</span>
            <input v-else v-model="empresaEditando.ruc" />
          </td>
          <td>
            <span v-if="empresaEditando !== empresa">{{ empresa.razon_social }}</span>
            <input v-else v-model="empresaEditando.razon_social" />
          </td>
          <td>
            <span v-if="empresaEditando !== empresa">{{ empresa.direccion }}</span>
            <input v-else v-model="empresaEditando.direccion" />
          </td>
          <td>
            <span v-if="empresaEditando !== empresa">{{ empresa.activo ? 'Sí' : 'No' }}</span>
            <input v-else type="checkbox" v-model="empresaEditando.activo" />
          </td>
          <td>
            <button v-if="empresaEditando !== empresa" @click="editarEmpresa(empresa)">Editar</button>
            <button v-else @click="guardarEdicion(empresa)">Guardar</button>
            <button @click="eliminarEmpresa(index)">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'ListaEmpresas',
  data() {
    return {
      nuevaEmpresa: {
        cod_empresa: '',
        ruc: '',
        razon_social: '',
        direccion: '',
        activo: true,
      },
      empresas: [],
      empresaEditando: null,
    };
  },
  created() {
    this.obtenerEmpresas();
  },
  methods: {
    async obtenerEmpresas() {
      try {
        const response = await axios.get('https://localhost:7118/api/Empresas');
        this.empresas = response.data;
      } catch (error) {
        console.error('Error al obtener empresas:', error);
      }
    },
    async agregarEmpresa() {
      try {
        const response = await axios.post('https://localhost:7118/api/Empresas', this.nuevaEmpresa);
        this.empresas.push(response.data);
        this.nuevaEmpresa = { cod_empresa: '', ruc: '', razon_social: '', direccion: '', activo: true };
      } catch (error) {
        if (error.response && error.response.status === 400 && error.response.data && error.response.data.cod_empresa) {
          // Manejar el caso específico de error de código de empresa duplicado
          alert('Error: El código de empresa ya existe.');
        } else {
          console.error('Error al agregar empresa:', error);
        }
      }
    },
    editarEmpresa(empresa) {
      this.empresaEditando = empresa;
    },
    async guardarEdicion(empresa) {
      try {
        await axios.put(`https://localhost:7118/api/Empresas/${empresa.cod_empresa}`, empresa);
        this.empresaEditando = null;
      } catch (error) {
        console.error('Error al guardar edición:', error);
      }
    },
    async eliminarEmpresa(index) {
      try {
        const empresa = this.empresas[index];
        await axios.delete(`https://localhost:7118/api/Empresas/${empresa.cod_empresa}`);
        this.empresas.splice(index, 1);
      } catch (error) {
        console.error('Error al eliminar empresa:', error);
      }
    },
  },
};
</script>

<style scoped>
  .empresas-container {
    max-width: 800px;
    margin: auto;
  }


  .form-group {
    margin-bottom: 10px;
  }

  .empresa-table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }

  .empresa-table th, .empresa-table td {
    border: 1px solid #ddd;
    padding: 8px;
    text-align: left;
  }

  .empresa-table th {
    background-color: #f2f2f2;
  }

  button {
    cursor: pointer;
    padding: 8px 12px;
    margin-right: 5px;
  }

  .empresa-form {
    background-color: #f5f5f5;
    border-radius: 8px;
    padding: 20px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 20px;
  }

  .form-group {
    margin-bottom: 15px;
  }

  .form-group label {
    display: block;
    font-weight: bold;
    margin-bottom: 5px;
  }

  .form-group input {
    width: 100%;
    padding: 8px;
    box-sizing: border-box;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  .form-group input[type="checkbox"] {
    width: auto;
  }

  button {
    cursor: pointer;
    padding: 10px 15px;
    background-color: #4caf50;
    color: #fff;
    border: none;
    border-radius: 4px;
    transition: background-color 0.3s;
  }

  button:hover {
    background-color: #45a049;
  }
</style>
