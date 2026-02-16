# Authorized Age Calculator Function

This script checks for a specific hardcoded set of roleIDs

```js
// Method 1: Using Directus Accountability
// Simple permission check using built-in user context

module.exports = async function(data) {
    const { $trigger, $accountability } = data;

    try {
        // 1. CHECK ACCOUNTABILITY (Built-in Directus user context)
        console.log('Accountability:', $accountability);

        if (!$accountability || !$accountability.user) {
            throw new Error('Authentication required - no user found');
        }

        // Check user role (role is a UUID in Directus)
        const userRoleId = $accountability.role;

        // List your specific role UUIDs that should have access
        const allowedRoleIds = [
            '12345678-1234-1234-1234-123456789012', // Replace with your actual role UUID
            '87654321-4321-4321-4321-210987654321'  // Add more role UUIDs as needed
        ];

        if (!allowedRoleIds.includes(userRoleId)) {
            throw new Error(`Access denied - role not authorized`);
        }

        // 2. VALIDATE INPUT
        const dateOfBirth = $trigger.query?.date_of_birth;

        if (!dateOfBirth) {
            throw new Error('Missing date_of_birth parameter');
        }

        if (!/^\d{4}-\d{2}-\d{2}$/.test(dateOfBirth)) {
            throw new Error('Invalid date format - use YYYY-MM-DD');
        }

        // 3. CALCULATE AGE
        const birthDate = new Date(dateOfBirth);
        const today = new Date();

        if (isNaN(birthDate.getTime())) {
            throw new Error('Invalid date provided');
        }

        if (birthDate > today) {
            throw new Error('Birth date cannot be in the future');
        }

        let years = today.getFullYear() - birthDate.getFullYear();
        let months = today.getMonth() - birthDate.getMonth();
        let days = today.getDate() - birthDate.getDate();

        // Adjust for negative days
        if (days < 0) {
            months--;
            const lastMonth = new Date(today.getFullYear(), today.getMonth(), 0);
            days += lastMonth.getDate();
        }

        // Adjust for negative months
        if (months < 0) {
            years--;
            months += 12;
        }

        // Calculate total days lived
        const timeDiff = today.getTime() - birthDate.getTime();
        const totalDays = Math.floor(timeDiff / (1000 * 3600 * 24));

        // Calculate next birthday
        const nextBirthday = calculateNextBirthday(birthDate, today);

        // 4. RETURN FULL RESPONSE WITH USER CONTEXT
        return {
            success: true,
            user_info: {
                user_id: $accountability.user,
                role_id: $accountability.role
            },
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
                next_birthday: nextBirthday
            }
        };

    } catch (error) {
        return {
            success: false,
            error: error.message,
            user_info: {
                user_id: $accountability?.user || 'none',
                role_id: $accountability?.role || 'none'
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

