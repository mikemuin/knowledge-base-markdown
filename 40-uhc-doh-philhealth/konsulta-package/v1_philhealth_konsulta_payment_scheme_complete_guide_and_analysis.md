# PhilHealth Konsulta Payment Scheme: Complete Guide and Analysis

## Table of Contents

1. [Basic Overview](https://claude.ai/chat/66260f5d-e997-41c1-9c70-74d16a209541#basic-overview)
2. [Payment Structure](https://claude.ai/chat/66260f5d-e997-41c1-9c70-74d16a209541#payment-structure)
3. [Covered Services](https://claude.ai/chat/66260f5d-e997-41c1-9c70-74d16a209541#covered-services)
4. [Performance Indicators](https://claude.ai/chat/66260f5d-e997-41c1-9c70-74d16a209541#performance-indicators)
5. [Age-Specific Considerations](https://claude.ai/chat/66260f5d-e997-41c1-9c70-74d16a209541#age-specific-considerations)
6. [Scenario Analysis: Elementary School Children](https://claude.ai/chat/66260f5d-e997-41c1-9c70-74d16a209541#scenario-analysis-elementary-school-children)
7. [Key Findings and System Issues](https://claude.ai/chat/66260f5d-e997-41c1-9c70-74d16a209541#key-findings-and-system-issues)

------

## Basic Overview

**Konsulta** (Konsultasyong Sulit at Tama) is PhilHealth's primary care benefit package that provides basic healthcare services to Filipino families through a **capitation payment system**.

### Key Facts

- **Maximum Annual Payment**: ₱1,700 per beneficiary per year (as of January 2024)
- **Applies to**: Both government and private healthcare providers
- **Additional Charges**: Private providers can charge up to ₱900 additional co-payment per year
- **Tax**: Private providers pay 2% withholding tax on PhilHealth payments

------

## Payment Structure

### Two-Tranche System

| Tranche            | Percentage | Amount | Timing      | Requirements                  |
| ------------------ | ---------- | ------ | ----------- | ----------------------------- |
| **First Tranche**  | 40%        | ₱680   | Monthly     | First Patient Encounter (FPE) |
| **Second Tranche** | 60%        | ₱1,020 | End of year | Performance targets achieved  |

### First Tranche (40% - ₱680)

**🔑 KEY POINTS:**

- **Trigger**: First Patient Encounter (FPE) required
- **Frequency**: Monthly payments based on new patients
- **Continuation**: No new FPE needed in following years IF at least one medical consultation occurred in previous year
- **Timeline**: Payment within 60 days after 8th day of following month
- **Data Submission**: Must upload complete data by 11:59 PM of 7th day of month

#### Sample Monthly Payments (Public Provider)

| Month            | New Patients with FPE | Payment (₱680 each) |
| ---------------- | --------------------- | ------------------- |
| January          | 1,500                 | ₱1,020,000          |
| February         | 2,000                 | ₱1,360,000          |
| March            | 500                   | ₱340,000            |
| **Annual Total** | **14,850**            | **₱10,098,000**     |

### Second Tranche (60% - ₱1,020)

**Performance-based payment** calculated at year-end based on achievement of four key indicators.

------

## Covered Services

### Laboratory Services (15 Tests)

| Test                                   | Purpose                                                |
| -------------------------------------- | ------------------------------------------------------ |
| **Complete Blood Count (CBC)**         | Blood disorders, infections                            |
| **Lipid Profile**                      | Heart disease risk assessment                          |
| **Fasting Blood Sugar (FBS)**          | Diabetes screening                                     |
| **Oral Glucose Tolerance Test (OGTT)** | Diabetes diagnosis                                     |
| **Glycosylated Hemoglobin (HbA1c)**    | Diabetes monitoring                                    |
| **Creatinine**                         | Kidney function                                        |
| **Chest X-Ray**                        | Lung problems                                          |
| **Sputum Microscopy**                  | Tuberculosis detection                                 |
| **Electrocardiogram (ECG)**            | Heart problems                                         |
| **Mammogram**                          | Breast cancer screening                                |
| **Ultrasound**                         | Internal organ imaging (upper abdomen, pelvic, breast) |
| **Urinalysis**                         | Kidney/urinary problems                                |
| **Pap Smear**                          | Cervical cancer screening                              |
| **Fecalysis**                          | Intestinal parasites                                   |
| **Fecal Occult Blood Test**            | Colon cancer screening                                 |

### Antibiotic Medicines (6 Types)

| Medicine           | Category       | Common Uses                  |
| ------------------ | -------------- | ---------------------------- |
| **Amoxicillin**    | Anti-microbial | Common bacterial infections  |
| **Co-Amoxiclav**   | Anti-microbial | Serious bacterial infections |
| **Cotrimoxazole**  | Anti-microbial | UTI, pneumonia               |
| **Nitrofurantoin** | Anti-microbial | Urinary tract infections     |
| **Ciprofloxacin**  | Anti-microbial | Various bacterial infections |
| **Clarithromycin** | Anti-microbial | Respiratory infections       |

### Chronic Disease (NCD) Medicines

#### For Diabetes:

- **Gliclazide**, **Metformin**

#### For High Blood Pressure:

- **Enalapril**, **Metoprolol**, **Amlodipine**, **Hydrochlorothiazide**, **Losartan**

#### For High Cholesterol:

- **Simvastatin**

#### For Heart Protection:

- **Aspirin**

#### For Asthma:

- **Salbutamol**, **Fluticasone + Salmeterol**

### Other Available Medicines:

- **Oral Rehydration Salts** (dehydration)
- **Prednisone** (inflammation)
- **Paracetamol** (fever and pain)
- **Chlorphenamine** (allergies - hospital use only)

------

## Performance Indicators

### Four Key Performance Areas

| Indicator                        | Target | Weight | Calculation                                                  |
| -------------------------------- | ------ | ------ | ------------------------------------------------------------ |
| **1. Primary Care Consultation** | 100%   | 30%    | Unique patients with consultation ÷ Total patients with FPE  |
| **2. Laboratory Services**       | 50%    | 30%    | Unique patients with lab services ÷ Total patients with FPE  |
| **3. Antibiotic Medicines**      | 15%    | 10%    | Unique patients with antibiotics ÷ Total patients with FPE   |
| **4. Chronic Disease Medicines** | 20%    | 30%    | Unique patients with NCD medicines ÷ Total patients with FPE |

### Sample Performance Calculation

**Scenario**: 14,850 patients with FPE

- Consultations: 8,000 patients
- Laboratory services: 3,000 patients
- Antibiotics: 2,000 patients
- NCD medicines: 1,500 patients

| Indicator              | Actual Performance | Target | Score | Weight | Weighted Score |
| ---------------------- | ------------------ | ------ | ----- | ------ | -------------- |
| **Consultation**       | 54% (8,000/14,850) | 100%   | 0.54  | 30%    | **16%**        |
| **Laboratory**         | 20% (3,000/14,850) | 50%    | 0.40  | 30%    | **12%**        |
| **Antibiotics**        | 13% (2,000/14,850) | 15%    | 0.87  | 10%    | **9%**         |
| **NCD Medicines**      | 10% (1,500/14,850) | 20%    | 0.50  | 30%    | **15%**        |
| **PERFORMANCE FACTOR** |                    |        |       |        | **52%**        |

### Second Tranche Payment Calculation

- **Public Provider**: 14,850 × 0.52 × ₱1,020 = **₱7,876,440**
- **Private Provider**: (14,850 × 0.52 × ₱1,020) - 2% tax = **₱7,718,911**

------

## Age-Specific Considerations

### CRITICAL FINDING: No Age-Specific Performance Factors

**The performance indicators apply universally to ALL ages** - this creates significant problems for providers serving specific age groups.

### Age-Appropriate Preventive Services

While services are age-targeted, **performance metrics are not**:

| Age Group       | Recommended Services           |
| --------------- | ------------------------------ |
| **0-12 months** | CBC with platelet count        |
| **1-4 years**   | General consultations          |
| **5-9 years**   | General consultations          |
| **10-19 years** | Chest X-ray                    |
| **20-39 years** | PAP Smear/VIA (can be waived)  |
| **40-49 years** | Lipid Profile, FBS, OGTT       |
| **50-59 years** | FOBT (Fecal Occult Blood Test) |
| **60+ years**   | ECG                            |
| **70+ years**   | All previous services          |
| **Risk-based**  | Mammography                    |

------

## Scenario Analysis: Elementary School Children

### Problem Statement

**What happens if 100% of patients are elementary school age children (5-9 years old)?**

### Impact Analysis for 1,000 Children

#### First Tranche: UNAFFECTED

- **Payment**: ₱680 × 1,000 = **₱680,000**
- This payment is guaranteed regardless of age

#### Second Tranche: SEVERE NEGATIVE IMPACT

| Performance Indicator         | Target | Reality for Ages 5-9                         | Expected Performance | Impact             |
| ----------------------------- | ------ | -------------------------------------------- | -------------------- | ------------------ |
| **Primary Care Consultation** | 100%   | ✅ Children visit doctors                     | **100%**             | **Full points**    |
| **Laboratory Services**       | 50%    | ❓ Most tests not age-appropriate             | **25%**              | **Reduced points** |
| **Antibiotic Medicines**      | 15%    | ⚠️ Only if infections occur                   | **10%**              | **Some points**    |
| **Chronic Disease Medicines** | 20%    | ❌ Children rarely have diabetes/hypertension | **2%**               | **Minimal points** |

### Realistic Performance Calculation

| Indicator                    | Expected Performance | Target | Score | Weight | Weighted Score |
| ---------------------------- | -------------------- | ------ | ----- | ------ | -------------- |
| **Consultation**             | 100%                 | 100%   | 1.00  | 30%    | **30%**        |
| **Laboratory**               | 25%                  | 50%    | 0.50  | 30%    | **15%**        |
| **Antibiotics**              | 10%                  | 15%    | 0.67  | 10%    | **7%**         |
| **Chronic Disease**          | 2%                   | 20%    | 0.10  | 30%    | **3%**         |
| **TOTAL PERFORMANCE FACTOR** |                      |        |       |        | **55%**        |

### Financial Impact

| Payment Component  | Standard Amount | With Children Only | Difference           |
| ------------------ | --------------- | ------------------ | -------------------- |
| **First Tranche**  | ₱680,000        | ₱680,000           | ₱0                   |
| **Second Tranche** | ₱1,020,000      | ₱561,000           | **-₱459,000**        |
| **TOTAL ANNUAL**   | ₱1,700,000      | ₱1,241,000         | **-₱459,000**        |
| **Per Patient**    | ₱1,700          | ₱1,241             | **-₱459 (27% loss)** |

------

## Key Findings and System Issues

### Critical System Design Flaws

1. **Age-Inappropriate Performance Targets**
   - Chronic disease medicine target (30% weight) impossible for healthy children
   - Laboratory service targets don't align with age-appropriate care
   - One-size-fits-all approach penalizes age-specific providers
2. **Financial Disincentives**
   - Providers serving children lose 27% of potential revenue
   - May discourage pediatric care provision
   - Creates unfair competitive disadvantage
3. **Misaligned Incentives**
   - System rewards inappropriate prescribing (giving diabetes medicines to children)
   - Penalizes appropriate age-specific care
   - Conflicts with evidence-based medicine

### Operational Challenges

1. **Data Submission Requirements**
   - Daily/weekly uploads required
   - XML file submissions through HCI Portal
   - Strict deadlines (11:59 PM, 7th day of month)
   - System validation requirements
2. **Documentation Requirements**
   - Photo documentation of patients mandatory
   - Electronic Konsulta Availment Slip (EKAS)
   - Electronic Prescription Slip (EPRESS)
   - Proper encoding in PhilHealth systems
3. **Service Level Agreements**
   - KPPs without mammogram/ultrasound must arrange SLAs within 3 years
   - Additional service notifications required to PhilHealth
   - Telemedicine services must be properly recorded and submitted

### Recommendations for System Improvement

1. **Age-Stratified Performance Indicators**
   - Develop separate targets for pediatric, adult, and geriatric populations
   - Align performance metrics with age-appropriate care standards
   - Weight indicators based on population health needs
2. **Flexible Service Requirements**
   - Adjust laboratory service targets based on age distribution
   - Create age-appropriate chronic disease indicators
   - Allow for population-specific performance calculations
3. **Payment Equity Measures**
   - Ensure equal payment opportunities regardless of age demographics
   - Consider population adjustment factors
   - Protect providers serving vulnerable or specific age groups

### Summary Statistics

- **Total Capitation**: ₱1,700 per beneficiary annually
- **Provider Types**: Government and private both eligible
- **Performance Weight Distribution**: 60% consultation + labs, 40% medicines
- **Payment Timeline**: Monthly (first tranche), Annual (second tranche)
- **Age Impact**: Up to 27% revenue loss for pediatric-focused providers
- **System Coverage**: All 15 laboratory tests, 21 essential medicines

------

## Conclusion

The PhilHealth Konsulta payment scheme provides a structured approach to primary care funding but contains significant design flaws that create unintended consequences for providers serving specific age demographics. The universal application of adult-oriented performance indicators creates financial penalties for appropriate pediatric care, highlighting the need for age-stratified performance metrics and payment adjustments.

Healthcare IT professionals implementing or optimizing Konsulta systems should be aware of these limitations and advocate for system improvements that align financial incentives with evidence-based, age-appropriate care delivery.

------

*This document is based on PhilHealth Circular No. 2024-0013 and related implementing guidelines as of 2024.*