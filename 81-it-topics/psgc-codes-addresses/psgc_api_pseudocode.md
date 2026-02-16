# PSGC Data Processing Pseudocode

## Overview

The Philippine Standard Geographic Code (PSGC) data processing happens in two stages:

1. **convert-psgc.ts** - Converts Excel file to JSON
2. **generate-api-data.ts** - Processes JSON and generates API-ready files

------

## PART 1: convert-psgc.ts

### Purpose

Convert Excel PSGC data into a standardized JSON format.

### High-Level Flow

```
START
├─ Load Excel workbook from file path
├─ Select 4th sheet (index 3) named "PSGC"
├─ Convert sheet to JSON array of row objects
├─ Transform each row to standardized format
├─ Write transformed data to psgc.json
└─ END
```

### Detailed Pseudocode

```
FUNCTION convertExcelToJson():
    TRY:
        // 1. LOAD EXCEL FILE
        workbook = readExcelFile(EXCEL_PATH)
        sheet = workbook.getSheet(index: 3)  // Get "PSGC" sheet

        // 2. CONVERT TO RAW JSON
        rawData = convertSheetToJSON(sheet)
        // Creates array where each row becomes an object with column names as keys
        // Example row:
        // {
        //   '10-digit PSGC': '1300000000',
        //   'Name': 'National Capital Region (NCR)',
        //   'Correspondence Code': 130000000,
        //   'Geographic Level': 'Reg',
        //   ...
        // }

        // 3. TRANSFORM EACH ROW
        transformedData = []
        FOR EACH row IN rawData:
            transformedRow = {
                // Extract and rename fields
                psgc10DigitCode: row['10-digit PSGC'] as string OR ''
                name: row['Name'] OR ''
                code: row['Correspondence Code'] as string OR 'Unavailable'

                // Fix geographic level (convert "Bgy" to "Brgy")
                geographicLevel: IF row['Geographic Level'] == 'Bgy'
                                  THEN 'Brgy'
                                  ELSE row['Geographic Level']

                // Optional fields (undefined if not present)
                oldName: row['Old Name'] OR undefined
                cityClass: row['City Class'] OR undefined
                incomeClassification: row['Income\r\nClassification'] OR undefined
                urbanRural: row['Urban / Rural\r\n(based on 2020 CPH)'] OR undefined
                population2020: row[' 2020 Population '] OR 0
                status: row['Status'] OR undefined
            }
            ADD transformedRow TO transformedData

        // 4. WRITE OUTPUT
        createDirectoryIfNotExists(directory of JSON_PATH)
        writeJSONFile(JSON_PATH, transformedData, prettyPrint: true)

        logSuccess("Successfully converted to JSON")

    CATCH error:
        logError("Conversion failed:", error)
        exitProgram(code: 1)
```

### Input Example

```
Excel Row:
┌─────────────────┬────────────────────────────────┬─────────────────────┬──────────────────┐
│ 10-digit PSGC   │ Name                           │ Correspondence Code │ Geographic Level │
├─────────────────┼────────────────────────────────┼─────────────────────┼──────────────────┤
│ 1300000000      │ National Capital Region (NCR) │ 130000000           │ Reg              │
│ 1380100000      │ City of Caloocan               │ 137501000           │ City             │
│ 1380100001      │ Barangay 1                     │ 137501001           │ Bgy              │
└─────────────────┴────────────────────────────────┴─────────────────────┴──────────────────┘
```

### Output Example

```json
[
  {
    "psgc10DigitCode": "1300000000",
    "name": "National Capital Region (NCR)",
    "code": "130000000",
    "geographicLevel": "Reg",
    "population2020": 0
  },
  {
    "psgc10DigitCode": "1380100000",
    "name": "City of Caloocan",
    "code": "137501000",
    "geographicLevel": "City",
    "cityClass": "HUC",
    "incomeClassification": "1st",
    "population2020": 0
  },
  {
    "psgc10DigitCode": "1380100001",
    "name": "Barangay 1",
    "code": "137501001",
    "geographicLevel": "Brgy",
    "urbanRural": "U",
    "population2020": 0
  }
]
```

------

## PART 2: generate-api-data.ts

### Purpose

Process the JSON data and generate individual API endpoint files organized by geographic level.

### High-Level Flow

```
START
├─ Load psgc.json
├─ Process Regions → write regions.json
├─ Process Provinces → write individual files
├─ Process Cities → write individual files
├─ Process Municipalities → write individual files
├─ Process Sub-municipalities → write individual files
├─ Process Barangays → write individual files
└─ END
```

### Detailed Pseudocode

```
FUNCTION generatePSGCData():
    TRY:
        // 1. LOAD DATA
        rawData = readJSONFile('psgc.json')

        // 2. PROCESS REGIONS
        regions = FILTER rawData WHERE geographicLevel == 'Reg'
        processedRegions = MAP regions WITH processRegion
        writeFile('api/regions.json', processedRegions)

        // 3. PROCESS PROVINCES
        provinces = FILTER rawData WHERE geographicLevel == 'Prov'
        processedProvinces = MAP provinces WITH processProvince
        FOR EACH province IN processedProvinces:
            filename = 'api/provinces/' + province.psgc10DigitCode + '.json'
            writeFile(filename, [province])  // Array with single item

        // 4. PROCESS CITIES
        cities = FILTER rawData WHERE geographicLevel == 'City'
        processedCities = MAP cities WITH processCityOrMunicipality
        FOR EACH city IN processedCities:
            filename = 'api/cities/' + city.psgc10DigitCode + '.json'
            writeFile(filename, [city])

        // 5. PROCESS MUNICIPALITIES
        municipalities = FILTER rawData WHERE geographicLevel == 'Mun'
        processedMunicipalities = MAP municipalities WITH processCityOrMunicipality
        FOR EACH municipality IN processedMunicipalities:
            filename = 'api/municipalities/' + municipality.psgc10DigitCode + '.json'
            writeFile(filename, [municipality])

        // 6. PROCESS SUB-MUNICIPALITIES
        subMunicipalities = FILTER rawData WHERE geographicLevel == 'SubMun'
        processedSubMunicipalities = MAP subMunicipalities WITH processSubMunicipality
        FOR EACH subMun IN processedSubMunicipalities:
            filename = 'api/submunicipalities/' + subMun.psgc10DigitCode + '.json'
            writeFile(filename, [subMun])

        // 7. PROCESS BARANGAYS
        barangays = FILTER rawData WHERE geographicLevel == 'Brgy'
        processedBarangays = MAP barangays WITH processBarangay
        FOR EACH barangay IN processedBarangays:
            filename = 'api/barangays/' + barangay.psgc10DigitCode + '.json'
            writeFile(filename, [barangay])

        logSuccess("Successfully generated PSGC JSON files")

    CATCH error:
        logError("Failed to generate PSGC JSON files:", error)
        exitProgram(code: 1)
```

------

## Processing Functions

### PSGC Code Structure

```
PSGC 10-digit code structure:
┌──────┬─────────┬────────┬─────────┬──────────┐
│ RR   │ PPP     │ CC     │ BBB     │ Total    │
├──────┼─────────┼────────┼─────────┼──────────┤
│ 13   │ 801     │ 00     │ 001     │ 10 digits│
└──────┴─────────┴────────┴─────────┴──────────┘
  │      │         │         │
  │      │         │         └─ Barangay (3 digits)
  │      │         └─────────── City/Municipality (2 digits)
  │      └─────────────────────── Province (3 digits)
  └────────────────────────────── Region (2 digits)

Examples:
- Region:           1300000000 (digits 1-2 only)
- Province:         1374600000 (digits 1-5 only)
- City/Mun:         1380100000 (digits 1-7 only)
- Barangay:         1380100001 (all 10 digits)
```

### Helper Function: extractCodes

```
FUNCTION extractCodes(psgcCode):
    // Validate input
    IF length(psgcCode) != 10:
        THROW error("Invalid PSGC code length")

    // Extract hierarchical codes by padding with zeros
    RETURN {
        // First 2 digits + 8 zeros
        regionCode: substring(psgcCode, 0, 2) + "00000000"
        // Example: "1300000000"

        // First 5 digits + 5 zeros
        provinceCode: substring(psgcCode, 0, 5) + "00000"
        // Example: "1374600000"

        // First 7 digits + 3 zeros
        cityMunicipalityCode: substring(psgcCode, 0, 7) + "000"
        // Example: "1380100000"

        // Full code for sub-municipality
        subMunicipalityCode: substring(psgcCode, 0, 7) + "000"
        // Example: "1380100000"
    }
```

### Processing Function: processRegion

```
FUNCTION processRegion(data):
    // Remove unwanted fields
    restData = data WITHOUT {status, urbanRural}

    // Parse region name format: "NAME (ABBREVIATION)"
    pattern = /(.+?)\s*\((.+?)\)/
    match = pattern.match(data.name)

    IF match exists:
        RETURN {
            ...restData,
            regionName: trim(match[1]),     // e.g., "National Capital Region"
            name: trim(match[2])             // e.g., "NCR"
        }
    ELSE:
        // Handle special cases like "MIMAROPA Region"
        nameParts = split(data.name, ' ')
        RETURN {
            ...restData,
            regionName: nameParts[0],        // e.g., "MIMAROPA"
            name: data.name                  // e.g., "MIMAROPA Region"
        }
```

### Processing Function: processProvince

```
FUNCTION processProvince(data):
    // Remove unwanted fields
    restData = data WITHOUT {status, urbanRural}

    // Extract parent codes
    codes = extractCodes(data.psgc10DigitCode)

    RETURN {
        ...restData,
        regionCode: codes.regionCode     // Parent region
    }
```

### Processing Function: processCityOrMunicipality

```
FUNCTION processCityOrMunicipality(data):
    // Remove unwanted fields
    restData = data WITHOUT {status, urbanRural}

    // Extract parent codes
    codes = extractCodes(data.psgc10DigitCode)

    RETURN {
        ...restData,
        regionCode: codes.regionCode,            // Grandparent region
        provinceCode: codes.provinceCode,        // Parent province
        isCapital: (data.status == 'Capital')    // Is this a capital city?
    }
```

### Processing Function: processSubMunicipality

```
FUNCTION processSubMunicipality(data):
    // Remove unwanted fields
    restData = data WITHOUT {status, urbanRural}

    // Extract parent codes
    codes = extractCodes(data.psgc10DigitCode)

    RETURN {
        ...restData,
        regionCode: codes.regionCode,
        provinceCode: false,                     // SubMun doesn't have province
        cityMunicipalityCode: substring(data.psgc10DigitCode, 0, 5) + "00000"
    }
```

### Processing Function: processBarangay

```
FUNCTION processBarangay(data):
    // Remove status from data (keep urbanRural for now)
    restData = data WITHOUT {status}

    // Extract parent codes
    codes = extractCodes(data.psgc10DigitCode)

    // Check if this barangay belongs to a sub-municipality
    hasSubMunicipality = ANY item IN rawData WHERE:
        item.geographicLevel == 'SubMun' AND
        substring(item.psgc10DigitCode, 0, 7) == substring(data.psgc10DigitCode, 0, 7)

    // Process urban/rural indicator
    urbanRuralValue = processUrbanRural(restData.urbanRural)

    // Build base data
    baseData = {
        ...restData,
        // Add "(Pob.)" suffix if poblacion
        name: IF data.status == 'Pob.'
              THEN data.name + " (Pob.)"
              ELSE data.name,

        urbanRural: urbanRuralValue,
        regionCode: codes.regionCode,
        provinceCode: codes.provinceCode,

        // If under submunicipality, use shorter code
        cityMunicipalityCode: IF hasSubMunicipality
                              THEN substring(data.psgc10DigitCode, 0, 5) + "00000"
                              ELSE codes.cityMunicipalityCode,

        isPoblacion: (data.status == 'Pob.')
    }

    // Add subMunicipalityCode only if applicable
    IF hasSubMunicipality:
        RETURN {
            ...baseData,
            subMunicipalityCode: codes.subMunicipalityCode
        }
    ELSE:
        RETURN baseData
```

### Helper Function: processUrbanRural

```
FUNCTION processUrbanRural(value):
    IF value is undefined OR null:
        RETURN undefined

    IF value == 'R':
        RETURN 'Rural'

    IF value == 'U':
        RETURN 'Urban'

    RETURN undefined
```

### Helper Function: writeFile

```
FUNCTION writeFile(relativePath, data):
    // Construct full path
    fullPath = resolve('public/api/', relativePath)

    // Ensure directory exists
    createDirectoryIfNotExists(directoryOf(fullPath))

    // Write JSON with pretty printing
    writeJSONFile(fullPath, data, prettyPrint: true, spaces: 2)
```

------

## Data Flow Example

### Example: Processing Barangay

**Input (from psgc.json):**

```json
{
  "psgc10DigitCode": "1380100001",
  "name": "Barangay 1",
  "code": "137501001",
  "geographicLevel": "Brgy",
  "urbanRural": "U",
  "population2020": 15234,
  "status": "Pob."
}
```

**Processing Steps:**

```
1. extractCodes("1380100001") returns:
   {
     regionCode: "1300000000",
     provinceCode: "1380100000",
     cityMunicipalityCode: "1380100000",
     subMunicipalityCode: "1380100000"
   }

2. Check for submunicipality: false (none found)

3. processUrbanRural("U") returns: "Urban"

4. Build base data with:
   - name: "Barangay 1 (Pob.)" (status is "Pob.")
   - urbanRural: "Urban"
   - isPoblacion: true
   - No subMunicipalityCode (not under submunicipality)
```

**Output (written to api/barangays/1380100001.json):**

```json
[
  {
    "psgc10DigitCode": "1380100001",
    "name": "Barangay 1 (Pob.)",
    "code": "137501001",
    "geographicLevel": "Brgy",
    "urbanRural": "Urban",
    "population2020": 15234,
    "regionCode": "1300000000",
    "provinceCode": "1380100000",
    "cityMunicipalityCode": "1380100000",
    "isPoblacion": true
  }
]
```

------

## Output File Structure

```
public/api/
├── regions.json                    (All regions in one file)
├── provinces/
│   ├── 1374600000.json            (Each province in separate file)
│   ├── 1374800000.json
│   └── ...
├── cities/
│   ├── 1380100000.json            (Each city in separate file)
│   ├── 1380200000.json
│   └── ...
├── municipalities/
│   ├── 1400100000.json            (Each municipality in separate file)
│   └── ...
├── submunicipalities/
│   ├── 1500100000.json            (Each submunicipality in separate file)
│   └── ...
└── barangays/
    ├── 1380100001.json            (Each barangay in separate file)
    ├── 1380100002.json
    └── ...
```

------

## Key Concepts

### 1. Geographic Hierarchy

```
Region
  └── Province
        ├── City
        │     └── Barangay
        ├── Municipality
        │     └── Barangay
        └── Sub-Municipality
              └── Barangay
```

### 2. Code Extraction Pattern

The system uses positional digits to determine hierarchy:

- **Digits 1-2**: Region identifier
- **Digits 3-5**: Province identifier
- **Digits 6-7**: City/Municipality identifier
- **Digits 8-10**: Barangay identifier

### 3. Special Cases

**Regions:**

- Names contain both full name and abbreviation
- Example: "National Capital Region (NCR)"
- System extracts both parts

**Barangays:**

- Can belong to City, Municipality, or Sub-Municipality
- "Poblacion" barangays get "(Pob.)" suffix
- Urban/Rural codes converted: "U" → "Urban", "R" → "Rural"

**Sub-Municipalities:**

- Don't have a province (provinceCode = false)
- Use different parent code structure

### 4. Data Transformation

- Removes transient fields (status, urbanRural in some cases)
- Adds hierarchical relationships (regionCode, provinceCode, etc.)
- Standardizes naming (Bgy → Brgy)
- Enriches data with computed fields (isCapital, isPoblacion)