# Rebuttal

Dear Program Chairs, Senior Area Chairs, Area Chairs, and Reviewers,

We sincerely thank all reviewers for their positive recognition of our work and their insightful, constructive feedback.

We thank Reviewers #vxpv and #JUfE for recognizing the paper's clear and well-written presentation. We appreciate Reviewers #vxpv, #mvV9, and #JUfE for acknowledging the promising direction and originality of our LLM-based approach in healthcare. We thank Reviewers #mvV9, #GWg2, and #JUfE for recognizing the effectiveness of our **well-designed agent-based architecture**, particularly the collaboration between DoctorAgents and MetaAgent for robust decision-making. We thank Reviewers #vxpv and #mvV9 for highlighting our work's **potential impact on healthcare professionals and clinical decision-making**. Furthermore, we appreciate Reviewers #KcGx, #GWg2 and #JUfE's recognition of our **comprehensive evaluation** across multiple real-world datasets (MIMIC-IV, CDSL, and ESRD), which demonstrated consistently better performance over baseline models.

We have thoroughly addressed reviewer concerns across several key aspects:

1. Multi-agent collaboration effectiveness and coordination
2. Comprehensive experiments with additional LLMs and SOTA baselines
3. Systematic human evaluation with experienced medical practitioners
4. Detailed ablation studies validating our design choices.

For reviewers' convenience, we have made complete code (https://colacare.netlify.app/code), available questionnaires (https://colacare.netlify.app/questionnaire), case studies (https://colacare.netlify.app/case), prompt designs (https://colacare.netlify.app/prompt), and our full rebuttal (https://colacare.netlify.app/rebuttal) at our anonymous website: https://colacare.netlify.app.

ColaCare represents a significant advancement in clinical decision support by bridging the gap between structured EHR analysis and clinical reasoning. Our framework's demonstrated effectiveness, interpretability, and alignment with expert clinical judgment position it to meaningfully impact healthcare delivery and patient outcomes. We are committed to further developing and validating ColaCare to ensure its practical value in clinical settings.

Best regards,
Authors of Paper #542

## Response to Reviewer #vxpv

We appreciate your recognition of our research direction and your insights highlighting valuable areas for future development. Additional rebuttal details, including **human evaluation questionnaires**, are available on our anonymized website (https://colacare.netlify.app/questionnaire).

### Q1 (Trade-off between Cost and Performance)

> can the authors comment on whether the cost of the LLMs justifies the slightly improved evaluation results over ensemble models?

While LLM-driven approaches incur higher computational costs than conventional ensemble methods, the cost remains practical. Our pipeline processes each patient in 151.90 seconds using the DeepSeek model, costing approximately $0.013 USD per patient (Tab. 6, main text).

In healthcare, even marginal performance improvements can significantly impact clinical outcomes by reducing misdiagnosis rates, decreasing unnecessary treatments, enhancing early warning capabilities, and preventing medical errors. Historical studies estimate the marginal cost per death deferred at $29,000 (year 1990) for an average-efficient hospital [1], making ColaCare highly cost-effective.

Furthermore, ColaCare's simulated MDT process and interpretable evidence generation serve as cost-effective tools for medical education and clinical practice by incorporating expert clinical knowledge and reasoning patterns.

[1] *The Trade-off Between Hospital Cost and Quality of Care: An Exploratory Empirical Analysis, 1992, Medical Care*

### Q2 (Novel Ensemble Methods)

> are there possible more novel/state-of-the-art ensemble models that could be compared to?

Thanks to your suggestion, we implemented and benchmarked three more advanced ensemble models: a) temperature scaling, b) deep ensemble, and c) MC-Dropout. ColaCare demonstrates better performance on MIMIC-IV Outcome, achieving AUPRC scores relative 0.91% higher than a), 1.75% higher than b), and 2.46% higher than c). ColaCare's integration of LLMs and expert models enables indirect knowledge injection through ensemble learning, enhancing system stability and interpretability.

|             Methods            |                    |  MIMIC-IV Outcome  |                          |                    | MIMIC-IV Readmission |                           |                    |    CDSL Outcome   |                           |                    |    ESRD Outcome    |                               |
|:------------------------------:|:------------------:|:------------------:|:------------------------:|:--------------------:|:------------------:|:------------------------:|:------------------:|:------------------:|:------------------------:|:------------------:|:------------------:|:------------------------:|
|                                | AUPRC | AUROC | min(+P, Se) |  AUPRC  | AUROC | min(+P, Se) | AUPRC | AUROC | min(+P, Se) | AUPRC | AUROC | min(+P, Se) |
| AdaCare                        |     52.67±4.50     |     87.56±1.37     |        53.62±3.91        |      50.51±4.24      |     78.82±1.94     |        50.01±3.37        |     82.76±3.74     |     95.54±1.02     |        77.20±3.23        |     60.44±6.94     |     69.11±5.53     |        59.23±6.46        |
| ConCare                        |     49.71±4.83     |     87.21±1.41     |        52.96±3.74        |      46.66±4.05      |     79.06±1.82     |        47.00±3.20        |     80.77±3.59     |     94.47±1.24     |        74.17±2.80        |     56.65±6.91     |     68.84±4.85     |        58.47±5.78        |
| RETAIN                         |     51.89±4.22     |     87.87±1.27     |        49.71±3.83        |      50.89±3.90      |     80.73±1.82     |        50.42±3.33        |     74.46±4.13     |     93.32±1.30     |        70.09±3.30        |     60.02±6.32     |     70.82±4.32     |        59.26±5.41        |
| MeanEnsemble |     53.59±4.52     |     88.60±1.26     |        54.08±3.66        |      50.48±4.68      |     86.97±1.45     |        50.36±3.58        |     83.54±3.36     |     95.63±1.04     |        76.24±3.48        |     60.90±7.08     |     73.41±4.80     |        59.98±6.02        |
| WeightedMeanEnsemble |     53.44±4.51     |     88.58±1.26     |        53.98±3.72        |      50.64±4.69      |     87.05±1.45     |        50.68±3.63        |     84.08±3.34     |     95.88±0.98     |        76.48±3.42        |     61.07±7.11     |     73.49±4.81     |        60.09±5.99        |
| TemperatureEnsemble |     53.56±4.52     |     88.57±1.26     |        54.07±3.69        |      51.11±4.00      |     80.38±1.80     |        **52.70±3.30**        |     83.86±3.32     |     95.72±1.02     |        76.39±3.43        |     61.47±6.41     |     73.36±3.70     |        59.00±5.19        |
| DeepEnsemble |     53.12±4.69     |     88.21±1.31     |        53.95±3.57        |      51.54±4.05      |     80.83±1.80     |        52.47±3.27        |     84.58±3.27     |     95.90±0.97     |        77.01±3.38        |     62.61±6.05     |     **73.76±3.76**     |        60.47±5.29        |
| MC-Dropout                     |     52.75±4.93     |     87.96±2.22     |        53.58±3.89        |      50.54±4.66      |     81.57±2.60     |        49.42±3.93        |     83.86±3.63     |     95.04±1.66     |        76.02±3.89        |     61.53±6.90     |     71.61±4.76     |        60.34±5.43        |
| ColaCare                       |     **54.05±4.88**     |     **88.80±1.31**     |        **54.44±3.80**        |      **51.91±4.99**      |     **87.72±1.37**     |        50.16±4.17        |     **85.76±2.99**     |     **96.23±0.91**     |        **78.93±3.21**        |     **63.81±6.43**     |     71.61±4.77     |       **63.33±5.54**        |

Our ColaCare has consistent performance improvements across diverse datasets and tasks.

### Q3 (True Clinical Evaluation)

> is there any possibility of a true clinical evaluation, i.e. observing how ColaCare performs in a clinical setting as part of a care loop?

We employed a systematic human evaluation using peritoneal dialysis patient data. We engaged 12 experienced medical practitioners (5-15 years of experience) from nephrology departments across 5 hospitals. Practitioners rated agreement levels (1-5) for each patient case. ColaCare achieved a mean score of 4.4 vs. ConCare's 3.2, demonstrating strong alignment with expert clinical judgment. Blinded evaluations confirm that ColaCare's interpretability closely matches human expert practice patterns. (Check https://colacare.netlify.app/questionnaire)

## Response to Reviewer #KcGx

Thank you for your thoughtful review and recognition of ColaCare's contributions! We appreciate your suggestions and address each of your questions below:

### Q1-Q2 (Multi-Agent Collaboration)

> 1. How does this method ensure the effectiveness and coordination of multi-agent collaboration?

> 2. How do DoctorAgents and MetaAgent in the organizational structure collaborate to analyze patient data and make decisions?

ColaCare ensures effective multi-agent coordination through a structured consultation process detailed in Section 4.2 and Fig. 3. The DoctorAgents and MetaAgent collaborate in an iterative workflow where: 1) DoctorAgents provide initial patient reviews backed by EHR data and medical guidelines, 2) the MetaAgent synthesizes these reviews into a comprehensive report, 3) DoctorAgents evaluate and debate the report with evidence-based arguments, and 4) the MetaAgent refines the report based on feedback until consensus is reached. Section 6.5's case study demonstrates how this process leads to refined diagnoses through constructive debate and knowledge integration.

### Q3 (LLM Integration)

> How are Large Language Models mentioned in the text used for structured electronic health record modeling? How is their integration with domain-specific expert models achieved?

The paper details the LLM integration in Section 4.3's Multimodal Fusion Network. We combine domain-specific expert models' EHR representations with LLM-encoded medical reports through a concatenation-based fusion approach. This architecture leverages both structured EHR analysis capabilities of expert models and the reasoning capabilities of LLMs. The ablation study in Section 6.2 validates this design, showing significant performance drops when either component is removed.

### Q4-Q6 (Article Organization and Presentation)

> 4. The organization of the article is a bit confusing, especially in Part Four, which can be clarified by moving specific prompts for LLMs to the appendix.

> 5. The titles of the images in the article need brief descriptions to provide context.

> 6. The font size in the tables is too small, making it difficult to read.

Thank you for these suggestions to improve readability. We will:
- Move specific LLM prompts to an appendix while maintaining core methodology discussion in the main text
- Add descriptive captions to all figures
- Increase font sizes in tables for better readability

### Q7 (Expand the Background)

> The article needs to include some additional background information to help readers better understand the objectives of the research.

We will better contextualize our research objectives within existing literature: bridging the gap between structured EHR data and clinical reasoning through a framework that combines domain-specific expert models with LLMs in a MDT inspired collaborative approach, ultimately providing both improved predictive performance and interpretable clinical decision support.

## Response to Reviewer #mvV9

We appreciate your thorough evaluation and constructive feedback!

### C1 (Workflow Design)

> The workflow developed in the paper relies heavily on manual design, making it difficult to extend to other domains. Creating a workflow to prompt LLMs for a specific task, especially in the context of research papers within the already well-explored LLM community, seems more like an engineering practice rather than a genuine research problem.

ColaCare's manual prompt design focuses on medical accuracy and safety. The core architecture: multi-agent collaboration & RAG integration remains domain-agnostic and adaptable to other fields by modifying domain-specific components. This adaptability could inspire researchers in other domains to bridge the capability gap between domain-specific expert models and LLMs.

### C2 (Clinical Tasks)

> The downstream task addressed in the manuscript is essentially a binary classification problem. The proposed approach is somewhat similar to performing data augmentation for a binary classification neural network. Using multiple LLM-based Agents for such a task might be an overkill, especially since the improvement shown in Table 2 is not particularly significant.

ColaCare's significance lies in its design of combining structured EHR data analysis with LLM-driven reasoning. While performance improvements are modest numerically, they significantly impact patient outcomes in clinical settings. ColaCare additionally provides evidence-based reasoning for predictions, enabling cost-effective clinical decision support.

### Q1 (Novel EHR-specific Baselines)

> In the experimental section, the manuscript compares its results with an EHR-specific baseline, the most recent of which dates back to 2020. Considering the rapid advancements in this field, particularly with the popularity of deep learning, it would be beneficial to include more up-to-date baselines for comparison.

The baseline models (ConCare, AdaCare, RETAIN) were selected for their established credibility (100+ citations for ConCare/AdaCare, 1500+ for RETAIN) and reproducibility. We have compared latest SOTA models and would be happy to include more.

|Methods||MIMIC-IV Outcome|||MIMIC-IV Readmission|||CDSL Outcome|||ESRD Outcome||
|:------------------------------:|:------------------:|:------------------:|:------------------------:|:------------------:|:--------------------:|:------------------------:|:------------------:|:------------------:|:------------------------:|:------------------:|:------------------:|:------------------------:|
||AUPRC|AUROC|min(+P, Se)|AUPRC|AUROC|min(+P, Se)|AUPRC|AUROC|min(+P, Se)|AUPRC|AUROC|min(+P, Se)|
|M3Care|52.77±4.57|87.45±1.28|53.26±3.79|50.62±4.30|80.14±1.84|51.59±3.46|76.17±3.96|93.91±1.19|70.69±3.64|56.52±6.86|71.97±4.12|54.81±5.36|
|AICare|50.20±4.58|87.66±1.41|48.77±3.74|49.51±4.13|80.35±1.84|49.87±3.39|83.69±3.08|94.19±0.98|76.78±2.97|62.37±6.26|75.35±3.86|59.23±5.42|
|PRISM|52.23±4.22|87.68±1.23|53.01±3.59|50.95±4.05|80.38±1.93|52.01±3.41|84.28±3.14|95.16±0.87|78.05±2.86|62.13±5.84|73.11±3.87|54.16±4.93|
|MeanEnsemble|54.54±4.45|88.80±1.25|54.19±3.77|51.13±4.18|80.90±1.84|50.75±3.43|86.04±3.19|96.71±0.83|79.39±2.91|63.40±6.20|76.90±3.62|63.48±4.70|
|WeightedMeanEnsemble|54.61±4.45|88.82±1.24|54.22±3.81|51.12±4.18|80.89±1.84|50.79±3.43|86.19±3.17|96.73±0.83|79.38±2.98|63.16±6.18|**77.15±3.61**|**64.05±4.75**|
|TemperatureEnsemble|54.78±4.45|88.83±1.24|54.47±3.85|51.22±4.18|80.89±1.85|51.07±3.37|85.65±3.19|96.57±0.85|78.79±2.82|64.74±6.45|74.41±3.71|60.89±4.71|
|DeepEnsemble|54.43±4.55|88.41±1.27|54.22±3.77|51.09±4.15|80.67±1.85|50.76±3.40|86.78±2.92|96.83±0.80|79.12±2.88|65.50±6.18|73.96±3.61|63.49±4.59|
|MC-Dropout|54.62±4.49|88.10±1.69|54.77±4.00|51.02±4.65|83.75±2.05|50.62±3.99|86.29±4.25|96.58±1.46|78.36±3.41|65.62±5.74|74.29±4.27|63.23±5.37|
|EMERGE|53.08±4.65|88.09±1.26|53.94±3.73|**51.35±3.89**|82.27±1.88|51.44±3.37|81.56±4.34|95.92±1.53|46.03±19.15|56.45±6.80|75.14±3.47|57.26±4.96|
|ColaCare|**55.24±4.60**|**89.40±1.20**|**54.99±3.66**|50.68±4.08|**84.22±1.82**|**52.51±3.42**|**87.55±3.28**|**97.62±0.98**|**80.68±2.99**|**68.62±6.23**|74.51±4.08|62.78±5.15|

We employed M3Care, AICare, and PRISM as ColaCare's expert models (and compared them individually). Results validate ColaCare's effectiveness in enhancing performance through DoctorAgents, MetaAgent collaboration, debate, and LLM reasoning. Furthermore, we compared the latest EMERGE model (CIKM-2024) that incorporates external knowledge graphs and uses RAG, demonstrating that ColaCare performs better.

- (KDD, 2022) M3Care: Learning with Missing Modalities in Multimodal Healthcare Data
- (Cell Patterns, 2023) AICare: Mortality prediction with adaptive feature importance recalibration for peritoneal dialysis patients
- (CIKM, 2024) PRISM: Mitigating EHR Data Sparsity via Learning from Missing Feature Calibrated Prototype Patient Representations
- (CIKM, 2024) EMERGE: Enhancing Multimodal Electronic Health Records Predictive Modeling with Retrieval-Augmented Generation

### Q2 (Documents Integration with EHR-specific Methods)

> This manuscript retrieves relevant domain-specific documents. Could integrating these documents into existing EHR-specific methods lead to further improvements in their performance?

ColaCare builds upon this idea by not only integrating domain-specific documents but also leveraging LLMs' reasoning capabilities to analyze and discuss these documents in a collaborative setting. This approach provides richer context and more nuanced interpretation than simple document integration.

|Methods||MIMIC-IV Outcome||| MIMIC-IV Readmission ||
|:------------------:|:------------------:|:------------------:|:------------------------:|:------------------:|:--------------------:|:------------------------:|
||AUPRC|AUROC|min(+P, Se)|AUPRC|AUROC|min(+P, Se)|
|w/o RAG| 51.57±4.76 | 88.09±1.30 |49.25±3.91| 48.00±4.58 |86.74±1.63|**51.43±3.85**|
|w/o Fusion Network| 42.61±4.02 | 87.53±1.27 |49.72±3.69| 49.03±4.38 |79.85±1.90|51.26±3.30|
|w/o Expert Models| 27.73±3.38 | 74.38±2.19 |27.80±3.31| 20.15±2.46 |58.25±2.66|19.39±2.29|
|w/o MDT| 50.63±5.02 | 86.80±1.36 |51.92±4.25| 47.68±4.36 |79.12±1.82|51.39±3.34|
|ColaCare| **54.05±4.88** | **88.80±1.31** |**54.44±3.80**| **51.91±4.99** |**87.72±1.37**|50.16±4.17|

Results show ColaCare with MDT collaboration performs better compared to the document retrieval baseline and without-RAG baseline, demonstrating that retrieving relevant documents improves performance and that the MDT process in ColaCare's framework plays a crucial role.

### Q3 (Fairness in Comparison)

> The methods proposed in this manuscript uses language models trained on large-scale specialized healthcare texts as feature encoders, whereas the EHR-specific methods do not. Could this result in an unfair comparison?

We conducted extensive experiments spanned 3 datasets across 4 tasks, covering diverse diseases and data sources. All private datasets required application. Dataset privacy licenses confirmed no pre-training information leakage. Results demonstrate consistent performance improvements.

### Q4 (Related multi-agent LLM work)

> The multi-agent collaboration approach described in the paper appears somewhat similar to methods[1] previously discussed in the community. Could the authors highlight how their approach to multi-agent collaboration differs from current methods in the community, or is it an application of these methods to a specific task?

> [1] Wu et al. AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation. In ArXiv 2023.

We will include detailed AutoGen comparisons in revision. Our key distinction: direct EHR analysis by LLMs alone is suboptimal. Enabling LLMs to leverage domain-specific expert models creates optimal cross-enhancement between traditional deep learning's EHR modeling and LLMs' external knowledge injection and reasoning capabilities.

### Q5 (Alternative Validation Methods)

> The paper emphasizes its contribution by stating that it enhances transparency and provides human-understandable evidence to support physicians in their diagnostic reasoning. However, if I understand correctly, the validation of this evidence still relies on human experts, which doesn't fully address the issues of trustworthiness or transparency. Could the authors explore alternative methods to validate the evidence and strengthen their claims?

ColaCare helps align clinical features with medical knowledge semantics, enabling clinicians to connect deep learning predictions with clinical guidelines and medical literature in human-understandable way.

We employed a systematic human evaluation using peritoneal dialysis patient data. We engaged 12 experienced medical practitioners (5-15 years of experience) from nephrology departments across 5 hospitals. Practitioners rated agreement levels (1-5) for each patient case. ColaCare achieved a mean score of 4.4 vs. ConCare's 3.2, demonstrating strong alignment with expert clinical judgment. Blinded evaluations confirm that ColaCare's interpretability closely matches human expert practice patterns. (Check https://colacare.netlify.app/questionnaire)

## Response to Reviewer #GWg2

We sincerely thank you for your thorough review and constructive feedback!

### W1 (Additional LLM Models)

> The authors utilized LLMs like GPT-4o, qwen-turbo, and two others. However, incorporating additional models like LLaMA would enhance the comparisons and strengthen the results.

We have conducted experiments with following LLMs: Llama 3.1 400B and Claude 3.5 Sonnet 1022 for more comprehensive comparisons.

|Methods||MIMIC-IV Outcome|||MIMIC-IV Readmission||
|:-----------------------:|:------------------:|:------------------:|:------------------------:|:------------------:|:--------------------:|:------------------------:|
||AUPRC|AUROC|min(+P, Se)|AUPRC|AUROC|min(+P, Se)|
|GPT-4o-Mini|**55.27±4.92**|88.66±1.38|**55.74±3.75**|51.01±5.05|85.00±1.88|51.09±4.09|
|GPT-4o|54.13±4.84|88.59±1.36|54.38±3.96|50.93±4.81|86.89±1.48|49.44±3.63|
|Qwen-Turbo|52.88±4.76|88.57±1.28|54.68±3.70|49.95±4.13|80.10±1.81|50.38±3.29|
|Doubao-Pro|52.14±4.65|88.04±1.34|53.37±3.69|49.70±4.04|79.92±1.80|50.00±3.33|
|Llama-3.1 400B|54.23±4.81|88.20±1.32|52.10±3.74|51.16±4.08|85.35±1.77|**51.96±3.05**|
|Claude-3.5-sonnet|52.58±4.90|88.07±1.37|53.92±3.63|50.77±4.45|81.53±1.74|50.50±3.45|
|ColaCare (DeepSeek-V2.5)|54.05±4.88|**88.80±1.31**|54.44±3.80|**51.91±4.99**|**87.72±1.37**|50.16±4.17|

The results validate the effectiveness of our multi-agent collaborative framework is model-agnostic and can leverage various LLMs successfully.

### W2 (More Evaluation Metrics and Baselines)

> More evaluation metrics and baseline comparisons are needed, as a broader set of both adds credibility and reinforces the novelty of the approach.

We fully agree that comprehensive evaluation is crucial in healthcare data analysis. Our initial metric selection (AUPRC, AUROC, min(P,Se)) was based on widely-used EHR benchmarks (MIMIC-III/IV) and recent COVID-19 prognosis prediction benchmarks in Cell Patterns. These threshold-independent metrics are particularly valuable for clinical risk ranking. Following your suggestion, we will add precision, recall, F1-score, and accuracy metrics in the revision.

We've also expanded comparisons to include:
1) SOTA ensemble learning methods to demonstrate ColaCare's advantages in knowledge-enhanced ensemble learning.

|             Methods            |                    |  MIMIC-IV Outcome  |                          |                    | MIMIC-IV Readmission |                           |                    |    CDSL Outcome   |                           |                    |    ESRD Outcome    |                               |
|:------------------------------:|:------------------:|:------------------:|:------------------------:|:--------------------:|:------------------:|:------------------------:|:------------------:|:------------------:|:------------------------:|:------------------:|:------------------:|:------------------------:|
|                                | AUPRC | AUROC | min(+P, Se) |  AUPRC  | AUROC | min(+P, Se) | AUPRC | AUROC | min(+P, Se) | AUPRC | AUROC | min(+P, Se) |
| AdaCare                        |     52.67±4.50     |     87.56±1.37     |        53.62±3.91        |      50.51±4.24      |     78.82±1.94     |        50.01±3.37        |     82.76±3.74     |     95.54±1.02     |        77.20±3.23        |     60.44±6.94     |     69.11±5.53     |        59.23±6.46        |
| ConCare                        |     49.71±4.83     |     87.21±1.41     |        52.96±3.74        |      46.66±4.05      |     79.06±1.82     |        47.00±3.20        |     80.77±3.59     |     94.47±1.24     |        74.17±2.80        |     56.65±6.91     |     68.84±4.85     |        58.47±5.78        |
| RETAIN                         |     51.89±4.22     |     87.87±1.27     |        49.71±3.83        |      50.89±3.90      |     80.73±1.82     |        50.42±3.33        |     74.46±4.13     |     93.32±1.30     |        70.09±3.30        |     60.02±6.32     |     70.82±4.32     |        59.26±5.41        |
| MeanEnsemble        |     53.59±4.52     |     88.60±1.26     |        54.08±3.66        |      50.48±4.68      |     86.97±1.45     |        50.36±3.58        |     83.54±3.36     |     95.63±1.04     |        76.24±3.48        |     60.90±7.08     |     73.41±4.80     |        59.98±6.02        |
| WeightedMeanEnsemble |     53.44±4.51     |     88.58±1.26     |        53.98±3.72        |      50.64±4.69      |     87.05±1.45     |        50.68±3.63        |     84.08±3.34     |     95.88±0.98     |        76.48±3.42        |     61.07±7.11     |     73.49±4.81     |        60.09±5.99        |
| TemperatureEnsemble  |     53.56±4.52     |     88.57±1.26     |        54.07±3.69        |      51.11±4.00      |     80.38±1.80     |        **52.70±3.30**        |     83.86±3.32     |     95.72±1.02     |        76.39±3.43        |     61.47±6.41     |     73.36±3.70     |        59.00±5.19        |
| DeepEnsemble         |     53.12±4.69     |     88.21±1.31     |        53.95±3.57        |      51.54±4.05      |     80.83±1.80     |        52.47±3.27        |     84.58±3.27     |     95.90±0.97     |        77.01±3.38        |     62.61±6.05     |     **73.76±3.76**     |        60.47±5.29        |
| MC-Dropout                     |     52.75±4.93     |     87.96±2.22     |        53.58±3.89        |      50.54±4.66      |     81.57±2.60     |        49.42±3.93        |     83.86±3.63     |     95.04±1.66     |        76.02±3.89        |     61.53±6.90     |     71.61±4.76     |        60.34±5.43        |
| ColaCare                       |     **54.05±4.88**     |     **88.80±1.31**     |        **54.44±3.80**        |      **51.91±4.99**      |     **87.72±1.37**     |        50.16±4.17        |     **85.76±2.99**     |     **96.23±0.91**     |        **78.93±3.21**        |     **63.81±6.43**     |     71.61±4.77     |       **63.33±5.54**        |

Our ColaCare has consistent performance improvements across diverse datasets and tasks.

2) Additional EHR-specific models, showing our framework's consistent performance advantages across datasets, tasks, and metrics.

|Methods||MIMIC-IV Outcome|||MIMIC-IV Readmission|||CDSL Outcome|||ESRD Outcome||
|:------------------------------:|:------------------:|:------------------:|:------------------------:|:------------------:|:--------------------:|:------------------------:|:------------------:|:------------------:|:------------------------:|:------------------:|:------------------:|:------------------------:|
||AUPRC|AUROC|min(+P, Se)|AUPRC|AUROC|min(+P, Se)|AUPRC|AUROC|min(+P, Se)|AUPRC|AUROC|min(+P, Se)|
|M3Care|52.77±4.57|87.45±1.28|53.26±3.79|50.62±4.30|80.14±1.84|51.59±3.46|76.17±3.96|93.91±1.19|70.69±3.64|56.52±6.86|71.97±4.12|54.81±5.36|
|AICare|50.20±4.58|87.66±1.41|48.77±3.74|49.51±4.13|80.35±1.84|49.87±3.39|83.69±3.08|94.19±0.98|76.78±2.97|62.37±6.26|75.35±3.86|59.23±5.42|
|PRISM|52.23±4.22|87.68±1.23|53.01±3.59|50.95±4.05|80.38±1.93|52.01±3.41|84.28±3.14|95.16±0.87|78.05±2.86|62.13±5.84|73.11±3.87|54.16±4.93|
|MeanEnsemble|54.54±4.45|88.80±1.25|54.19±3.77|51.13±4.18|80.90±1.84|50.75±3.43|86.04±3.19|96.71±0.83|79.39±2.91|63.40±6.20|76.90±3.62|63.48±4.70|
|WeightedMeanEnsemble|54.61±4.45|88.82±1.24|54.22±3.81|51.12±4.18|80.89±1.84|50.79±3.43|86.19±3.17|96.73±0.83|79.38±2.98|63.16±6.18|**77.15±3.61**|**64.05±4.75**|
|TemperatureEnsemble|54.78±4.45|88.83±1.24|54.47±3.85|51.22±4.18|80.89±1.85|51.07±3.37|85.65±3.19|96.57±0.85|78.79±2.82|64.74±6.45|74.41±3.71|60.89±4.71|
|DeepEnsemble|54.43±4.55|88.41±1.27|54.22±3.77|51.09±4.15|80.67±1.85|50.76±3.40|86.78±2.92|96.83±0.80|79.12±2.88|65.50±6.18|73.96±3.61|63.49±4.59|
|MC-Dropout|54.62±4.49|88.10±1.69|54.77±4.00|51.02±4.65|83.75±2.05|50.62±3.99|86.29±4.25|96.58±1.46|78.36±3.41|65.62±5.74|74.29±4.27|63.23±5.37|
|EMERGE|53.08±4.65|88.09±1.26|53.94±3.73|**51.35±3.89**|82.27±1.88|51.44±3.37|81.56±4.34|95.92±1.53|46.03±19.15|56.45±6.80|75.14±3.47|57.26±4.96|
|ColaCare|**55.24±4.60**|**89.40±1.20**|**54.99±3.66**|50.68±4.08|**84.22±1.82**|**52.51±3.42**|**87.55±3.28**|**97.62±0.98**|**80.68±2.99**|**68.62±6.23**|74.51±4.08|62.78±5.15|

We employed M3Care, AICare, and PRISM as ColaCare's expert models (and compared them individually). Results validate ColaCare's effectiveness in enhancing performance through DoctorAgents, MetaAgent collaboration, debate, and LLM reasoning. Furthermore, we compared the latest EMERGE model (CIKM-2024) that incorporates external knowledge graphs and uses RAG, demonstrating that ColaCare performs better.

*EMERGE: Enhancing Multimodal Electronic Health Records Predictive Modeling with Retrieval-Augmented Generation (CIKM, 2024)*

### W3 (Human Comparison)

> Relying on LLM-based methods raises the question: how do they compare to human performance? A detailed human comparison would further validate the proposed method.

We appreciate your valuable suggestion and have conducted a systematic human evaluation study:
1. **Human evaluation results**: Our human evaluation employed a systematic assessment using peritoneal dialysis patient data. We engaged 12 experienced medical practitioners (5-15 years of experience) from nephrology departments across 5 hospitals. Practitioners rated agreement levels (1-5) for each patient case. ColaCare achieved a mean score of 4.4 vs. ConCare's 3.2, demonstrating strong alignment with expert clinical judgment. Blinded evaluations confirm that ColaCare's interpretability closely matches human expert practice patterns.  (Check https://colacare.netlify.app/questionnaire)
2. **Clinical relevance**: Through discussions with participating clinicians, we learned that physicians also rely on unquantified observations (patient appearance, mobility, etc.) in real clinical settings. This insight guides our future work in incorporating more comprehensive clinical indicators.

### W4 (Test Set Size)

> "The test set size is limited to approximately 1,000 samples due to the time cost associated with each sample. We suggest a sample of 1,000 patients provides sufficient representation for benchmarking and evaluation." Using a larger and more diverse test set provides greater clarity on results, as it better evaluates the proposed method's performance across varied scenarios.

We agree that a larger, more diverse test set would strengthen our evaluation. We are expanding our test set to approximately 10,000 samples (10x increase) with k-fold cross-validation. If accepted, we will include these expanded results in the camera-ready version.

## Response to Reviewer #JUfE

Thank you for your thoughtful review and positive feedback on our work. We appreciate your recognition of ColaCare's originality and importance in clinical decision-making!

### W1 (Human evaluation)

> ColaCare has not been used by real doctors. Given its domain, one would expect to see a human evaluation as well.

This is a valid point. While our current evaluation demonstrates ColaCare's strong performance on clinical tasks through comprehensive experiments on 3 real-world EHR datasets, we agree that human evaluation would provide additional valuable insights. We have conducted questionnaire-based human evaluations as shown in our responses to Reviewer #vxpv:

Our human evaluation employed a systematic assessment using peritoneal dialysis patient data. We engaged 12 experienced medical practitioners (5-15 years of experience) from nephrology departments across 5 hospitals. Practitioners rated agreement levels (1-5) for each patient case. ColaCare achieved a mean score of 4.4 vs. ConCare's 3.2, demonstrating strong alignment with expert clinical judgment. Blinded evaluations confirm that ColaCare's interpretability closely matches human expert practice patterns. (Check https://colacare.netlify.app/questionnaire)

### Q1 (Related multi-agent LLM works)

> It is not clear to me whether there are published papers that adopt a multi-agent approach similar to yours and use LLMs. You seem to imply that there are some but references 7, 14 and 26 in the second paragraph of Section 2.2 are rather general proposals. Maybe you should clarify the connection to your work.

Thank you for the clarification request. While references 7, 14, and 26 present general multi-agent frameworks, our work specifically adapts and extends these concepts for clinical settings. Recent works like MedAgents and ArgMed-Agents have explored LLM agents for medical reasoning but focus primarily on medical QA rather than structured EHR analysis. ColaCare uniquely combines expert models and LLM agents in an MDT-inspired framework specifically designed for clinical decision support. We will clarify these distinctions in the related work section.

### Q2 (Recent related papers)

> You may want to discuss in the related work, the recent related papers https://openreview.net/forum?id=lvycHSrFgk and https://www.nature.com/articles/s41746-024-01083-y.

The EHRFlow paper presents a workflow for EHR analysis using LLMs, while the npj Digital Medicine paper discusses evaluation frameworks for clinical LLM agents. Our work complements these by providing a concrete implementation of a multi-agent system that combines both expert models and LLMs for clinical decision support, bridging the capability gap between domain-specific expert models and LLMs. In addition, ColaCare's simulated MDT process and interpretable evidence generation serve as cost-effective tools for medical education and clinical practice by incorporating expert clinical knowledge and reasoning patterns. We will incorporate discussions of these papers and position our work accordingly in the related work section.
