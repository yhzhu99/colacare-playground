<script setup>
import { onMounted, ref, watch } from 'vue'
import * as echarts from 'echarts'

const chartContainer = ref(null)
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
watch(activeIndicators, (_newVal) => {
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
          result += `${param.marker + param.seriesName}: ${param.value} ${indicator?.unit}<br/>`
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

const evaluationQuestions = [
  {
    id: 1,
    title: 'Clinical Expert Alignment',
    description: 'Rate how well the AI system\'s analysis aligns with expert clinical judgment:',
    options: [
      { value: 1, label: 'Poor alignment with expert clinical reasoning' },
      { value: 2, label: 'Limited alignment with clinical expertise' },
      { value: 3, label: 'Moderate alignment with expert judgment' },
      { value: 4, label: 'Good alignment with clinical expert assessment' },
      { value: 5, label: 'Excellent alignment with expert clinical decision-making' },
    ],
  },
  {
    id: 2,
    title: 'Feature Identification Accuracy',
    description: 'Evaluate the relevance of identified risk factors:',
    options: [
      { value: 1, label: 'Irrelevant feature identification' },
      { value: 2, label: 'Partially relevant features identified' },
      { value: 3, label: 'Moderately relevant feature selection' },
      { value: 4, label: 'Highly relevant feature identification' },
      { value: 5, label: 'Optimal clinical feature selection' },
    ],
  },
  {
    id: 3,
    title: 'Clinical Decision Support Value',
    description: 'Rate the overall clinical utility of the AI analysis:',
    options: [
      { value: 1, label: 'Minimal clinical value' },
      { value: 2, label: 'Limited clinical value' },
      { value: 3, label: 'Moderate clinical value' },
      { value: 4, label: 'High clinical value' },
      { value: 5, label: 'Exceptional clinical value' },
    ],
  },
]

const selectedScores = ref({
  1: null,
  2: null,
  3: null,
})

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
                v-for="indicator in indicators" :key="indicator.id" class="indicator-btn"
                :class="[{ active: activeIndicators.includes(indicator.id) }]" @click="toggleIndicator(indicator.id)"
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
              <div v-for="question in evaluationQuestions" :key="question.id" class="evaluation-question">
                <h4>{{ question.id }}) {{ question.title }}</h4>
                <p class="question-description">
                  {{ question.description }}
                </p>

                <div class="score-options">
                  <label
                    v-for="option in question.options" :key="option.value" class="score-option"
                    :class="{ selected: selectedScores[question.id] === option.value }"
                  >
                    <input
                      v-model="selectedScores[question.id]" type="radio" :name="`question-${question.id}`"
                      :value="option.value"
                    >
                    <span class="score-label">Score: {{ option.value }}</span>
                    <span class="score-description">{{ option.label }}</span>
                  </label>
                </div>
              </div>
            </div>

            <button class="submit-btn">
              Submit Evaluation
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.medical-dashboard {
  margin: 0 auto;
  padding: 20px;
  display: grid;
  gap: 4px;
  grid-template-columns: 450px 1fr; /* Increased from 380px */
  max-width: 2200px; /* Add this line to set maximum width */
  width: 100%;
}

.container {
  min-height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  max-width: 2200px; /* Add this line */
  margin: 0 auto; /* Add this line */
}

.left-panel, .right-panel {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 0 10px;
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
  height: 330px; /* Increased from 400px */
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

.evaluation-question {
  margin-bottom: 24px;
  padding: 20px;
  background: #f8f9fa;
  border-radius: 8px;
  text-align: left;
}

.evaluation-question h4 {
  color: #1976d2;
  font-size: 16px;
  margin-bottom: 12px;
  font-weight: 600;
}

.question-description {
  color: #424242;
  font-size: 14px;
  margin-bottom: 16px;
  line-height: 1.5;
}

.score-options {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.score-option {
  display: flex;
  align-items: center;
  padding: 14px;
  background: white;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s ease;
  margin-bottom: 8px;
}

.score-option:hover {
  background: #f5f5f5;
  border-color: #bbdefb;
}

.score-option.selected {
  background: #e3f2fd;
  border-color: #2196f3;
}

.score-label {
  font-weight: 600;
  margin-right: 16px;
  min-width: 80px;
  color: #1976d2;
}

.score-description {
  color: #616161;
  font-size: 14px;
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
  align-items: flex-start; font-size: 14px;
  padding: 8px;
  background: #f8f9fa;
  border-radius: 6px;
  cursor: pointer;
}

.options label:hover {
  background: #e9ecef;
}

.submit-btn {
  margin-top: 24px;
  padding: 14px 28px;
  background: #2196f3;
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: 600;
  font-size: 16px;
  transition: all 0.3s ease;
  width: 100%;
  cursor: pointer;
  box-shadow: 0 2px 4px rgba(33, 150, 243, 0.3);
}

.submit-btn:hover {
  background: #1976d2;
  transform: translateY(-1px);
  box-shadow: 0 4px 8px rgba(33, 150, 243, 0.4);
}

.submit-btn:active {
  transform: translateY(0);
  box-shadow: 0 2px 4px rgba(33, 150, 243, 0.3);
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

@media (max-width: 2000px) { /* Increased from 1400px */
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
