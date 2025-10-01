# Documentation on the GetNutritionalInfo API: Retrieving Nutritional Information

## Overview
The GetNutritionalInfo API is an interface that allows developers to retrieve nutritional information for specific food items. By sending generic HTTP requests with the food identifier and the user's API key, the API returns a JSON object containing nutrients for the requested food. It provides both macronutrient (e.g., calories, protein, fat) and micronutrient (e.g., vitamins, minerals) data.

## Table of Contents
- [Overview](#overview)
- [Getting Started](#getting-started)
  - [Endpoint](#endpoint)
  - [Parameters](#parameters)
  - [Authentication](#authentication)
  - [Response](#response)
- [Error Handling](#error-handling)
- [Rate Limits](#rate-limits)
- [Example Usage](#example-usage)

## Getting Started
To use this API, make an HTTP GET request to the endpoint with the required parameters and the user’s API key in the header. The API will return structured JSON containing the food item and its nutritional breakdown.

### Endpoint

Production Endpoint: 
```http
GET https://api.VitalicX.com/food/getNutritionalInfo?foodId={foodId}
```

Development Endpoint (used during testing): 
```http
GET https://api.VitalicX.com/dev/food/getNutritionalInfo?foodId={foodId}
```


### Parameters
| Name                    | Type   | Required                 | Definition                      |
|-------------------------|--------|--------------------------|----------------------------------|
| foodId                  | string | Yes                      | A unique string assigned to each food item in the database, used to retrieve its nutritional information via the API.   |
| includeMicronutrients   | bool   | No (default = true)      | A true/false parameter that determines whether all micronutrients, such as vitamins and minerals, are included in the nutritionalInfo array. |



### Authentication
Each user has their own API key under ```user.apiKey```. Include your API key in the Authorization header as a Bearer token. EX:

```http
Authorization: Bearer {user.apiKey}
```

### Response
For a valid request, the API returns a JSON object containing basic food information. The nutritionalInfo field is an array of nutrient objects, each containing the nutrient ID, name, unit, and amount.



**Response Fields:**
| Field                              | Type    | Definition                                             |
|------------------------------------|---------|---------------------------------------------------------|
| foodId                             | string  | Unique identifier of the food.                          |
| name                               | string  | The name of the food.                                   |
| createdAt                          | string  | The date and time the food was created.                 |
| updatedAt                          | string  | The last date and time the food was updated or changed. |
| nutritionalInfo                    | array   | An array of objects containing nutritional information. |
| nutritionalInfo[0].nutritionId     | string  | The unique identifier of the nutrient.                  |
| nutritionalInfo[0].name            | string  | The name of the nutrient.                               |
| nutritionalInfo[0].unit            | string  | The unit of the nutrient.                               |
| nutritionalInfo[0].amount          | double  | The amount of the nutrient.                             |

**Notes**:
- **Nutrients**:
    - Calorie
    - Protein
    - Carbohydrate
    - Total Fat

- **Micronturients**:
    - Saturated Fat
    - Trans Fat
    - Fiber
    - Sugar
    - Sodium
    - Cholesterol
    - Calcium
    - Potassium
    - Iron
    - Zinc
    - Vitamin A
    - Vitamin B
    - Vitamin C
    - Vitamin D
    - Vitamin E
    - Vitamin K
- The date and time will always be in standard UTC and formatted as ```YYYY-MM-DDTHH:MM:SSZ```
- Possible units are: ```g, kcal, mg, RAE, µg```
- Values are rounded to two decimal places unless unnecessary.

#### Example Response JSON:
```json
{
    "foodId": "123",
    "name": "apple",
    "createdAt": "2025-01-15T08:30:00Z",
    "updatedAt": "2025-09-25T17:45:22Z",
    "nutritionalInfo": [
        {
            "nutritionId": "321",
            "name": "Calorie",
            "unit": "kcal",
            "amount": 95.0
        },
        {
            "nutritionId": "789",
            "name": "Protein",
            "unit": "g",
            "amount": 0.5
        },
        {
            "nutritionId": "790",
            "name": "Carbohydrate",
            "unit": "g",
            "amount": 25
        },
        {
            "nutritionId": "791",
            "name": "Total Fat",
            "unit": "g",
            "amount": 0.3
        },
        // Micronutrients if the includeMicronutrients parameter is true ...
    ]
}
```





## Error Handling
The API uses standard HTTP status codes to indicate success or failure.

| Status | Meaning                             |
|--------|-------------------------------------|
| 200    | Request Successful                  |
| 400    | Invalid Input                       |
| 401    | Unauthorized (missing credentials)  |
| 404    | Not Found (resource does not exist) |
| 429    | Too Many Requests                   |
| 500    | Internal Server Error               |

#### Example Error Responses:

```JSON
{
    "error": {
        "status": 401,
        "message": "Unauthorized request",
        "details": "User is not authorized to request this information"
    }
}
```

```JSON
{
    "error": {
        "status": 429,
        "message": "Too Many Requests",
        "details": "You have exceeded the API request limit of 10 requests per second. Please wait and try again."
    }
    
}
```


## Rate Limits
- 10 requests per second per user.
- Exceeding this limit returns 429 Too Many Requests.


## Example Usage

Here’s a JavaScript example using fetch. This example fetches the nutrition data for an apple and will print the food’s name, timestamps, and a readable list of nutrients and their amounts.

```javascript

async function getNutritionalInfo(foodId, apiKey) {
  const response = await fetch(`https://api.VitalicX.com/food/getNutritionalInfo?foodId=${foodId}`, {
    method: "GET",
    headers: {
      "Authorization": `Bearer ${apiKey}`,
      "Content-Type": "application/json"
    }
  });

  if (!response.ok) {
    throw new Error(`Request failed with status ${response.status}`);
  }

  const data = await response.json();

  console.log("Food:", data.name);
  console.log("Created At:", data.createdAt);
  console.log("Updated At:", data.updatedAt);

  console.log("Nutritional Info:");
  data.nutritionalInfo.forEach(nutrient => {
    console.log(`- ${nutrient.name}: ${nutrient.amount} ${nutrient.unit}`);
  });

  return data;
}

// Example usage:
getNutritionalInfo("23657", "USER_API_KEY_HERE")
  .then(data => console.log("Full Response:", data))
  .catch(err => console.error("Error:", err.message));
```
