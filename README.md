# Holiday Checker n8n Workflow

This n8n workflow fetches public holidays from the Nager.Date API and provides detailed information about each holiday including dates, duration, and days remaining from today.

## Features

- 🌍 **Multi-country support** - Works with any country code supported by Nager.Date API
- 📅 **Date calculations** - Shows days until upcoming holidays and days since past holidays
- 📊 **Summary statistics** - Provides overview of total, upcoming, and past holidays
- 🎯 **Next holiday info** - Highlights the next upcoming holiday
- 📱 **RESTful API** - Easy to integrate with webhooks or direct API calls

## Workflow Components

### 1. Webhook Trigger
- **Type**: Webhook
- **Method**: GET
- **Path**: `/holidays`
- **Purpose**: Entry point for the workflow

### 2. Fetch Holidays
- **Type**: HTTP Request
- **API**: Nager.Date API v3
- **URL**: `https://date.nager.at/api/v3/PublicHolidays/{year}/{countryCode}`
- **Purpose**: Retrieves holiday data from external API

### 3. Process Holidays
- **Type**: Code (JavaScript)
- **Purpose**: Calculates days until/since holidays and formats data

### 4. Format Response
- **Type**: Code (JavaScript)
- **Purpose**: Creates structured response with summary and detailed holiday list

### 5. Respond to Webhook
- **Type**: Respond to Webhook
- **Purpose**: Returns formatted JSON response

## Setup Instructions

### 1. Import the Workflow

1. Open your n8n instance
2. Click on "Workflows" in the sidebar
3. Click "Import from File" or "Import from URL"
4. Upload the `holiday-checker-workflow.json` file
5. The workflow will be imported with all nodes configured

### 2. Activate the Workflow

1. Click the "Active" toggle in the top-right corner
2. The webhook URL will be generated automatically
3. Copy the webhook URL for testing

### 3. Test the Workflow

#### Basic Usage (Default: US holidays for current year)
```bash
curl -X GET "https://your-n8n-instance.com/webhook/holidays"
```

#### With Parameters (Custom year and country)
```bash
curl -X GET "https://your-n8n-instance.com/webhook/holidays" \
  -H "Content-Type: application/json" \
  -d '{
    "year": 2024,
    "countryCode": "GB"
  }'
```

## API Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `year` | number | Current year | Year to fetch holidays for |
| `countryCode` | string | "US" | ISO 3166-1 alpha-2 country code |

## Response Format

```json
{
  "summary": {
    "totalHolidays": 10,
    "upcomingHolidays": 7,
    "pastHolidays": 3,
    "todayHolidays": 0,
    "nextHoliday": {
      "name": "New Year's Day",
      "date": "Monday, January 1, 2024",
      "daysLeft": 15
    }
  },
  "holidays": [
    {
      "name": "New Year's Day",
      "date": "2024-01-01",
      "formattedDate": "Monday, January 1, 2024",
      "status": "upcoming",
      "daysInfo": "15 days left",
      "daysUntil": 15,
      "daysSince": -15,
      "isFixed": true,
      "isGlobal": true,
      "types": ["Public"]
    }
  ]
}
```

## Supported Countries

The workflow supports all countries available in the Nager.Date API. Some popular country codes:

- `US` - United States
- `GB` - United Kingdom
- `CA` - Canada
- `AU` - Australia
- `DE` - Germany
- `FR` - France
- `IT` - Italy
- `ES` - Spain
- `JP` - Japan
- `IN` - India

For a complete list, visit: https://date.nager.at/Country

## Holiday Status Types

- **`upcoming`** - Holiday is in the future
- **`today`** - Holiday is today
- **`past`** - Holiday has already occurred

## Customization Options

### Modify Default Country/Year
Edit the HTTP Request node URL:
```
https://date.nager.at/api/v3/PublicHolidays/{{ $json.year || 2024 }}/{{ $json.countryCode || 'GB' }}
```

### Add Additional Data Processing
Modify the "Process Holidays" Code node to include:
- Holiday duration calculations
- Business day calculations
- Custom formatting
- Additional metadata

### Change Response Format
Modify the "Format Response" Code node to:
- Change JSON structure
- Add/remove fields
- Include additional statistics
- Format for specific use cases

## Error Handling

The workflow includes basic error handling:
- Invalid country codes return appropriate error messages
- Network failures are handled gracefully
- Missing parameters use sensible defaults

## Performance Considerations

- The Nager.Date API is free but has rate limits
- Consider caching responses for high-traffic applications
- The workflow processes data efficiently with minimal memory usage

## Troubleshooting

### Common Issues

1. **No holidays returned**
   - Check if the country code is valid
   - Verify the year is supported by the API

2. **Webhook not responding**
   - Ensure the workflow is activated
   - Check n8n instance is running
   - Verify webhook URL is correct

3. **Date calculations incorrect**
   - Check timezone settings in n8n
   - Verify date format compatibility

### Debug Mode

Enable debug mode in n8n to see detailed execution logs:
1. Go to workflow settings
2. Enable "Save execution progress"
3. Run the workflow and check execution logs

## License

This workflow is provided as-is for educational and commercial use. The Nager.Date API is a free service - please respect their terms of use.

## Support

For issues with:
- **n8n**: Check the [n8n documentation](https://docs.n8n.io/)
- **Nager.Date API**: Visit [their documentation](https://date.nager.at/)
- **This workflow**: Create an issue in the repository
