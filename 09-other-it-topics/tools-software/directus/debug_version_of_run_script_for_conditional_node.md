# Debug Version of Run Script for Conditional Node

```js
module.exports = async function(data) {
    // Debug: Log the entire data object to see what's available
    console.log('Full data object:', JSON.stringify(data, null, 2));

    // Try different ways to access the previous operation data
    const method1 = data['read-active-patient'];
    const method2 = data.$last;
    const method3 = data.read_active_patient; // underscore version

    console.log('Method 1 (data[operation-name]):', method1);
    console.log('Method 2 (data.$last):', method2);
    console.log('Method 3 (data.operation_name):', method3);

    // Try to find the actual data
    let activePatientData = method1 || method2 || method3;

    console.log('Active patient data found:', activePatientData);
    console.log('Is array?', Array.isArray(activePatientData));
    console.log('Length:', activePatientData ? activePatientData.length : 'undefined');

    // Check if we got results
    if (!activePatientData || !Array.isArray(activePatientData) || activePatientData.length === 0) {
        console.log('RETURNING FALSE - No patient data found');
        return {
            patient_found: false,
            error_message: 'Active patient not found or inactive'
        };
    }

    console.log('RETURNING TRUE - Patient data found');
    return {
        patient_found: true,
        patient_data: activePatientData[0]
    };
};
```

