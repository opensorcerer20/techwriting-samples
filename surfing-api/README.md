# Surf Report API

## Overview

We have added an API endpoint for surfers who want to check things like tide and wave conditions to determine whether they should head out to their favorite beach to surf. The new endpoint is `/surfreport/{beachId}`. The `{beachId}` is retrieved from a list of beaches on our site.

---

## Request

Base request using a beach ID:

```
https://api.openweathermap.org/com/surfreport/{beachId}
```

Some beaches are available by name at https://example.com/surfreport/beaches_available

> **Note:** When including the beach name in the URL, it must be URL encoded, e.g. spaces must be replaced with `+`

Base request using a supported beach name "Cool Beach":

```
https://api.openweathermap.org/com/surfreport/Cool+Beach
```

You may add additional parameters to the request by appending them to the base URL. For example, when using a beach ID of `123` and adding a specified number of `2` days to return, the request URL would be:

```
https://api.openweathermap.org/com/surfreport/123?&days=2
```

### Request Parameters

| Parameter | Description | Default | Valid values |
|-----------|-------------|---------|--------------|
| `days` | Number of days to fetch | `3` | `1`–`7` |
| `units` | Unit system for response values | `imperial` | `imperial`, `metric` |
| `hour` | Specific time local to the beach, in 24-hour format (e.g. `1400` for 2pm). If omitted, all hours are returned for each day. | All hours | Any valid 24-hour time as a 4-digit string |

If the query is malformed, you will receive error code `400` and an indication of the error.

---

## Response

| Field | Description | Imperial units | Metric units |
|-------|-------------|---------------|--------------|
| `surfheight` | Wave height | feet | centimeters |
| `wind` | Wind speed | kts | kph |
| `tide` | Tide level. Negative values indicate incoming tide. | feet | centimeters |
| `watertemp` | Water temperature | °F | °C |

The response also includes a `recommendation` field returns one of three strings based on an algorithm that scores current conditions against an optimal surfing rubric:

- `"Go surfing!"`
- `"Surfing conditions okay, not great"`
- `"Not today -- try some other activity."`

---

## Limitations

An error result that is not `400` is an internal error. Causes include an internal issue with our software or a problem fetching information from our data providers.

> ⚠️ **Safety notice:** Riptides are not included in recommendations, and no guarantees are given regarding riptide safety. Exercise caution.

---

## Edge Cases

The following inputs will return a `400` error:

- Beach ID not recognized
- Beach name not recognized or not in proper URL encoding
- `days` less than `1` or greater than `7`
- `units` value other than `"imperial"` or `"metric"`
- `hour` not 4 digits or not a valid 24-hour time

### Cases Where Behavior Is Unknown

- A request is made with both a valid beach ID and a known beach name
- The data source used by our API has fewer days available than the number requested

---

## Use Cases

### Use Case 1

Get a surf report for:

- Beach with ID `123`
- Next `2` days
- Metric units
- `2pm` only

Base URL for beach ID `123`:

```
https://api.openweathermap.org/com/surfreport/123
```

Full URL with `days`, `units`, and `hour` parameters:

```
https://api.openweathermap.org/com/surfreport/123?&days=2&units=metric&hour=1400
```

Sample output:

```json
{
    "surfreport": [
        {
            "beach": "Santa Cruz",
            "monday": {
                "2pm": {
                    "tide": -1,
                    "wind": 1,
                    "watertemp": 30,
                    "surfheight": 1,
                    "recommendation": "Surfing conditions are okay, not great"
                }
            },
            "tuesday": {
                "2pm": {
                    "tide": -1,
                    "wind": 1,
                    "watertemp": 30,
                    "surfheight": 1,
                    "recommendation": "Surfing conditions are okay, not great"
                }
            }
        }
    ]
}
```
