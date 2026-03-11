# Age Calculator Flow Function Only

## Instructions

I'll help you create a Directus Flow operation that validates a date of birth parameter and calculates the precise age. Here's how to implement this using Directus's "Run Script" operation:## Implementation Guide for Directus Flow

### Setting up the Flow:

1. **Create a new Flow in Directus:**
   - Navigate to Settings → Flows
   - Click "Create Flow"
   - Set trigger type to "Webhook" or "Manual"
2. **Configure the Trigger:**
   - If using Webhook: Set method to `GET`
   - The webhook URL will be: `https://your-directus-instance.com/flows/trigger/[flow-id]`
3. **Add the Run Script Operation:**
   - Add operation → "Run Script"
   - Paste the provided code
   - Set operation name to "Calculate Age"

### Usage Examples:

**Valid Request:**

```
GET /flows/trigger/your-flow-id?date_of_birth=1990-05-15
```

**Response:**

```json
{
  "success": true,
  "input": {
    "date_of_birth": "1990-05-15",
    "calculation_date": "2024-07-21"
  },
  "age": {
    "years": 34,
    "months": 2,
    "days": 6,
    "formatted": "34 years, 2 months, and 6 days",
    "total_days": 12485
  },
  "details": {
    "born_on": "Tue May 15 1990",
    "calculated_on": "Sun Jul 21 2024",
    "is_adult": true,
    "next_birthday": {
      "date": "Thu May 15 2025",
      "days_until": 298,
      "is_today": false
    }
  }
}
```

**Error Response (Invalid Format):**

```json
{
  "success": false,
  "error": {
    "message": "Invalid date format. Expected format: YYYY-MM-DD",
    "code": "VALIDATION_ERROR",
    "timestamp": "2024-07-21T10:30:00.000Z"
  },
  "input": {
    "date_of_birth": "15-05-1990"
  }
}
```

## Key Features:

1. **Comprehensive Validation:**
   - Checks parameter existence
   - Validates YYYY-MM-DD format
   - Ensures valid date
   - Prevents future dates
2. **Precise Age Calculation:**
   - Accounts for years, months, and days
   - Handles edge cases (month/day boundaries)
   - Provides total days lived
3. **Rich Response Data:**
   - Formatted age string
   - Additional details (next birthday, adult status)
   - Consistent error handling
4. **Microservices Best Practices:**
   - Structured error responses
   - Input validation
   - Detailed logging information
   - Stateless operation

This implementation follows microservices principles by being self-contained, providing clear API contracts, and handling errors gracefully. The script can be easily integrated into your SaaS application's user management or profile services.

## Function

- `age-calculator` Run Script Operation

```js
// Directus Flow - Run Script Operation: Age Calculator
// This script validates date_of_birth parameter and calculates precise age

module.exports = async function(data) {
    const { $trigger, $accountability, $env } = data;

    try {
        // 1. Check if date_of_birth parameter exists
        const dateOfBirth = $trigger.query?.date_of_birth || $trigger.body?.date_of_birth;

        if (!dateOfBirth) {
            throw new Error('Missing required parameter: date_of_birth');
        }

        // 2. Validate date format (YYYY-MM-DD)
        const dateRegex = /^\d{4}-\d{2}-\d{2}$/;
        if (!dateRegex.test(dateOfBirth)) {
            throw new Error('Invalid date format. Expected format: YYYY-MM-DD');
        }

        // 3. Parse and validate the date
        const birthDate = new Date(dateOfBirth);
        const today = new Date();

        // Check if the parsed date is valid
        if (isNaN(birthDate.getTime())) {
            throw new Error('Invalid date provided. Please provide a valid date in YYYY-MM-DD format');
        }

        // Check if birth date is not in the future
        if (birthDate > today) {
            throw new Error('Date of birth cannot be in the future');
        }

        // 4. Calculate precise age (years, months, days)
        let years = today.getFullYear() - birthDate.getFullYear();
        let months = today.getMonth() - birthDate.getMonth();
        let days = today.getDate() - birthDate.getDate();

        // Adjust for negative days
        if (days < 0) {
            months--;
            // Get the last day of the previous month
            const lastMonth = new Date(today.getFullYear(), today.getMonth(), 0);
            days += lastMonth.getDate();
        }

        // Adjust for negative months
        if (months < 0) {
            years--;
            months += 12;
        }

        // 5. Calculate total days lived (optional additional info)
        const timeDiff = today.getTime() - birthDate.getTime();
        const totalDays = Math.floor(timeDiff / (1000 * 3600 * 24));

        // 6. Format response
        const ageResponse = {
            success: true,
            input: {
                date_of_birth: dateOfBirth,
                calculation_date: today.toISOString().split('T')[0]
            },
            age: {
                years: years,
                months: months,
                days: days,
                formatted: `${years} years, ${months} months, and ${days} days`,
                total_days: totalDays
            },
            details: {
                born_on: birthDate.toDateString(),
                calculated_on: today.toDateString(),
                is_adult: years >= 18,
                next_birthday: calculateNextBirthday(birthDate, today)
            }
        };

        return ageResponse;

    } catch (error) {
        // Return error response in consistent format
        return {
            success: false,
            error: {
                message: error.message,
                code: 'VALIDATION_ERROR',
                timestamp: new Date().toISOString()
            },
            input: {
                date_of_birth: $trigger.query?.date_of_birth || $trigger.body?.date_of_birth || null
            }
        };
    }
};

// Helper function to calculate next birthday
function calculateNextBirthday(birthDate, today) {
    const currentYear = today.getFullYear();
    let nextBirthday = new Date(currentYear, birthDate.getMonth(), birthDate.getDate());

    // If birthday has already passed this year, set it to next year
    if (nextBirthday < today) {
        nextBirthday.setFullYear(currentYear + 1);
    }

    // Calculate days until next birthday
    const daysUntil = Math.ceil((nextBirthday - today) / (1000 * 3600 * 24));

    return {
        date: nextBirthday.toDateString(),
        days_until: daysUntil,
        is_today: daysUntil === 0
    };
}
```

