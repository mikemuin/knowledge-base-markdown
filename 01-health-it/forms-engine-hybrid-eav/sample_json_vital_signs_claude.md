# Complete JSON Schema: Vital Signs Form

## JSON

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "formId": "vitals-001",
  "version": "1.0.0",
  "formName": "Vital Signs",
  "description": "Standard vital signs collection form for clinical encounters",
  "encounterType": "VITALS",
  "encounterTypeUUID": "67a71486-1a54-468f-ac3e-7091a9a79584",
  "published": true,
  "retired": false,
  "meta": {
    "programs": ["ALL"],
    "facilityTypes": ["CLINIC", "HOSPITAL", "HEALTH_POST"],
    "minimumAgeYears": 0,
    "applicableGenders": ["M", "F", "U"],
    "allowedRoles": ["NURSE", "CLINICIAN", "MEDICAL_OFFICER", "DATA_ENTRY_CLERK"],
    "tags": ["vitals", "triage", "routine"],
    "schemaAuthor": "Clinical Informatics Team",
    "lastReviewedDate": "2025-01-01",
    "nextReviewDate": "2026-01-01"
  },
  "pages": [
    {
      "pageId": "page-vitals-main",
      "label": "Vital Signs",
      "pageNumber": 1,
      "sections": [

        {
          "sectionId": "section-encounter-context",
          "label": "Encounter Context",
          "collapsible": false,
          "required": true,
          "questions": [
            {
              "id": "q_encounter_date",
              "type": "datetime",
              "label": "Date and Time of Measurement",
              "concept": "CIEL:1712",
              "conceptName": "Date of observation",
              "required": true,
              "default": "NOW()",
              "validators": [
                {
                  "type": "datetime",
                  "allowFutureDates": false,
                  "message": "Encounter date cannot be in the future"
                }
              ],
              "historicalExpression": "encounter.encounterDatetime"
            },
            {
              "id": "q_encounter_location",
              "type": "ui-select-extended",
              "label": "Measurement Location",
              "concept": "CIEL:162724",
              "conceptName": "Location of encounter",
              "required": true,
              "datasource": {
                "type": "location",
                "locationTag": "Facility Location"
              }
            },
            {
              "id": "q_clinician_id",
              "type": "person",
              "label": "Recorded By",
              "concept": "CIEL:1473",
              "required": true,
              "datasource": {
                "type": "provider",
                "roles": ["NURSE", "CLINICIAN"]
              }
            }
          ]
        },

        {
          "sectionId": "section-heart-rate",
          "label": "Heart Rate",
          "collapsible": false,
          "questions": [
            {
              "id": "q_heart_rate",
              "type": "number",
              "label": "Heart Rate",
              "concept": "CIEL:5087",
              "conceptName": "Pulse",
              "required": true,
              "questionOptions": {
                "rendering": "number",
                "unit": "beats/min",
                "concept": "CIEL:5087"
              },
              "validators": [
                {
                  "type": "range",
                  "min": 20,
                  "max": 300,
                  "message": "Heart rate must be between 20 and 300 beats/min"
                },
                {
                  "type": "js_expression",
                  "failsWhenExpression": "myValue < 20 || myValue > 300",
                  "message": "Value is outside physiologically plausible range"
                }
              ],
              "referenceRanges": [
                {
                  "rangeId": "hr-adult-normal",
                  "label": "Adult Normal",
                  "ageMinYears": 18,
                  "ageMaxYears": null,
                  "low": 60,
                  "high": 100,
                  "criticalLow": 40,
                  "criticalHigh": 150,
                  "unit": "beats/min"
                },
                {
                  "rangeId": "hr-child-normal",
                  "label": "Pediatric Normal (1-12 years)",
                  "ageMinYears": 1,
                  "ageMaxYears": 12,
                  "low": 70,
                  "high": 120,
                  "criticalLow": 50,
                  "criticalHigh": 160,
                  "unit": "beats/min"
                },
                {
                  "rangeId": "hr-neonate-normal",
                  "label": "Neonate Normal (0-1 year)",
                  "ageMinYears": 0,
                  "ageMaxYears": 1,
                  "low": 100,
                  "high": 160,
                  "criticalLow": 80,
                  "criticalHigh": 200,
                  "unit": "beats/min"
                }
              ],
              "alertRules": [
                {
                  "alertId": "hr-bradycardia",
                  "condition": "myValue < 60",
                  "severity": "WARNING",
                  "message": "Bradycardia detected. Confirm reading and assess patient."
                },
                {
                  "alertId": "hr-tachycardia",
                  "condition": "myValue > 100",
                  "severity": "WARNING",
                  "message": "Tachycardia detected. Consider clinical review."
                },
                {
                  "alertId": "hr-critical-low",
                  "condition": "myValue < 40",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Severe bradycardia. Immediate clinical assessment required."
                },
                {
                  "alertId": "hr-critical-high",
                  "condition": "myValue > 150",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Severe tachycardia. Immediate clinical assessment required."
                }
              ],
              "historicalExpression": "HD.getObs(patient, 'CIEL:5087', 1)",
              "trend": {
                "enabled": true,
                "lookbackDays": 30,
                "sparklineEnabled": true
              }
            },
            {
              "id": "q_heart_rate_rhythm",
              "type": "select",
              "label": "Heart Rhythm",
              "concept": "CIEL:160430",
              "conceptName": "Cardiac rhythm",
              "required": false,
              "answerConcepts": [
                { "label": "Regular", "value": "CIEL:1115", "sortWeight": 1 },
                { "label": "Irregular", "value": "CIEL:1116", "sortWeight": 2 },
                { "label": "Regularly Irregular", "value": "CIEL:163590", "sortWeight": 3 },
                { "label": "Irregularly Irregular", "value": "CIEL:163591", "sortWeight": 4 }
              ]
            },
            {
              "id": "q_heart_rate_method",
              "type": "select",
              "label": "Method of Measurement",
              "concept": "CIEL:160430",
              "conceptName": "Method of heart rate measurement",
              "required": false,
              "answerConcepts": [
                { "label": "Manual Palpation", "value": "CIEL:162501", "sortWeight": 1 },
                { "label": "Pulse Oximeter", "value": "CIEL:162502", "sortWeight": 2 },
                { "label": "ECG Monitor", "value": "CIEL:162503", "sortWeight": 3 },
                { "label": "Automated BP Cuff", "value": "CIEL:162504", "sortWeight": 4 }
              ]
            }
          ]
        },

        {
          "sectionId": "section-respiratory-rate",
          "label": "Respiratory Rate",
          "collapsible": false,
          "questions": [
            {
              "id": "q_respiratory_rate",
              "type": "number",
              "label": "Respiratory Rate",
              "concept": "CIEL:5242",
              "conceptName": "Respiratory rate",
              "required": true,
              "questionOptions": {
                "rendering": "number",
                "unit": "breaths/min",
                "concept": "CIEL:5242"
              },
              "validators": [
                {
                  "type": "range",
                  "min": 4,
                  "max": 80,
                  "message": "Respiratory rate must be between 4 and 80 breaths/min"
                }
              ],
              "referenceRanges": [
                {
                  "rangeId": "rr-adult-normal",
                  "label": "Adult Normal",
                  "ageMinYears": 18,
                  "ageMaxYears": null,
                  "low": 12,
                  "high": 20,
                  "criticalLow": 8,
                  "criticalHigh": 30,
                  "unit": "breaths/min"
                },
                {
                  "rangeId": "rr-child-normal",
                  "label": "Pediatric Normal (1-12 years)",
                  "ageMinYears": 1,
                  "ageMaxYears": 12,
                  "low": 20,
                  "high": 30,
                  "criticalLow": 15,
                  "criticalHigh": 40,
                  "unit": "breaths/min"
                },
                {
                  "rangeId": "rr-neonate-normal",
                  "label": "Neonate Normal (0-1 year)",
                  "ageMinYears": 0,
                  "ageMaxYears": 1,
                  "low": 30,
                  "high": 60,
                  "criticalLow": 20,
                  "criticalHigh": 70,
                  "unit": "breaths/min"
                }
              ],
              "alertRules": [
                {
                  "alertId": "rr-bradypnea",
                  "condition": "myValue < 12",
                  "severity": "WARNING",
                  "message": "Bradypnea detected. Assess airway and respiratory effort."
                },
                {
                  "alertId": "rr-tachypnea",
                  "condition": "myValue > 20",
                  "severity": "WARNING",
                  "message": "Tachypnea detected. Consider respiratory assessment."
                },
                {
                  "alertId": "rr-critical-low",
                  "condition": "myValue < 8",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Respiratory rate severely low. Immediate intervention required."
                },
                {
                  "alertId": "rr-critical-high",
                  "condition": "myValue > 30",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Severe tachypnea. Immediate respiratory assessment required."
                }
              ],
              "historicalExpression": "HD.getObs(patient, 'CIEL:5242', 1)",
              "trend": {
                "enabled": true,
                "lookbackDays": 30,
                "sparklineEnabled": true
              }
            },
            {
              "id": "q_oxygen_saturation",
              "type": "number",
              "label": "Oxygen Saturation (SpO2)",
              "concept": "CIEL:5092",
              "conceptName": "Arterial blood oxygen saturation by pulse oximetry",
              "required": false,
              "questionOptions": {
                "rendering": "number",
                "unit": "%"
              },
              "validators": [
                {
                  "type": "range",
                  "min": 50,
                  "max": 100,
                  "message": "SpO2 must be between 50% and 100%"
                }
              ],
              "alertRules": [
                {
                  "alertId": "spo2-low",
                  "condition": "myValue < 95",
                  "severity": "WARNING",
                  "message": "SpO2 below normal. Assess for hypoxia."
                },
                {
                  "alertId": "spo2-critical",
                  "condition": "myValue < 90",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Severe hypoxia. Immediate oxygen therapy required."
                }
              ]
            },
            {
              "id": "q_respiratory_pattern",
              "type": "select",
              "label": "Respiratory Pattern",
              "concept": "CIEL:163565",
              "required": false,
              "answerConcepts": [
                { "label": "Normal", "value": "CIEL:1115", "sortWeight": 1 },
                { "label": "Kussmaul", "value": "CIEL:163566", "sortWeight": 2 },
                { "label": "Cheyne-Stokes", "value": "CIEL:163567", "sortWeight": 3 },
                { "label": "Biot", "value": "CIEL:163568", "sortWeight": 4 },
                { "label": "Ataxic", "value": "CIEL:163569", "sortWeight": 5 }
              ],
              "hide": {
                "hideWhenExpression": "isEmpty(q_respiratory_rate)"
              }
            }
          ]
        },

        {
          "sectionId": "section-temperature",
          "label": "Temperature",
          "collapsible": false,
          "questions": [
            {
              "id": "q_temperature_unit",
              "type": "select",
              "label": "Temperature Unit",
              "concept": "CIEL:163011",
              "required": true,
              "default": "CELSIUS",
              "answerConcepts": [
                { "label": "Celsius (°C)", "value": "CELSIUS", "sortWeight": 1 },
                { "label": "Fahrenheit (°F)", "value": "FAHRENHEIT", "sortWeight": 2 }
              ]
            },
            {
              "id": "q_temperature",
              "type": "number",
              "label": "Temperature",
              "concept": "CIEL:5088",
              "conceptName": "Temperature",
              "required": true,
              "questionOptions": {
                "rendering": "number",
                "unitExpression": "q_temperature_unit === 'CELSIUS' ? '°C' : '°F'"
              },
              "validators": [
                {
                  "type": "js_expression",
                  "failsWhenExpression": "q_temperature_unit === 'CELSIUS' && (myValue < 25 || myValue > 45)",
                  "message": "Temperature in Celsius must be between 25°C and 45°C"
                },
                {
                  "type": "js_expression",
                  "failsWhenExpression": "q_temperature_unit === 'FAHRENHEIT' && (myValue < 77 || myValue > 113)",
                  "message": "Temperature in Fahrenheit must be between 77°F and 113°F"
                }
              ],
              "calculatedFields": [
                {
                  "fieldId": "q_temperature_celsius_normalized",
                  "label": "Temperature (°C normalized)",
                  "concept": "CIEL:5088",
                  "expression": "q_temperature_unit === 'FAHRENHEIT' ? Math.round(((q_temperature - 32) * 5/9) * 10) / 10 : q_temperature",
                  "hidden": true,
                  "storageTarget": "obs_numeric",
                  "storageUnit": "DEG_C"
                }
              ],
              "referenceRanges": [
                {
                  "rangeId": "temp-adult-normal-c",
                  "label": "Adult Normal",
                  "unit": "CELSIUS",
                  "ageMinYears": 18,
                  "low": 36.1,
                  "high": 37.2,
                  "criticalLow": 35.0,
                  "criticalHigh": 39.5
                },
                {
                  "rangeId": "temp-child-normal-c",
                  "label": "Pediatric Normal",
                  "unit": "CELSIUS",
                  "ageMinYears": 0,
                  "ageMaxYears": 17,
                  "low": 36.3,
                  "high": 37.4,
                  "criticalLow": 35.5,
                  "criticalHigh": 39.5
                }
              ],
              "alertRules": [
                {
                  "alertId": "temp-hypothermia",
                  "condition": "q_temperature_celsius_normalized < 36.0",
                  "severity": "WARNING",
                  "message": "Hypothermia detected. Assess and initiate warming measures."
                },
                {
                  "alertId": "temp-fever",
                  "condition": "q_temperature_celsius_normalized >= 37.5 && q_temperature_celsius_normalized < 39.0",
                  "severity": "WARNING",
                  "message": "Fever detected. Assess for infection source."
                },
                {
                  "alertId": "temp-high-fever",
                  "condition": "q_temperature_celsius_normalized >= 39.0 && q_temperature_celsius_normalized < 41.0",
                  "severity": "WARNING",
                  "message": "High fever. Initiate antipyretic therapy and investigate cause."
                },
                {
                  "alertId": "temp-critical-high",
                  "condition": "q_temperature_celsius_normalized >= 41.0",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Hyperpyrexia. Immediate cooling and emergency assessment required."
                },
                {
                  "alertId": "temp-critical-low",
                  "condition": "q_temperature_celsius_normalized < 35.0",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Severe hypothermia. Immediate warming intervention required."
                }
              ],
              "historicalExpression": "HD.getObs(patient, 'CIEL:5088', 1)",
              "trend": {
                "enabled": true,
                "lookbackDays": 7,
                "sparklineEnabled": true
              }
            },
            {
              "id": "q_temperature_route",
              "type": "select",
              "label": "Route of Measurement",
              "concept": "CIEL:163013",
              "conceptName": "Temperature measurement method",
              "required": true,
              "answerConcepts": [
                { "label": "Oral", "value": "CIEL:163014", "sortWeight": 1 },
                { "label": "Axillary", "value": "CIEL:163015", "sortWeight": 2 },
                { "label": "Rectal", "value": "CIEL:163016", "sortWeight": 3 },
                { "label": "Tympanic", "value": "CIEL:163017", "sortWeight": 4 },
                { "label": "Temporal Artery", "value": "CIEL:163018", "sortWeight": 5 }
              ]
            }
          ]
        },

        {
          "sectionId": "section-blood-pressure",
          "label": "Blood Pressure",
          "collapsible": false,
          "questions": [
            {
              "id": "q_bp_position",
              "type": "select",
              "label": "Patient Position During Measurement",
              "concept": "CIEL:163019",
              "required": true,
              "answerConcepts": [
                { "label": "Sitting", "value": "CIEL:163020", "sortWeight": 1 },
                { "label": "Lying (Supine)", "value": "CIEL:163021", "sortWeight": 2 },
                { "label": "Standing", "value": "CIEL:163022", "sortWeight": 3 }
              ]
            },
            {
              "id": "q_bp_arm",
              "type": "select",
              "label": "Arm Used",
              "concept": "CIEL:163023",
              "required": false,
              "answerConcepts": [
                { "label": "Right Arm", "value": "CIEL:163024", "sortWeight": 1 },
                { "label": "Left Arm", "value": "CIEL:163025", "sortWeight": 2 }
              ]
            },
            {
              "id": "q_bp_systolic",
              "type": "number",
              "label": "Systolic Blood Pressure",
              "concept": "CIEL:5085",
              "conceptName": "Systolic blood pressure",
              "required": true,
              "questionOptions": {
                "rendering": "number",
                "unit": "mmHg"
              },
              "validators": [
                {
                  "type": "range",
                  "min": 50,
                  "max": 300,
                  "message": "Systolic BP must be between 50 and 300 mmHg"
                }
              ],
              "referenceRanges": [
                {
                  "rangeId": "sbp-adult-normal",
                  "label": "Adult Normal",
                  "ageMinYears": 18,
                  "low": 90,
                  "high": 120,
                  "criticalLow": 70,
                  "criticalHigh": 180,
                  "unit": "mmHg"
                }
              ],
              "alertRules": [
                {
                  "alertId": "sbp-hypotension",
                  "condition": "myValue < 90",
                  "severity": "WARNING",
                  "message": "Hypotension detected. Assess for shock, dehydration, or medication effect."
                },
                {
                  "alertId": "sbp-elevated",
                  "condition": "myValue >= 130 && myValue < 140",
                  "severity": "INFO",
                  "message": "Elevated blood pressure (Stage 1 range). Monitor and review risk factors."
                },
                {
                  "alertId": "sbp-stage2-htn",
                  "condition": "myValue >= 140 && myValue < 180",
                  "severity": "WARNING",
                  "message": "Stage 2 Hypertension. Clinical review and management plan required."
                },
                {
                  "alertId": "sbp-hypertensive-urgency",
                  "condition": "myValue >= 180",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Hypertensive urgency/emergency. Immediate clinical assessment required."
                },
                {
                  "alertId": "sbp-critical-low",
                  "condition": "myValue < 70",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Severe hypotension. Immediate resuscitation assessment required."
                }
              ],
              "historicalExpression": "HD.getObs(patient, 'CIEL:5085', 1)",
              "trend": {
                "enabled": true,
                "lookbackDays": 90,
                "sparklineEnabled": true
              }
            },
            {
              "id": "q_bp_diastolic",
              "type": "number",
              "label": "Diastolic Blood Pressure",
              "concept": "CIEL:5086",
              "conceptName": "Diastolic blood pressure",
              "required": true,
              "questionOptions": {
                "rendering": "number",
                "unit": "mmHg"
              },
              "validators": [
                {
                  "type": "range",
                  "min": 30,
                  "max": 200,
                  "message": "Diastolic BP must be between 30 and 200 mmHg"
                },
                {
                  "type": "js_expression",
                  "failsWhenExpression": "!isEmpty(q_bp_systolic) && myValue >= q_bp_systolic",
                  "message": "Diastolic BP cannot be greater than or equal to Systolic BP"
                },
                {
                  "type": "js_expression",
                  "failsWhenExpression": "!isEmpty(q_bp_systolic) && (q_bp_systolic - myValue) < 10",
                  "message": "Pulse pressure appears implausibly narrow. Please verify both readings."
                }
              ],
              "referenceRanges": [
                {
                  "rangeId": "dbp-adult-normal",
                  "label": "Adult Normal",
                  "ageMinYears": 18,
                  "low": 60,
                  "high": 80,
                  "criticalLow": 40,
                  "criticalHigh": 120,
                  "unit": "mmHg"
                }
              ],
              "alertRules": [
                {
                  "alertId": "dbp-stage2-htn",
                  "condition": "myValue >= 90",
                  "severity": "WARNING",
                  "message": "Diastolic hypertension detected. Clinical review required."
                },
                {
                  "alertId": "dbp-critical-high",
                  "condition": "myValue >= 120",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: Severe diastolic hypertension. Immediate assessment required."
                }
              ],
              "historicalExpression": "HD.getObs(patient, 'CIEL:5086', 1)",
              "trend": {
                "enabled": true,
                "lookbackDays": 90,
                "sparklineEnabled": true
              }
            },
            {
              "id": "q_bp_mean_arterial_pressure",
              "type": "number",
              "label": "Mean Arterial Pressure (MAP)",
              "concept": "CIEL:163035",
              "conceptName": "Mean arterial pressure",
              "required": false,
              "readonly": true,
              "questionOptions": {
                "rendering": "number",
                "unit": "mmHg"
              },
              "calculatedExpression": "!isEmpty(q_bp_systolic) && !isEmpty(q_bp_diastolic) ? Math.round((q_bp_diastolic * 2 + q_bp_systolic) / 3) : null",
              "alertRules": [
                {
                  "alertId": "map-low",
                  "condition": "myValue < 65",
                  "severity": "CRITICAL",
                  "message": "CRITICAL: MAP below 65 mmHg. Risk of organ hypoperfusion. Immediate assessment."
                }
              ]
            },
            {
              "id": "q_bp_pulse_pressure",
              "type": "number",
              "label": "Pulse Pressure",
              "concept": "CIEL:163036",
              "required": false,
              "readonly": true,
              "questionOptions": {
                "rendering": "number",
                "unit": "mmHg"
              },
              "calculatedExpression": "!isEmpty(q_bp_systolic) && !isEmpty(q_bp_diastolic) ? q_bp_systolic - q_bp_diastolic : null",
              "alertRules": [
                {
                  "alertId": "pp-narrow",
                  "condition": "myValue < 25",
                  "severity": "WARNING",
                  "message": "Narrow pulse pressure. Consider cardiac tamponade or severe aortic stenosis."
                },
                {
                  "alertId": "pp-wide",
                  "condition": "myValue > 60",
                  "severity": "WARNING",
                  "message": "Wide pulse pressure. Consider aortic regurgitation or atherosclerosis."
                }
              ]
            },
            {
              "id": "q_bp_cuff_size",
              "type": "select",
              "label": "Cuff Size Used",
              "concept": "CIEL:163026",
              "required": false,
              "answerConcepts": [
                { "label": "Small Adult (12x22 cm)", "value": "CIEL:163027", "sortWeight": 1 },
                { "label": "Adult (16x30 cm)", "value": "CIEL:163028", "sortWeight": 2 },
                { "label": "Large Adult (16x36 cm)", "value": "CIEL:163029", "sortWeight": 3 },
                { "label": "Thigh (16x42 cm)", "value": "CIEL:163030", "sortWeight": 4 },
                { "label": "Pediatric", "value": "CIEL:163031", "sortWeight": 5 },
                { "label": "Infant/Neonatal", "value": "CIEL:163032", "sortWeight": 6 }
              ]
            },
            {
              "id": "q_bp_readings_group",
              "type": "obsGroup",
              "label": "Additional BP Readings (for averaging)",
              "concept": "CIEL:163033",
              "conceptName": "Blood pressure panel",
              "required": false,
              "repeating": true,
              "minOccurs": 0,
              "maxOccurs": 2,
              "addButtonLabel": "Add Another Reading",
              "questions": [
                {
                  "id": "q_bp_systolic_repeat",
                  "type": "number",
                  "label": "Systolic (Repeat)",
                  "concept": "CIEL:5085",
                  "required": true,
                  "questionOptions": {
                    "rendering": "number",
                    "unit": "mmHg"
                  },
                  "validators": [
                    { "type": "range", "min": 50, "max": 300 }
                  ]
                },
                {
                  "id": "q_bp_diastolic_repeat",
                  "type": "number",
                  "label": "Diastolic (Repeat)",
                  "concept": "CIEL:5086",
                  "required": true,
                  "questionOptions": {
                    "rendering": "number",
                    "unit": "mmHg"
                  },
                  "validators": [
                    { "type": "range", "min": 30, "max": 200 },
                    {
                      "type": "js_expression",
                      "failsWhenExpression": "myValue >= q_bp_systolic_repeat",
                      "message": "Diastolic must be less than Systolic"
                    }
                  ]
                }
              ]
            }
          ]
        },

        {
          "sectionId": "section-clinical-notes",
          "label": "Clinical Notes and Disposition",
          "collapsible": true,
          "collapsed": false,
          "questions": [
            {
              "id": "q_triage_level",
              "type": "select",
              "label": "Triage Level",
              "concept": "CIEL:162643",
              "required": false,
              "calculatedExpression": "computeTriageLevel(q_heart_rate, q_respiratory_rate, q_temperature_celsius_normalized, q_bp_systolic, q_bp_diastolic, q_oxygen_saturation)",
              "overrideable": true,
              "answerConcepts": [
                { "label": "Level 1 - Resuscitation (Immediate)", "value": "CIEL:162644", "sortWeight": 1, "cssClass": "triage-level-1" },
                { "label": "Level 2 - Emergent (< 15 min)", "value": "CIEL:162645", "sortWeight": 2, "cssClass": "triage-level-2" },
                { "label": "Level 3 - Urgent (< 30 min)", "value": "CIEL:162646", "sortWeight": 3, "cssClass": "triage-level-3" },
                { "label": "Level 4 - Semi-urgent (< 60 min)", "value": "CIEL:162647", "sortWeight": 4, "cssClass": "triage-level-4" },
                { "label": "Level 5 - Non-urgent (< 120 min)", "value": "CIEL:162648", "sortWeight": 5, "cssClass": "triage-level-5" }
              ]
            },
            {
              "id": "q_vitals_notes",
              "type": "textarea",
              "label": "Clinical Notes",
              "concept": "CIEL:160430",
              "required": false,
              "questionOptions": {
                "rows": 3,
                "maxLength": 1000
              }
            }
          ]
        }

      ]
    }
  ],

  "logic": {
    "computedExpressions": {
      "computeTriageLevel": {
        "description": "Derives triage level from combined vital sign alerts",
        "expression": "function computeTriageLevel(hr, rr, temp, sbp, dbp, spo2) { var critical = (hr < 40 || hr > 150 || rr < 8 || rr > 30 || temp < 35.0 || temp >= 41.0 || sbp < 70 || sbp >= 180 || (spo2 && spo2 < 90)); var emergent = (!critical && (hr < 50 || hr > 130 || rr < 10 || rr > 25 || temp < 35.5 || temp >= 39.5 || sbp < 80 || sbp >= 160 || (spo2 && spo2 < 93))); if (critical) return 'CIEL:162644'; if (emergent) return 'CIEL:162645'; return 'CIEL:162647'; }"
      }
    },
    "crossFieldValidations": [
      {
        "ruleId": "bp-systolic-diastolic-consistency",
        "description": "Systolic must always exceed diastolic",
        "expression": "isEmpty(q_bp_systolic) || isEmpty(q_bp_diastolic) || q_bp_systolic > q_bp_diastolic",
        "severity": "ERROR",
        "message": "Systolic blood pressure must be greater than diastolic blood pressure"
      },
      {
        "ruleId": "pulse-pressure-plausibility",
        "description": "Pulse pressure must be physiologically plausible",
        "expression": "isEmpty(q_bp_systolic) || isEmpty(q_bp_diastolic) || (q_bp_systolic - q_bp_diastolic) >= 10",
        "severity": "WARNING",
        "message": "Pulse pressure less than 10 mmHg is highly unusual. Please verify readings."
      }
    ]
  },

  "i18n": {
    "defaultLocale": "en",
    "supportedLocales": ["en", "fr", "sw", "pt", "es", "ar"],
    "translationKeys": {
      "section-heart-rate.label": {
        "en": "Heart Rate",
        "fr": "Fréquence cardiaque",
        "sw": "Kiwango cha Moyo",
        "pt": "Frequência cardíaca",
        "es": "Frecuencia cardíaca",
        "ar": "معدل ضربات القلب"
      },
      "section-temperature.label": {
        "en": "Temperature",
        "fr": "Température",
        "sw": "Joto la Mwili",
        "pt": "Temperatura",
        "es": "Temperatura",
        "ar": "درجة الحرارة"
      },
      "section-blood-pressure.label": {
        "en": "Blood Pressure",
        "fr": "Pression artérielle",
        "sw": "Shinikizo la Damu",
        "pt": "Pressão arterial",
        "es": "Presión arterial",
        "ar": "ضغط الدم"
      },
      "section-respiratory-rate.label": {
        "en": "Respiratory Rate",
        "fr": "Fréquence respiratoire",
        "sw": "Kiwango cha Kupumua",
        "pt": "Frequência respiratória",
        "es": "Frecuencia respiratoria",
        "ar": "معدل التنفس"
      }
    }
  },

  "submission": {
    "encounterProviderRoles": ["CIEL:162527"],
    "encounterLocation": "useCurrentLocation",
    "onSubmitActions": [
      {
        "actionId": "refresh-patient-header",
        "type": "REFRESH_PATIENT_VITALS_HEADER"
      },
      {
        "actionId": "check-triage-escalation",
        "type": "TRIGGER_ALERT_IF",
        "condition": "q_triage_level === 'CIEL:162644' || q_triage_level === 'CIEL:162645'",
        "alertType": "CLINICAL_ESCALATION",
        "message": "High acuity vitals recorded. Clinical review triggered.",
        "notifyRoles": ["CLINICIAN", "MEDICAL_OFFICER"]
      },
      {
        "actionId": "populate-mart",
        "type": "ASYNC_PROCEDURE",
        "procedure": "sp_refresh_vitals_mart",
        "params": { "encounterId": "{{encounter.encounterUUID}}" }
      }
    ]
  },

  "audit": {
    "trackingEnabled": true,
    "captureFieldTouchEvents": true,
    "captureSubmitDuration": true,
    "captureDeviceInfo": true,
    "captureNetworkStatus": true
  }
}
```

------

### Key Design Decisions Explained

**Dual-unit temperature** is handled entirely within the schema through a hidden normalized calculated field. The storage layer always receives Celsius regardless of how the user entered the value, which is the correct semantic contract for a terminology-bound EAV store.

**Blood pressure repeating group** uses the nested `obsGroup` pattern with `maxOccurs: 2` to allow up to two additional readings for averaging, which is standard clinical practice for hypertension management. Each repeat maps to its own `obs_group` record in storage.

**Computed MAP and Pulse Pressure** are `readonly` calculated fields bound to their own CIEL concepts, meaning they are stored as discrete observations — not application-level ephemera — and are therefore queryable in the analytics layer like any other measured value.

**Triage level auto-computation** via `computeTriageLevel` demonstrates how clinical decision support can be embedded directly in the forms engine rather than requiring a separate CDS service call for routine triage. The field is marked `overrideable: true` so the clinician retains final authority.

**The i18n block** with Swahili, French, Portuguese, Arabic, and Spanish labels reflects the global health deployment reality where the same form schema must serve facilities across multiple WHO regions without schema duplication.

---

## Follow Up #1

> Help me understand the JSON. Am I correct in the following:
>
> - This is the JSON that the frontend renderer needs to process
> - Much of the content here comes from forms and data metadata and is embedded in the JSON
> - This JSON is basically generated on the fly based on the key details in the database

Yes, you are exactly correct on all three points, and it is worth unpacking each one precisely because understanding the relationship between them is what separates a well-architected forms engine from one that becomes unmaintainable.

------

### What the Frontend Renderer Does With This JSON

The renderer — whether Enketo, react-jsonschema-form, or a custom Angular/React implementation — receives this JSON and uses it as its complete instruction set. It never hardcodes a form. It reads the schema and dynamically constructs the UI: which fields to show, in what order, what type of input widget to render, what validation rules to enforce, what skip logic to apply, and what labels to display in the current locale. The renderer is intentionally form-agnostic. It knows how to render a `number` type, a `select` type, an `obsGroup` type — but it has no knowledge of vital signs specifically. That clinical knowledge lives entirely in the JSON.

------

### Where the Content Actually Comes From

This is the critical architectural insight. The JSON is not authored by hand in full for each form. It is assembled from several distinct sources in your database, and this is precisely why the metadata layer matters so much.

The **concept bindings** like `"concept": "CIEL:5085"` and all the `answerConcepts` arrays come directly from your terminology service and concept dictionary tables. The renderer resolves the display label at runtime from `concept_name` using the current locale — which is why the i18n block can be generated rather than manually translated for each form.

The **reference ranges** and **alert rules** are stored in your clinical rules engine tables, not hardcoded per form. A concept like CIEL:5085 (systolic BP) has associated normal ranges tied to age bands, and those rows in the database populate the `referenceRanges` array when the schema is assembled.

The **validators** are generated from attribute type metadata — the `min`, `max`, and data type constraints you defined in `attribute_type` and enriched with clinical plausibility rules from your DQ rule table.

The **form structure itself** — pages, sections, question ordering, skip logic expressions — is the one part that is genuinely authored, stored in a `form_schema` table, and versioned. This is the creative clinical design work. Everything else is assembled around it.

------

### The Generation Mechanism

You are right that this JSON is generated on the fly, and the typical pattern works like this. When a clinician opens a form, the application calls a form schema endpoint — something like `GET /api/forms/vitals-001?version=1.0.0&locale=sw&patientId=xyz`. The backend service then does several things in sequence.

It fetches the base form structure from the `form_schema` table. It resolves every concept reference against the terminology service, injecting the correct locale-specific labels. It fetches reference ranges and alert rules for each concept from the clinical rules tables, filtered by the patient's age and gender derived from the patient record. It evaluates any `historicalExpression` fields to pre-populate previous values. It checks the patient's program enrollment to determine if any program-specific questions should be injected or suppressed. It then serializes the entire assembled object as the JSON you see and returns it to the renderer.

The result is that the same `form_schema` row in the database produces a different JSON payload for a neonate versus an adult, for a Swahili-speaking facility versus a French-speaking one, and for a patient enrolled in the HIV program versus one who is not. The schema is a template. The JSON is the rendered instance of that template for a specific clinical context.

------

### Why This Architecture Matters

The practical consequence is that when WHO updates a reference range for pediatric blood pressure, or when your clinical team decides to add a new triage alert threshold, no form needs to be redeployed. You update one row in your clinical rules table, and every subsequent form generation picks it up automatically. The form schema version does not change because the structure did not change — only the embedded clinical knowledge did. This is the governance advantage that makes the metadata-driven approach worth the upfront complexity.

---

## Follow Up #2

> I am expecting the IT people or the users' devs to build the JSON to execute the "widget tree". How is that done, and what does the JSON look like? I imagine the devs to build a JSON file with the identified widgets, structure, etc., and upload that to a UI that would ingest that along with other configurations. Is this correct? Use the vital signs form as basis for sample

Yes, that is exactly the right mental model, and it is the correct way to separate concerns between the **clinical form authors** (who define what to collect) and the **renderer engine** (which knows how to display it). Let me break down the full picture.

------

### The Two Distinct JSON Artifacts

There are actually two separate JSON documents involved, and conflating them is the most common source of confusion in forms engine implementations.

The first is the **Form Definition JSON** — what your devs author and upload. This is intentionally minimal. It describes structure, widget types, concept bindings, and layout. It does not contain reference ranges, translated labels, or alert thresholds. Those are resolved by the backend at request time as described earlier.

The second is the **Resolved Runtime JSON** — what the renderer actually receives. This is the fully assembled document shown in the previous response, generated by the backend by enriching the Form Definition with data from the terminology service, rules engine, and patient context.

Your devs only ever author and maintain the first one.

------

### The Form Definition JSON — What Devs Actually Build

```json
{
  "formId": "vitals-001",
  "version": "1.0.0",
  "formName": "Vital Signs",
  "encounterType": "VITALS",
  "meta": {
    "programs": ["ALL"],
    "facilityTypes": ["CLINIC", "HOSPITAL", "HEALTH_POST"],
    "allowedRoles": ["NURSE", "CLINICIAN", "MEDICAL_OFFICER"]
  },
  "pages": [
    {
      "pageId": "page-vitals-main",
      "label": "Vital Signs",
      "sections": [

        {
          "sectionId": "section-encounter-context",
          "label": "Encounter Context",
          "questions": [
            {
              "id": "q_encounter_date",
              "type": "datetime",
              "label": "Date and Time of Measurement",
              "concept": "CIEL:1712",
              "required": true,
              "default": "NOW()"
            },
            {
              "id": "q_encounter_location",
              "type": "ui-select-extended",
              "label": "Measurement Location",
              "concept": "CIEL:162724",
              "required": true,
              "datasource": {
                "type": "location",
                "locationTag": "Facility Location"
              }
            }
          ]
        },

        {
          "sectionId": "section-heart-rate",
          "label": "Heart Rate",
          "questions": [
            {
              "id": "q_heart_rate",
              "type": "number",
              "label": "Heart Rate",
              "concept": "CIEL:5087",
              "required": true,
              "unit": "beats/min",
              "referenceRangeKey": "CIEL:5087",
              "alertRuleKey": "CIEL:5087",
              "trend": true
            },
            {
              "id": "q_heart_rate_rhythm",
              "type": "select",
              "label": "Heart Rhythm",
              "concept": "CIEL:160430",
              "required": false,
              "answerConceptSource": "CIEL:160430"
            },
            {
              "id": "q_heart_rate_method",
              "type": "select",
              "label": "Method of Measurement",
              "concept": "CIEL:162500",
              "required": false,
              "answerConceptSource": "CIEL:162500"
            }
          ]
        },

        {
          "sectionId": "section-respiratory-rate",
          "label": "Respiratory Rate",
          "questions": [
            {
              "id": "q_respiratory_rate",
              "type": "number",
              "label": "Respiratory Rate",
              "concept": "CIEL:5242",
              "required": true,
              "unit": "breaths/min",
              "referenceRangeKey": "CIEL:5242",
              "alertRuleKey": "CIEL:5242",
              "trend": true
            },
            {
              "id": "q_oxygen_saturation",
              "type": "number",
              "label": "Oxygen Saturation (SpO2)",
              "concept": "CIEL:5092",
              "required": false,
              "unit": "%",
              "referenceRangeKey": "CIEL:5092",
              "alertRuleKey": "CIEL:5092"
            },
            {
              "id": "q_respiratory_pattern",
              "type": "select",
              "label": "Respiratory Pattern",
              "concept": "CIEL:163565",
              "required": false,
              "answerConceptSource": "CIEL:163565",
              "hide": {
                "hideWhenExpression": "isEmpty(q_respiratory_rate)"
              }
            }
          ]
        },

        {
          "sectionId": "section-temperature",
          "label": "Temperature",
          "questions": [
            {
              "id": "q_temperature_unit",
              "type": "select",
              "label": "Temperature Unit",
              "concept": "CIEL:163011",
              "required": true,
              "default": "CELSIUS",
              "inlineOptions": [
                { "label": "Celsius (°C)", "value": "CELSIUS" },
                { "label": "Fahrenheit (°F)", "value": "FAHRENHEIT" }
              ]
            },
            {
              "id": "q_temperature",
              "type": "number",
              "label": "Temperature",
              "concept": "CIEL:5088",
              "required": true,
              "unitExpression": "q_temperature_unit === 'CELSIUS' ? '°C' : '°F'",
              "referenceRangeKey": "CIEL:5088",
              "alertRuleKey": "CIEL:5088",
              "trend": true,
              "normalizeExpression": "q_temperature_unit === 'FAHRENHEIT' ? Math.round(((q_temperature - 32) * 5/9) * 10) / 10 : q_temperature",
              "normalizeUnit": "DEG_C"
            },
            {
              "id": "q_temperature_route",
              "type": "select",
              "label": "Route of Measurement",
              "concept": "CIEL:163013",
              "required": true,
              "answerConceptSource": "CIEL:163013"
            }
          ]
        },

        {
          "sectionId": "section-blood-pressure",
          "label": "Blood Pressure",
          "questions": [
            {
              "id": "q_bp_position",
              "type": "select",
              "label": "Patient Position",
              "concept": "CIEL:163019",
              "required": true,
              "answerConceptSource": "CIEL:163019"
            },
            {
              "id": "q_bp_arm",
              "type": "select",
              "label": "Arm Used",
              "concept": "CIEL:163023",
              "required": false,
              "answerConceptSource": "CIEL:163023"
            },
            {
              "id": "q_bp_systolic",
              "type": "number",
              "label": "Systolic Blood Pressure",
              "concept": "CIEL:5085",
              "required": true,
              "unit": "mmHg",
              "referenceRangeKey": "CIEL:5085",
              "alertRuleKey": "CIEL:5085",
              "trend": true
            },
            {
              "id": "q_bp_diastolic",
              "type": "number",
              "label": "Diastolic Blood Pressure",
              "concept": "CIEL:5086",
              "required": true,
              "unit": "mmHg",
              "referenceRangeKey": "CIEL:5086",
              "alertRuleKey": "CIEL:5086",
              "trend": true,
              "crossValidate": [
                {
                  "ruleId": "dbp-lt-sbp",
                  "expression": "myValue < q_bp_systolic",
                  "message": "Diastolic must be less than Systolic"
                }
              ]
            },
            {
              "id": "q_bp_map",
              "type": "number",
              "label": "Mean Arterial Pressure (MAP)",
              "concept": "CIEL:163035",
              "required": false,
              "readonly": true,
              "unit": "mmHg",
              "calculatedExpression": "Math.round((q_bp_diastolic * 2 + q_bp_systolic) / 3)",
              "alertRuleKey": "CIEL:163035"
            },
            {
              "id": "q_bp_readings_group",
              "type": "obsGroup",
              "label": "Additional BP Readings",
              "concept": "CIEL:163033",
              "repeating": true,
              "maxOccurs": 2,
              "addButtonLabel": "Add Another Reading",
              "questions": [
                {
                  "id": "q_bp_systolic_repeat",
                  "type": "number",
                  "label": "Systolic (Repeat)",
                  "concept": "CIEL:5085",
                  "required": true,
                  "unit": "mmHg"
                },
                {
                  "id": "q_bp_diastolic_repeat",
                  "type": "number",
                  "label": "Diastolic (Repeat)",
                  "concept": "CIEL:5086",
                  "required": true,
                  "unit": "mmHg",
                  "crossValidate": [
                    {
                      "ruleId": "repeat-dbp-lt-sbp",
                      "expression": "myValue < q_bp_systolic_repeat",
                      "message": "Diastolic must be less than Systolic"
                    }
                  ]
                }
              ]
            },
            {
              "id": "q_bp_cuff_size",
              "type": "select",
              "label": "Cuff Size",
              "concept": "CIEL:163026",
              "required": false,
              "answerConceptSource": "CIEL:163026"
            }
          ]
        },

        {
          "sectionId": "section-clinical-notes",
          "label": "Clinical Notes and Disposition",
          "collapsible": true,
          "questions": [
            {
              "id": "q_triage_level",
              "type": "select",
              "label": "Triage Level",
              "concept": "CIEL:162643",
              "required": false,
              "overrideable": true,
              "calculatedExpression": "computeTriageLevel(q_heart_rate, q_respiratory_rate, q_temperature, q_bp_systolic, q_bp_diastolic, q_oxygen_saturation)",
              "answerConceptSource": "CIEL:162643"
            },
            {
              "id": "q_vitals_notes",
              "type": "textarea",
              "label": "Clinical Notes",
              "concept": "CIEL:160430",
              "required": false,
              "maxLength": 1000
            }
          ]
        }

      ]
    }
  ],

  "submission": {
    "onSubmitActions": [
      { "type": "REFRESH_PATIENT_VITALS_HEADER" },
      {
        "type": "TRIGGER_ALERT_IF",
        "condition": "q_triage_level === 'CIEL:162644' || q_triage_level === 'CIEL:162645'",
        "notifyRoles": ["CLINICIAN", "MEDICAL_OFFICER"]
      }
    ]
  }
}
```

------

### What the Dev Authoring UI Does With This

When a dev or clinical informatics officer uploads this JSON through the form builder UI, the backend ingestion pipeline does the following in sequence.

```
Dev uploads Form Definition JSON
            │
            ▼
┌─────────────────────────────────┐
│   Schema Validation Service     │  ← validates structure, required fields,
│                                 │    widget type registry
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│   Concept Resolution Service    │  ← verifies every CIEL: reference exists
│                                 │    in your local concept dictionary
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│   Reference Range Linker        │  ← confirms referenceRangeKey and
│                                 │    alertRuleKey rows exist in rules tables
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│   form_schema Table             │  ← stores the definition JSON as a
│   (persisted, versioned)        │    versioned immutable record
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│   Widget Tree Registry          │  ← maps each widget type declared in
│                                 │    the JSON to its renderer component
└─────────────────────────────────┘
```

------

### The Widget Tree Registry

This is the piece your devs need to maintain separately from the form definitions. It is a configuration document — stored in the database or as a static config file — that maps every `type` value in the form definition to a concrete renderer component and its capability flags.

```json
{
  "widgetRegistry": {
    "version": "1.0.0",
    "widgets": {
      "number": {
        "component": "NumberInputWidget",
        "supportsUnit": true,
        "supportsReferenceRange": true,
        "supportsAlerts": true,
        "supportsTrend": true,
        "supportsReadonly": true,
        "supportsCalculated": true,
        "storageType": "obs_numeric"
      },
      "select": {
        "component": "ConceptSelectWidget",
        "supportsAnswerConcepts": true,
        "supportsInlineOptions": true,
        "supportsCalculated": true,
        "supportsOverride": true,
        "storageType": "obs_coded"
      },
      "datetime": {
        "component": "DateTimeWidget",
        "supportsDefault": true,
        "supportsFutureDateBlock": true,
        "storageType": "obs_datetime"
      },
      "textarea": {
        "component": "FreeTextWidget",
        "supportsMaxLength": true,
        "storageType": "obs_text"
      },
      "ui-select-extended": {
        "component": "AsyncSearchSelectWidget",
        "supportsDatasource": true,
        "datasourceTypes": ["location", "provider", "patient", "concept"],
        "storageType": "obs_coded"
      },
      "obsGroup": {
        "component": "ObsGroupWidget",
        "supportsRepeating": true,
        "supportsMaxOccurs": true,
        "supportsNesting": true,
        "maxNestingDepth": 3,
        "storageType": "obs_group"
      }
    }
  }
}
```

------

### The Complete Flow End to End

```
DEV SIDE                        BACKEND                        RENDERER SIDE
──────────                      ───────                        ─────────────
Author Form                     Ingest + Validate
Definition JSON         ──►     Store in form_schema
(minimal, structural)           Link to concept dict
                                Link to rules tables

                                At request time:
                                GET /api/forms/vitals-001
                                ?locale=sw
                                &patientId=xyz         ──►    Receives fully
                                                               resolved Runtime
                                Enrichment pipeline:           JSON
                                + inject concept labels
                                + inject reference ranges      Widget tree
                                + inject alert rules           is built from
                                + inject patient history       "type" keys
                                + apply program filters        in the JSON
                                                       ──►
                                                               Renderer draws
                                                               the form

                                At submission time:
                                                       ◄──    Posts obs values
                                Validate against               keyed by
                                concept data types             concept IDs
                                Store to EAV tables
                                Trigger mart refresh
```

------

### The Key Point for Your IT Team

Your devs need to know three things and three things only when authoring a form definition. First, what `type` values are available in the widget registry — they pick from that list. Second, what CIEL concept codes map to the clinical data points they are collecting — they get those from the terminology service UI. Third, what the page, section, and question structure should be — that is the clinical design decision.

They do not write reference ranges, alert thresholds, translated labels, or storage logic. Those are resolved automatically by the backend enrichment pipeline because the concept code is the key that unlocks all of that configuration from the database. The form definition JSON is intentionally kept thin so that clinical knowledge can be governed centrally and updated without touching any form definition file.