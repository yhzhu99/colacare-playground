<script setup>
import { onMounted, ref, watch } from 'vue'
import * as echarts from 'echarts'

const chartContainer = ref(null)
const activeIndicators = ref(['albumin']) // Default to showing albumin
let chart = null

// Timeline data and indicators remain the same
const timelineData = {
  cl: [99.0, 99.0, 100.0, 102.0, 102.0, 102.0, 98.0, 101.0, 99.9, 100.0, 102.0, 102.0, 100.0, 98.0, 92.0, 96.0, 101.0, 101.0, 93.0, 96.0, 101.0, 97.0, 101.0, 97.0, 102.0, 100.0, 99.0, 100.0, 99.0, 96.0, 98.0],
  co2cp: [22.9, 22.9, 25.6, 24.5, 24.5, 26.3, 22.5, 22.2, 24.0, 22.6, 24.3, 24.3, 21.7, 26.4, 26.6, 29.0, 26.7, 26.7, 27.7, 26.2, 25.9, 26.8, 22.6, 23.2, 24.3, 23.1, 25.1, 26.3, 25.0, 20.4, 18.7],
  wbc: [8.2, 8.4, 8.67, 7.7, 7.3, 7.3, 7.71, 8.64, 8.75, 6.76, 7.5, 6.54, 8.4, 8.21, 11.3, 9.5, 8.9, 8.9, 8.02, 8.17, 8.06, 8.06, 7.73, 7.73, 7.54, 7.32, 8.23, 6.66, 8.04, 9.6, 8.6],
  hb: [91.0, 103.0, 117.0, 137.0, 161.0, 90.0, 118.0, 109.0, 123.0, 118.0, 121.0, 119.0, 109.0, 115.0, 130.0, 125.0, 118.0, 118.0, 116.0, 111.0, 118.0, 118.0, 122.0, 122.0, 125.0, 119.0, 129.0, 118.0, 113.0, 115.0, 133.0],
  urea: [24.0, 24.0, 22.1, 24.2, 24.0, 27.8, 27.8, 30.5, 30.5, 31.6, 24.5, 24.5, 24.5, 28.5, 15.8, 20.3, 26.3, 30.9, 21.4, 31.8, 28.3, 15.7, 22.8, 26.1, 25.5, 26.5, 22.9, 21.9, 26.3, 19.6, 15.2],
  ca: [2.43, 2.43, 2.47, 2.52, 2.5, 2.38, 2.38, 2.32, 2.32, 2.34, 2.36, 2.36, 2.36, 2.46, 2.41, 2.41, 2.46, 2.29, 2.53, 2.54, 2.49, 2.42, 2.37, 2.53, 2.56, 2.51, 2.77, 2.52, 2.63, 2.61, 2.43],
  k: [3.74, 3.74, 3.75, 3.67, 3.55, 4.41, 4.41, 4.13, 4.13, 3.61, 3.72, 3.72, 3.72, 3.62, 3.74, 4.17, 4.63, 4.63, 4.33, 3.72, 3.86, 3.71, 3.53, 3.65, 4.55, 4.33, 3.78, 3.85, 4.55, 3.58, 3.11],
  na: [141.5, 141.5, 139.2, 140.3, 139.6, 141.0, 141.0, 141.0, 141.0, 142.0, 143.0, 143.0, 143.0, 136.0, 132.0, 138.0, 139.0, 139.0, 132.0, 138.0, 140.0, 139.0, 140.0, 137.0, 142.0, 140.0, 141.0, 140.0, 140.0, 137.0, 136.0],
  scr: [1114.0, 1114.0, 1189.0, 1084.0, 1087.0, 1009.0, 1009.0, 1104.0, 1104.0, 1181.0, 1031.0, 1031.0, 1031.0, 961.0, 841.0, 991.0, 954.0, 916.0, 892.0, 912.0, 852.0, 913.0, 822.0, 833.0, 795.0, 838.0, 881.0, 850.0, 776.0, 778.0, 768.0],
  p: [2.33, 2.33, 1.75, 1.61, 2.21, 1.88, 1.88, 2.34, 2.34, 2.01, 2.17, 2.17, 2.17, 2.2, 1.51, 1.9, 1.53, 2.03, 1.76, 1.91, 1.55, 1.63, 2.05, 1.33, 1.88, 1.81, 1.96, 1.99, 1.9, 1.86, 1.42],
  albumin: [41.0, 41.0, 40.0, 41.0, 40.0, 38.0, 38.0, 39.0, 39.0, 37.0, 37.0, 37.0, 37.0, 37.0, 35.5, 37.6, 38.3, 38.3, 35.3, 35.6, 36.0, 36.0, 35.4, 34.5, 35.6, 34.3, 35.2, 33.0, 35.4, 31.8, 30.2],
  crp: [0.07, 0.07, 0.08, 0.08, 3.81, 3.81, 2.25, 8.66, 7.43, 2.83, 5.11, 5.11, 20.25, 16.8, 15.39, 12.5, 11.44, 11.44, 9.24, 9.24, 2.83, 2.83, 13.57, 13.57, 13.57, 13.57, 2.05, 1.76, 1.76, 12.93, 7.17],
  glucose: [8.6, 8.6, 5.6, 6.3, 6.0, 7.3, 7.3, 8.1, 8.1, 5.6, 8.2, 8.2, 8.2, 11.3, 11.3, 9.3, 7.8, 7.8, 6.7, 6.7, 8.6, 8.6, 8.6, 8.6, 8.6, 9.4, 11.5, 8.9, 11.4, 16.8, 14.7],
  appetite: [2073.78, 2778.0, 2173.24, 1019.5, 1019.5, 1019.5, 3278.07, 1355.89, 2834.46, 2834.46, 2834.46, 2907.1, 2907.1, 2907.1, 2474.7, 2474.7, 2474.7, 2474.7, 2474.7, 3495.67, 3495.67, 3495.67, 3495.67, 3495.67, 3495.67, 3495.67, 3495.67, 3495.67, 3495.67, 3495.67, 3495.67],
  weight: [68.55, 68.09, 67.78, 67.15, 66.8, 66.34, 65.36, 64.97, 64.23, 64.05, 63.6, 63.14, 62.68, 62.2, 65.0, 65.0, 63.0, 63.15, 68.0, 67.22, 66.42, 65.71, 64.0, 64.31, 66.0, 64.81, 61.0, 61.0, 64.0, 64.0, 64.0],
  sbp: [130.0, 160.0, 130.0, 145.0, 145.0, 154.0, 158.0, 135.0, 130.0, 140.0, 150.0, 150.0, 150.0, 150.0, 135.0, 163.0, 130.0, 175.0, 140.0, 140.0, 140.0, 145.0, 140.0, 140.0, 140.0, 140.0, 135.0, 135.0, 137.0, 130.0, 130.0],
  dbp: [80.0, 90.0, 70.0, 80.0, 80.0, 80.0, 80.0, 90.0, 75.0, 90.0, 80.0, 80.0, 85.0, 95.0, 80.0, 106.0, 85.0, 105.0, 83.0, 83.0, 90.0, 90.0, 80.0, 80.0, 90.0, 87.0, 80.0, 84.0, 80.0, 80.0, 85.0],
}

const indicators = [
  { id: 'cl', name: 'Chlorine', unit: 'mmol/L', value: '98.0' },
  { id: 'co2cp', name: 'CO2CP', unit: 'mmol/L', value: '18.7' },
  { id: 'wbc', name: 'WBC', unit: '×10^9/L', value: '8.6' },
  { id: 'hb', name: 'HGB', unit: 'g/L', value: '133.0' },
  { id: 'urea', name: 'Urea', unit: 'mmol/L', value: '15.2' },
  { id: 'ca', name: 'Calcium', unit: 'mmol/L', value: '2.43' },
  { id: 'k', name: 'Potassium', unit: 'mmol/L', value: '3.11' },
  { id: 'na', name: 'Sodium', unit: 'mmol/L', value: '136.0' },
  { id: 'scr', name: 'SCR', unit: 'μmol/L', value: '768.0' },
  { id: 'p', name: 'PHOS', unit: 'mmol/L', value: '1.42' },
  { id: 'albumin', name: 'Albumin', unit: 'g/L', value: '30.2' },
  { id: 'crp', name: 'hs-CRP', unit: 'mg/L', value: '7.17' },
  { id: 'glucose', name: 'Glucose', unit: 'mmol/L', value: '14.7' },
  { id: 'appetite', name: 'Food intake', unit: 'g', value: '3495.67' },
  { id: 'weight', name: 'Weight', unit: 'kg', value: '64.0' },
  { id: 'sbp', name: 'Systolic pressure', unit: 'mmHg', value: '130.0' },
  { id: 'dbp', name: 'Diastolic pressure', unit: 'mmHg', value: '85.0' },
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
      data: ['2007-07', '2007-10', '2007-12', '2008-04', '2008-07', '2008-10', '2009-04', '2009-07', '2009-11', '2010-01', '2010-04', '2010-07', '2010-10', '2011-01', '2011-08', '2011-12', '2012-03', '2012-04', '2012-06', '2012-08', '2012-10', '2012-12', '2013-02', '2013-03', '2013-08', '2013-10', '2014-03', '2014-06', '2014-10', '2015-01', '2015-03'],
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
                  Chronic glomerulonephritis
                </div>
              </div>
              <div class="patient-tag">
                ID: a1
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
                  46 years
                </div>
              </div>
              <div class="demo-item">
                <div class="label">
                  Height
                </div>
                <div class="value">
                  159.4 cm
                </div>
              </div>
              <div class="demo-item">
                <div class="label">
                  Weight
                </div>
                <div class="value">
                  68.5 kg
                </div>
              </div>
              <!-- <div class="demo-item">
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
                  Diabetes
                </div>
              </div> -->
            </div>
          </div>

          <!-- AI Generated Patient Report -->
          <div class="report-card">
            <h3>AI Generated Patient Report</h3>
            <p class="report-content">
              The patient, a 46-year-old female with End-Stage Renal Disease (ESRD) due to chronic glomerulonephritis,
              exhibits significant biochemical abnormalities including metabolic acidosis, hypoalbuminemia, and
              hypokalemia. These conditions indicate severe nutritional deficits, electrolyte imbalances, and potential
              cardiovascular risks. Despite increased food intake, the patient's nutritional status remains compromised,
              suggesting poor nutrient absorption or increased metabolic demands. Additionally, the patient's blood
              pressure, while within the higher reference range, suggests ongoing cardiovascular stress. These factors
              collectively contribute to a precarious condition with significant risks related to electrolyte
              imbalances, nutritional status, and cardiovascular health, all of which are critical in ESRD management.
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
                {{ indicator.name }}
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
                <p><strong>Date of Death:</strong> 2015-05</p>
                <p><strong>Cause of Death:</strong> Sudden cardiac death</p>
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
  grid-template-columns: 450px 1fr;
  /* Increased from 380px */
  max-width: 2200px;
  /* Add this line to set maximum width */
  width: 100%;
}

.container {
  min-height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  max-width: 2200px;
  /* Add this line */
  margin: 0 auto;
  /* Add this line */
}

.left-panel,
.right-panel {
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
  height: 330px;
  /* Increased from 400px */
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

@media (max-width: 2000px) {

  /* Increased from 1400px */
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
