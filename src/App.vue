<template>
  <div id="app">
    <!-- NAVBAR -->
    <nav class="navbar">
      <div class="nav-container">
        <h1 class="nav-title">Convertidor de Divisas</h1>
        <div class="nav-info">
          <span>Tasas en tiempo real</span>
        </div>
      </div>
    </nav>

    <!-- CONTENIDO PRINCIPAL -->
    <main class="main-content">
      <div class="converter-layout">

        <!-- IZQUIERDA: Formularios -->
        <section class="left-panel">
          <div class="converter-box">
            <h2 class="box-title">Convertir moneda</h2>

            <!-- Monto -->
            <div class="input-group">
              <label>Cantidad</label>
              <input
                v-model.number="amount"
                type="number"
                placeholder="Ingresa la cantidad"
                min="0"
                @keyup.enter="convertCurrency"
              />
            </div>

            <!-- SELECTS en una fila (o apilados en móvil) -->
            <div class="row">
              <div class="input-group small">
                <label>De</label>
                <select v-model="fromCurrency" @change="convertCurrency">
                  <option value="USD">USD - Dólar Estadounidense</option>
                  <option value="EUR">EUR - Euro</option>
                  <option value="ARS">ARS - Peso Argentino</option>
                  <option value="GBP">GBP - Libra Esterlina</option>
                  <option value="BRL">BRL - Real Brasileño</option>
                  <option value="MXN">MXN - Peso Mexicano</option>
                  <option value="CLP">CLP - Peso Chileno</option>
                  <option value="JPY">JPY - Yen Japonés</option>
                </select>
              </div>

              <div class="swap-container" aria-hidden="true">
                <button @click="swapCurrencies" class="swap-btn" title="Intercambiar monedas">⇄</button>
              </div>

              <div class="input-group small">
                <label>A</label>
                <select v-model="toCurrency" @change="convertCurrency">
                  <option value="USD">USD - Dólar Estadounidense</option>
                  <option value="EUR">EUR - Euro</option>
                  <option value="ARS">ARS - Peso Argentino</option>
                  <option value="GBP">GBP - Libra Esterlina</option>
                  <option value="BRL">BRL - Real Brasileño</option>
                  <option value="MXN">MXN - Peso Mexicano</option>
                  <option value="CLP">CLP - Peso Chileno</option>
                  <option value="JPY">JPY - Yen Japonés</option>
                </select>
              </div>
            </div>

            <!-- Botón convertir -->
            <button @click="convertCurrency" class="convert-btn" :disabled="loading">
              <span v-if="!loading">Convertir</span>
              <span v-else>⏳ Convirtiendo...</span>
            </button>

            <!-- Mensaje de error (aparece debajo del botón) -->
            <transition name="fade">
              <div v-if="error" class="error">⚠️ {{ error }}</div>
            </transition>
          </div>

          
        </section>

        <!-- DERECHA: Resultado -->
        <aside class="right-panel">
          <transition name="fade">
            <div v-if="result !== null && !error" class="result-card">
              <div class="result-top">
                <div class="result-icon"></div>
                <div class="result-texts">
                  <p class="result-amount">{{ formatNumber(amount) }} <span class="mono">{{ fromCurrency }}</span></p>
                  <div class="result-equals">=</div>
                  <p class="result-converted">{{ formatNumber(result) }} <span class="mono">{{ toCurrency }}</span></p>
                </div>
              </div>

              <div class="result-meta">
                <p class="exchange-rate">Tasa: 1 {{ fromCurrency }} = {{ exchangeRate.toFixed(4) }} {{ toCurrency }}</p>
                <p class="last-update">Última actualización: {{ currentTime }}</p>
              </div>
            </div>
          </transition>

          <!-- Si aún no hay resultado, mostrar un placeholder tranquilo -->
          <transition name="fade">
            <div v-if="result === null && !error" class="placeholder-card">
              <p class="placeholder-text">Realizá una conversión y verás el resultado aquí.</p>
            </div>
          </transition>
        </aside>

      </div>
    </main>

    <!-- FOOTER -->
    <footer class="footer">
      <p>Tasas proporcionadas por ExchangeRate-API</p>
    </footer>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      amount: 100,
      fromCurrency: 'USD',
      toCurrency: 'ARS',
      result: null,
      exchangeRate: 0,
      loading: false,
      error: null,
      currentTime: '',
    }
  },
  methods: {
    async convertCurrency() {
      // Valida que el monto sea válido
      if (!this.amount || this.amount <= 0) {
        this.error = 'Por favor ingresa una cantidad válida mayor a 0'
        this.result = null
        return
      }

      // Si las monedas son iguales
      if (this.fromCurrency === this.toCurrency) {
        this.result = this.amount
        this.exchangeRate = 1
        this.error = null
        this.updateTime()
        return
      }

      try {
        this.loading = true
        this.error = null

        // Llamada a la API de tasas de cambio
        const response = await fetch(
          `https://api.exchangerate-api.com/v4/latest/${this.fromCurrency}`,
        )

        if (!response.ok) {
          throw new Error('Error al obtener las tasas de cambio')
        }

        const data = await response.json()
        this.exchangeRate = data.rates[this.toCurrency]
        this.result = this.amount * this.exchangeRate
        this.updateTime()
      } catch (err) {
        this.error = 'Error al conectar con el servicio. Verifica tu conexión.'
        this.result = null
        console.error(err)
      } finally {
        this.loading = false
      }
    },

    // Intercambiar monedas
    swapCurrencies() {
      const temp = this.fromCurrency
      this.fromCurrency = this.toCurrency
      this.toCurrency = temp
      if (this.result !== null) {
        this.convertCurrency()
      }
    },

    // Formatear números con separadores de miles
    formatNumber(num) {
      return new Intl.NumberFormat('es-AR', {
        minimumFractionDigits: 2,
        maximumFractionDigits: 2,
      }).format(num)
    },

    // Actualizar hora
    updateTime() {
      const now = new Date()
      this.currentTime = now.toLocaleTimeString('es-AR')
    },
  },

  // Convertir automáticamente al cargar
  mounted() {
    this.convertCurrency()
  },
}
</script>

<style scoped>
/* Estética tranqui, neutra y ordenada */

/* Root & layout */
#app {
  font-family: "Inter", "Segoe UI", Tahoma, sans-serif;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background: #fafafa;
  color: #1f2937;
}

/* Navbar */
.navbar {
  background: #ffffff;
  border-bottom: 1px solid #ececec;
  padding: 16px 0;
  position: sticky;
  top: 0;
  z-index: 50;
}
.nav-container {
  max-width: 1100px;
  margin: 0 auto;
  padding: 0 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.nav-title {
  font-size: 1.25rem;
  font-weight: 700;
  color: #0f172a;
}
.nav-info {
  color: #6b7280;
  font-size: 0.9rem;
}

/* Main container */
.main-content {
  flex: 1;
  display: flex;
  justify-content: center;
  padding: 36px 18px;
}

/* Layout: izquierda (form) / derecha (resultado) */
.converter-layout {
  display: grid;
  grid-template-columns: 1fr;
  gap: 20px;
  width: 100%;
  max-width: 1000px;
}

/* Para pantallas grandes, dos columnas */
@media (min-width: 900px) {
  .converter-layout {
    grid-template-columns: 1fr 380px;
    align-items: start;
  }
}

.left-panel { display: flex; flex-direction: column; gap: 16px; }
.right-panel { display: flex; flex-direction: column; gap: 16px; align-items: stretch; }

/* Form card */
.converter-box {
  background: #ffffff;
  border-radius: 12px;
  padding: 22px;
  box-shadow: 0 6px 18px rgba(16,24,40,0.06);
  border: 1px solid #eef2f7;
}
.box-title {
  margin: 0 0 14px;
  font-size: 1.15rem;
  font-weight: 700;
  color: #0f172a;
}

/* Inputs */
.input-group { margin-bottom: 12px; }
.input-group.small { margin-bottom: 0; }

label {
  display: block;
  margin-bottom: 6px;
  color: #374151;
  font-weight: 600;
  font-size: 0.9rem;
}

input, select {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  background: #ffffff;
  font-size: 0.95rem;
  color: #0f172a;
  transition: all 0.15s ease;
}
input::placeholder { color: #9ca3af; }
input:focus, select:focus {
  outline: none;
  border-color: #7c93ff;
  box-shadow: 0 0 0 4px rgba(124,147,255,0.08);
}

/* Row for selects + swap */
.row {
  display: flex;
  gap: 12px;
  align-items: center;
}
.row .small { flex: 1; }

/* Swap button */
.swap-container { display:flex; align-items:center; justify-content:center; }
.swap-btn {
  width: 44px;
  height: 44px;
  border-radius: 8px;
  border: 1px solid #c7d2fe;
  background: #ffffff;
  color: #374151;
  cursor: pointer;
  font-size: 18px;
  transition: all 0.18s;
}
.swap-btn:hover { transform: translateY(-2px); box-shadow: 0 6px 12px rgba(99,102,241,0.08); }

/* Convert button */
.convert-btn {
  width: 100%;
  padding: 12px;
  margin-top: 12px;
  background: linear-gradient(90deg,#4f46e5,#7c93ff);
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: 700;
  font-size: 0.98rem;
  cursor: pointer;
  transition: transform .18s, box-shadow .18s;
}
.convert-btn:hover:not(:disabled) { transform: translateY(-3px); box-shadow: 0 10px 20px rgba(79,70,229,0.15); }
.convert-btn:disabled { opacity: 0.7; cursor: not-allowed; }

/* Info box */
.info-box {
  background: #ffffff;
  padding: 12px 14px;
  border-radius: 10px;
  border: 1px solid #eef2f7;
  color: #475569;
  font-size: 0.95rem;
}

/* Result card / placeholder */
.result-card, .placeholder-card {
  background: #ffffff;
  border-radius: 12px;
  padding: 18px;
  box-shadow: 0 6px 18px rgba(16,24,40,0.04);
  border: 1px solid #eef2f7;
}
.result-top {
  display: flex;
  gap: 12px;
  align-items: center;
}
.result-icon {
  font-size: 2.4rem;
  line-height: 1;
}
.result-texts { display:flex; flex-direction:column; gap:6px; }
.result-amount, .result-converted {
  font-size: 1.2rem;
  font-weight: 700;
  color: #0f172a;
}
.result-equals {
  font-size: 1.4rem;
  color: #7c93ff;
  font-weight: 700;
}
.result-meta { margin-top: 10px; color: #6b7280; font-size: 0.9rem; }

.placeholder-text {
  color: #6b7280;
  text-align: center;
  padding: 24px 6px;
}

/* Error */
.error {
  margin-top: 12px;
  padding: 10px;
  background: #fff5f5;
  border: 1px solid #fecaca;
  color: #991b1b;
  border-radius: 8px;
  font-weight: 600;
  text-align: center;
}

/* Footer */
.footer {
  background: transparent;
  padding: 18px;
  text-align: center;
  color: #6b7280;
  font-size: 0.9rem;
  border-top: 1px solid #f1f5f9;
}

/* Transitions */
.fade-enter-active, .fade-leave-active { transition: opacity .35s, transform .35s; }
.fade-enter-from, .fade-leave-to { opacity: 0; transform: translateY(6px); }

/* Small screens adjustments */
@media (max-width: 420px) {
  .nav-title { font-size: 1.05rem; }
  .box-title { font-size: 1.05rem; }
  .swap-btn { width:40px; height:40px; }
}
</style>
