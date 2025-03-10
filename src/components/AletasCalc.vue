<template>
  <div class="container">
    <h1>Análise de Eficiência de Aletas Retangulares Retas</h1>
    
    <div class="input-group">
      <div class="input-container">
        <label for="h">Coeficiente de Convecção (h) [W/m²K]:</label>
        <input type="number" id="h" v-model.number="h" 
               min="0.1" step="0.1" required>
        <span class="input-error" v-if="errors.h">{{ errors.h }}</span>
      </div>
      
      <div class="input-container">
        <label for="k">Condutividade Térmica (k) [W/mK]:</label>
        <input type="number" id="k" v-model.number="k" 
               min="0.02" step="0.1" required>
        <span class="input-error" v-if="errors.k">{{ errors.k }}</span>
      </div>

      <div class="input-container">
        <label for="t">Espessura da Aleta (t) [m]:</label>
        <input type="number" id="t" v-model.number="t" 
               min="0.001" step="0.001" required>
        <span class="input-error" v-if="errors.t">{{ errors.t }}</span>
      </div>

      <div class="input-container">
        <label for="L">Comprimento da Aleta (L) [m]:</label>
        <input type="number" id="L" v-model.number="L" 
               min="0.01" step="0.01" required>
        <span class="input-error" v-if="errors.L">{{ errors.L }}</span>
      </div>
    </div>

    <div v-if="isValid" class="results">
      <h2>Resultados:</h2>
      <div class="parameters">
        <p>Parâmetro m: {{ m.toFixed(4) }} m⁻¹</p>
        <p>Comprimento corrigido Lc: {{ Lc.toFixed(4) }} m</p>
      </div>
      <p>Eficiência da aleta: {{ efficiency.toFixed(4) }}</p>
      <div id="plot-container"></div>
    </div>

    <div v-else class="error">
      Preencha todos os campos com valores válidos
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watchEffect, nextTick, onUnmounted } from 'vue';
import * as Plotly from 'plotly.js-dist-min';

// Aki ficam as reatividades, que sao os parâmetros iniciais pro usuario colocar
const h = ref('');
const k = ref('');
const t = ref('');
const L = ref('');
const errors = ref({ h: '', k: '', t: '', L: '' });
const plotInstance = ref(null);

// Aki as validaçoes dos parametros
const isValid = computed(() => {
  const numericValues = [h, k, t, L].every(v => 
    !isNaN(Number(v.value)) && Number(v.value) > 0
  );
  
  // Pesquisei valores usuais para os parametros e colocados aki
  return numericValues && 
    Number(h.value) >= 0.1 && Number(h.value) <= 10000 &&
    Number(k.value) >= 0.02 && Number(k.value) <= 500 &&
    Number(t.value) >= 0.001 && Number(t.value) <= 0.1 &&
    Number(L.value) >= 0.01 && Number(L.value) <= 10;
});

// Aki ficam parametros das equaçoes pro calculo e determinar a eficiencia da aleta
const Lc = computed(() => {
  return isValid.value ? Number(L.value) + (Number(t.value) / 2) : 0;
});

const m = computed(() => {
  if (!isValid.value) return 0;
  const denominator = Number(k.value) * Number(t.value);
  return denominator !== 0 ? Math.sqrt((2 * Number(h.value)) / denominator) : 0;
});

const efficiency = computed(() => {
  if (!isValid.value) return 0;
  const mLc = m.value * Lc.value;
  return mLc !== 0 ? Math.tanh(mLc) / mLc : 0; // importante pra evitar divisoes por 0 !!
});

// Pra plotagem do grafico
const updatePlot = async () => {
  await nextTick();
  
  if (!isValid.value || !document.getElementById('plot-container')) {
    if (plotInstance.value) {
      Plotly.purge('plot-container');
      plotInstance.value = null;
    }
    return;
  }

  try {
    const xValues = Array.from({length: 100}, (_, i) => 
      0.01 + (Number(L.value) - 0.01) * (i/99)
    );

    const yValues = xValues.map(l => {
      const currentLc = l + Number(t.value)/2;
      const currentM = Math.sqrt((2 * Number(h.value)) / (Number(k.value) * Number(t.value)));
      return currentM * currentLc !== 0 ? 
        Math.tanh(currentM * currentLc) / (currentM * currentLc) : 
        0;
    });

    const trace = {
      x: xValues,
      y: yValues,
      mode: 'lines',
      name: 'Eficiência',
      line: { color: '#2196F3' }
    };

    const currentPoint = {
      x: [Number(L.value)],
      y: [efficiency.value],
      mode: 'markers',
      name: 'Ponto Atual',
      marker: { color: '#FF5722', size: 12 }
    };

    const layout = {
  title: {
    text: 'Eficiência vs Comprimento',
    font: {
      size: 18
    }
  },
  xaxis: {
    title: {
      text: 'Comprimento (m)'
    },
    range: [0, Number(L.value)]
  },
  yaxis: {
    title: {
      text: 'Eficiência'
    },
    range: [0, 1.1]
  },
  showlegend: true,
  margin: {
    l: 50,
    r: 20,
    b: 50,
    t: 80,
    pad: 4
  },
  font: {
    size: window.innerWidth < 768 ? 10 : 12
  }
};

    if (plotInstance.value) {
      Plotly.react('plot-container', [trace, currentPoint], layout);
    } else {
      plotInstance.value = await Plotly.newPlot('plot-container', [trace, currentPoint], layout);
    }
  } catch (error) {
    console.error('Erro no Plotly:', error);
  }
};

// Os observers e lifecycle (observadores e ciclo de vida)
watchEffect(() => {
  if (isValid.value) updatePlot();
});

onUnmounted(() => {
  if (plotInstance.value) {
    Plotly.purge('plot-container');
  }
});
</script>

<style>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

h1 {
  font-size: 2rem;
  text-align: center;
  margin-bottom: 1.5rem;
  width: 100%;
}

.input-group {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
  margin-bottom: 2rem;
  width: 100%;
  justify-items: center;
}

.input-container {
  display: flex;
  flex-direction: column;
  position: relative;
  width: 100%;
  max-width: 300px;
}

input {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
  width: 100%;
}

.input-error {
  color: #dc3545;
  font-size: 0.875rem;
  margin-top: 4px;
  position: absolute;
  bottom: -20px;
  left: 0;
  right: 0;
  text-align: center;
}

.results {
  margin-top: 2rem;
  padding: 1rem;
  background: #f8f9fa;
  border-radius: 8px;
  width: 100%;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
}

#plot-container {
  width: 100%;
  max-width: 600px;
  height: 400px;
  margin: 1rem auto 0;
}

.error {
  color: #dc3545;
  padding: 1rem;
  border: 1px solid #dc3545;
  border-radius: 4px;
  margin: 1rem auto 0;
  text-align: center;
  width: fit-content;
  max-width: 90%;
}

@media (max-width: 768px) {
  .input-group {
    grid-template-columns: 1fr;
  }

  #plot-container {
    max-width: 100%;
  }
}

@media (max-width: 480px) {
  .input-group {
    grid-template-columns: 1fr;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 2rem;
    margin-bottom: 2rem;
    width: 100%;
    justify-items: center;
    align-items: center;
    }
}
</style>