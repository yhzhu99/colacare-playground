<script setup>
import { onMounted, ref, watch } from 'vue'
import * as echarts from 'echarts'

const chartContainer = ref(null)
const selectedPrediction = ref('')
const selectedFeatureAssessment = ref('')
const activeIndicators = ref(['albumin']) // Default to showing albumin
let chart = null

// Timeline data and indicators remain the same
const timelineData = {
  albumin: [35.0, 32.0, 30.0, 28.0, 25.0, 23.0, 22.3],
  phos: [1.30, 1.15, 1.35, 1.28, 1.25, 1.10, 0.93],
  co2cp: [28.5, 29.2, 30.1, 30.8, 31.2, 31.5, 31.8],
  wbc: [8.5, 9.2, 10.5, 12.1, 13.4, 14.0, 14.72],
  glucose: [15.2, 16.8, 18.1, 19.5, 20.2, 21.4, 22.1],
  urea: [6.8, 7.2, 7.8, 8.2, 8.7, 9.0, 9.3],
  crp: [45.2, 58.4, 72.6, 85.3, 98.7, 108.5, 117.01],
  potassium: [4.2, 3.9, 3.7, 3.5, 3.3, 3.2, 3.09],
}

const indicators = [
  { id: 'albumin', name: 'Albumin', value: '22.3', unit: 'g/L' },
  { id: 'phos', name: 'Phosphate', value: '0.93', unit: 'mmol/L' },
  { id: 'co2cp', name: 'CO2CP', value: '31.8', unit: 'mmol/L' },
  { id: 'wbc', name: 'WBC', value: '14.72', unit: '10^9/L' },
  { id: 'glucose', name: 'Glucose', value: '22.1', unit: 'mmol/L' },
  { id: 'urea', name: 'Urea', value: '9.3', unit: 'mmol/L' },
  { id: 'crp', name: 'hs-CRP', value: '117.01', unit: 'mg/L' },
  { id: 'potassium', name: 'Potassium', value: '3.09', unit: 'mmol/L' },
]

// Updated toggle function
function toggleIndicator(id) {
  const index = activeIndicators.value.indexOf(id)
  if (index === -1)
    activeIndicators.value.push(id)
  else
    activeIndicators.value.splice(index, 1)

  updateChart() // Immediately update chart when toggling
}

// Watch for changes in activeIndicators
watch(activeIndicators, (newVal) => {
  if (chart)
    updateChart()
}, { deep: true })

// Updated chart initialization and update logic
function initChart() {
  if (chart)
    chart.dispose()

  chart = echarts.init(chartContainer.value)
  updateChart()

  // Handle resize
  const handleResize = () => {
    if (chart)
      chart.resize()
  }

  window.addEventListener('resize', handleResize)

  // Clean up event listener when component is unmounted
  onUnmounted(() => {
    window.removeEventListener('resize', handleResize)
    if (chart)
      chart.dispose()
  })
}

function updateChart() {
  if (!chart)
    return

  // Only create series for active indicators
  const series = activeIndicators.value.map((id) => {
    const indicator = indicators.find(i => i.id === id)
    return {
      name: indicator?.name,
      data: timelineData[id],
      type: 'line',
      smooth: true,
      symbol: 'circle',
      symbolSize: 8,
      lineStyle: {
        width: 3,
      },
    }
  })

  const option = {
    title: {
      text: 'Laboratory Test Timeline',
      left: 'center',
    },
    tooltip: {
      trigger: 'axis',
      formatter(params) {
        let result = `${params[0].axisValue}<br/>`
        params.forEach((param) => {
          const indicator = indicators.find(i => i.name === param.seriesName)
          result += `${param.marker + param.seriesName}: ${
                   param.value} ${indicator?.unit}<br/>`
        })
        return result
      },
    },
    legend: {
      data: activeIndicators.value.map(id =>
        indicators.find(i => i.id === id)?.name,
      ),
      top: 25,
      selected: activeIndicators.value.reduce((acc, id) => {
        const name = indicators.find(i => i.id === id)?.name
        acc[name] = true
        return acc
      }, {}),
    },
    grid: {
      left: '5%',
      right: '5%',
      bottom: '10%',
      top: '15%',
      containLabel: true,
    },
    xAxis: {
      type: 'category',
      data: ['2018.04', '2018.10', '2019.04', '2019.10', '2020.04', '2020.10', '2021.04'],
      name: 'Time',
      nameLocation: 'middle',
      nameGap: 30,
      axisLabel: {
        rotate: 45,
      },
    },
    yAxis: {
      type: 'value',
      name: 'Value',
      nameLocation: 'middle',
      nameGap: 50,
      scale: true,
    },
    series,
  }

  chart.setOption(option, true) // Use true to clear previous options completely
}

const aiPredictionOptions = [
  { value: 'A', label: 'A. AI failed to correctly assess the patient\'s condition throughout the follow-up sequence' },
  { value: 'B', label: 'B. AI made false-positive predictions when the patient was still in good condition' },
  { value: 'C', label: 'C. AI failed to respond when patient was at risk, making predictions too late' },
  { value: 'D', label: 'D. AI made relatively correct judgments in most visits' },
  { value: 'E', label: 'E. AI maintained highly reasonable and accurate predictive performance throughout' },
]

const featureIdentificationOptions = [
  { value: 'A', label: 'A. AI completely failed to identify predictive features in every visit' },
  { value: 'B', label: 'B. AI reasonably identified features indicating good prognosis during healthy periods' },
  { value: 'C', label: 'C. AI reasonably identified warning features during risk periods' },
  { value: 'D', label: 'D. AI reasonably discovered outcome-indicative features for most visits' },
]

// Mount chart
onMounted(() => {
  initChart()
})

// Clean up chart when component is unmounted
onUnmounted(() => {
  if (chart) {
    chart.dispose()
    chart = null
  }
})

onMounted(() => {
  chart = echarts.init(chartContainer.value)
  updateChart()

  window.addEventListener('resize', () => {
    chart.resize()
  })
})
</script>

<template>
  <div h-full>
    <div class="mx-auto px-4 py-8 container">
      <div class="medical-dashboard">
        <div class="left-panel">
          <!-- Patient Demographics Card -->
          <div class="demographics-card">
            <div class="card-header">
              <div class="title-container">
                <h2 class="disease-title">
                  Original Disease:
                </h2>
                <div class="disease-name">
                  Diabetic Nephropathy
                </div>
              </div>
              <div class="patient-tag">
                ID: a7
              </div>
            </div>

            <div class="demographics-grid">
              <div class="demo-item">
                <div class="label">
                  Gender
                </div>
                <div class="value">
                  Female
                </div>
              </div>
              <div class="demo-item">
                <div class="label">
                  Age
                </div>
                <div class="value">
                  75 years
                </div>
              </div>
              <div class="demo-item">
                <div class="label">
                  Height
                </div>
                <div class="value">
                  154.9 cm
                </div>
              </div>
              <div class="demo-item">
                <div class="label">
                  Weight
                </div>
                <div class="value">
                  58.0 kg
                </div>
              </div>
              <div class="demo-item">
                <div class="label">
                  BMI
                </div>
                <div class="value">
                  24.2
                </div>
              </div>
              <div class="demo-item">
                <div class="label">
                  Comorbidities
                </div>
                <div class="value">
                  Diabetes, Heart Disease
                </div>
              </div>
            </div>
          </div>

          <!-- AI Generated Patient Report -->
          <div class="report-card">
            <h3>AI Generated Patient Report</h3>
            <p class="report-content">
              75-year-old female patient with a history of Diabetic Nephropathy as the primary condition.
              Patient exhibits multiple comorbidities including diabetes and heart disease. Recent laboratory
              indicators show concerning trends with declining albumin levels (22.3 g/L) and elevated WBC
              (14.72 10^9/L), suggesting potential inflammatory processes. Patient's condition deteriorated
              progressively, leading to death in April 2012 due to Peripheral vascular disease.
            </p>
          </div>

          <!-- Lab Indicators -->
          <div class="indicators-card">
            <h3>Laboratory Indicators</h3>
            <div class="indicators-grid">
              <button
                v-for="indicator in indicators"
                :key="indicator.id"
                class="indicator-btn" :class="[{ active: activeIndicators.includes(indicator.id) }]"
                @click="toggleIndicator(indicator.id)"
              >
                {{ indicator.name }} ({{ indicator.value }} {{ indicator.unit }})
              </button>
            </div>
          </div>
        </div>

        <div class="right-panel">
          <!-- Lab Test Timeline -->
          <div class="timeline-card">
            <h3>Test Results Timeline</h3>
            <div ref="chartContainer" class="chart-container" />
          </div>

          <!-- Risk Assessment -->
          <div class="assessment-card">
            <h3>ColaCare Evaluation Questionnaire</h3>

            <div class="outcome-info">
              <h4>Patient Outcome</h4>
              <div class="outcome-details">
                <p><strong>Date of Death:</strong> 2012-04</p>
                <p><strong>Cause of Death:</strong> Peripheral vascular disease</p>
              </div>
            </div>

            <div class="questionnaire">
              <div class="question">
                <h4>1. AI Model's Death Risk Prediction Assessment:</h4>
                <div class="options">
                  <label v-for="(option, index) in aiPredictionOptions" :key="index">
                    <input
                      v-model="selectedPrediction"
                      type="radio"
                      :value="option.value"
                    >
                    <span>{{ option.label }}</span>
                  </label>
                </div>
              </div>

              <div class="question">
                <h4>2. Reasonability of AI's Key Feature Identification:</h4>
                <div class="options">
                  <label v-for="(option, index) in featureIdentificationOptions" :key="index">
                    <input
                      v-model="selectedFeatureAssessment"
                      type="radio"
                      :value="option.value"
                    >
                    <span>{{ option.label }}</span>
                  </label>
                </div>
              </div>
            </div>

            <button class="submit-btn" @click="submitColaCareEvaluation">
              Submit ColaCare Evaluation
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  min-height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.medical-dashboard {
  max-width: 1400px;
  margin: 0 auto;
  padding: 20px;
  display: grid;
  gap: 20px;
  grid-template-columns: 380px 1fr;
}

.left-panel, .right-panel {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.demographics-card,
.timeline-card,
.indicators-card,
.assessment-card {
  background: white;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.disease-title {
  font-size: 20px;
  color: #2c3e50;
  margin: 0;
}

.patient-tag {
  background: #e3f2fd;
  padding: 6px 12px;
  border-radius: 16px;
  color: #1976d2;
  font-weight: 500;
}

.demographics-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
}

.demo-item {
  padding: 8px;
  background: #f8f9fa;
  border-radius: 8px;
}

.label {
  color: #6c757d;
  font-size: 12px;
  margin-bottom: 4px;
}

.value {
  font-size: 14px;
  font-weight: 500;
  color: #2c3e50;
}

.chart-container {
  height: 400px;
  width: 100%;
  margin: 20px 0;
}

.indicators-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 8px;
}

.indicator-btn {
  padding: 8px 12px;
  border-radius: 20px;
  border: 1px solid #e0e0e0;
  background: white;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 13px;
  text-align: left;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.indicator-btn.active {
  background: #1976d2;
  color: white;
  border-color: #1976d2;
}

.risk-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.risk-options {
  display: grid;
  gap: 8px;
}

.features-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 8px;
}

.risk-option,
.feature-option {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 6px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 13px;
}

.submit-btn {
  width: 100%;
  padding: 12px;
  background: #1976d2;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
  transition: background 0.3s;
  margin-top: 20px;
}

.title-container {
  display: flex;
  align-items: center;
  gap: 10px;
}

.disease-name {
  color: #1976d2;
  font-weight: 600;
  font-size: 20px;
}

.report-card {
  background: white;
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
  margin-bottom: 20px;
}

.report-content {
  line-height: 1.6;
  color: #2c3e50;
  font-size: 14px;
  text-align: left;
  padding: 10px 0;
}

.outcome-info {
  background: #f8f9fa;
  padding: 15px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.outcome-details {
  font-size: 14px;
  color: #2c3e50;
}

.questionnaire .question {
  margin-bottom: 20px;
}

.options {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.options label {
  display: flex;
  gap: 10px;
  align-items: flex-start;
  font-size: 14px;
  padding: 8px;
  background: #f8f9fa;
  border-radius: 6px;
  cursor: pointer;
}

.options label:hover {
  background: #e9ecef;
}

.submit-btn:hover {
  background: #1565c0;
}

h3 {
  color: #2c3e50;
  margin-bottom: 16px;
  font-size: 18px;
}

h4 {
  color: #2c3e50;
  margin-bottom: 12px;
  font-size: 14px;
}

@media (max-width: 1024px) {
  .medical-dashboard {
    grid-template-columns: 1fr;
  }

  .risk-container {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .demographics-grid,
  .indicators-grid,
  .features-grid {
    grid-template-columns: 1fr;
  }
}
</style>
