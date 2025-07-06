# Real Estate Agent Performance Analyzer

A self-contained MVP landing page that analyzes real estate agent performance by city and agent type (Buyer/Seller).

## Features

- **Simple Form Interface**: Enter city name and select agent type
- **Real-time Analysis**: Connects to n8n webhook for data processing
- **Visual Results**: Displays performance score, chart visualization, and summary
- **Responsive Design**: Mobile-first approach with max-width 600px
- **No Dependencies**: Pure vanilla JavaScript (ES6+) with Chart.js via CDN

## File Structure

```
real-estate-analyzer/
├── index.html          # All-in-one HTML file with inline CSS and JavaScript
├── README.md          # This file
└── .gitignore         # Git ignore file (optional)
```

## Step-by-Step Setup Guide

### 1. Create GitHub Repository

```bash
# Create new directory
mkdir real-estate-analyzer
cd real-estate-analyzer

# Initialize Git
git init

# Create index.html and copy the provided code
touch index.html
# Copy the HTML content into index.html

# Create README.md
touch README.md
# Copy this README content

# Initial commit
git add .
git commit -m "Initial commit - Real Estate Agent Performance Analyzer"

# Create GitHub repo and push
# Go to github.com, create new repository
git remote add origin https://github.com/YOUR_USERNAME/real-estate-analyzer.git
git push -u origin main
```

### 2. Configure n8n Webhook

#### Option A: Using n8n.cloud (Recommended for beginners)

1. Sign up at [n8n.cloud](https://n8n.cloud)
2. Create a new workflow
3. Add nodes in this order:

**Webhook Node:**
- Trigger: Webhook
- HTTP Method: POST
- Path: `/real-estate-analysis`
- Response Mode: "When last node finishes"
- Response Data: "First item's JSON"

**Tavily Search Node (or HTTP Request):**
- Connect to Webhook
- Search query: `{{$json["city"]}} real estate market {{$json["type"]}} agent performance`
- API Key: Your Tavily API key

**OpenAI Node:**
- Connect to Tavily
- Model: GPT-4 or GPT-3.5
- Prompt:
```
Based on the following real estate market data for {{$json["city"]}}:
{{$json["searchResults"]}}

Analyze the performance potential for a {{$json["type"]}} agent.
Provide:
1. A performance score from 0-100
2. Key metrics for visualization
3. A brief 2-3 sentence summary

Format as JSON:
{
  "score": [0-100],
  "chartData": {
    "type": "bar",
    "labels": ["Metric1", "Metric2", "Metric3"],
    "values": [value1, value2, value3]
  },
  "summary": "Brief analysis summary..."
}
```

**Function Node:**
- Parse and format the OpenAI response
- Code:
```javascript
const aiResponse = JSON.parse($input.item.json.response);
return {
  json: {
    score: aiResponse.score,
    chartData: aiResponse.chartData,
    summary: aiResponse.summary
  }
};
```

4. Activate the workflow
5. Copy the webhook URL (looks like: `https://YOUR_INSTANCE.n8n.cloud/webhook/real-estate-analysis`)

#### Option B: Self-hosted n8n

1. Install n8n on your VPS:
```bash
npm install -g n8n
n8n start
```

2. Access n8n at `http://YOUR_SERVER:5678`
3. Follow the same workflow setup as Option A
4. Configure SSL/domain for production use

### 3. Configure Webhook URL

1. Open `index.html`
2. Find the line: `const N8N_WEBHOOK_URL = '{{N8N_WEBHOOK_URL}}';`
3. Replace with your actual webhook URL:
```javascript
const N8N_WEBHOOK_URL = 'https://your-instance.n8n.cloud/webhook/real-estate-analysis';
```

### 4. Deploy to Netlify

#### Option A: Drag & Drop
1. Go to [netlify.com](https://netlify.com)
2. Drag your project folder to the deployment area
3. Your site will be live immediately

#### Option B: GitHub Integration
1. Push your code to GitHub
2. Log in to Netlify
3. Click "New site from Git"
4. Choose your repository
5. Deploy settings:
   - Build command: (leave empty)
   - Publish directory: `.`
6. Click "Deploy site"

### 5. Deploy to GitHub Pages (Alternative)

1. In your GitHub repository, go to Settings
2. Scroll to "Pages" section
3. Source: Deploy from branch
4. Branch: main, folder: / (root)
5. Save and wait for deployment
6. Access at: `https://YOUR_USERNAME.github.io/real-estate-analyzer/`

## API Response Format

The n8n webhook should return JSON in this format:

```json
{
  "score": 85,
  "chartData": {
    "type": "bar",
    "labels": ["Market Share", "Client Satisfaction", "Sales Volume"],
    "values": [75, 90, 88]
  },
  "summary": "The San Francisco buyer's agent market shows strong potential with high demand and competitive commission rates. Recent trends indicate growing opportunities in the luxury segment."
}
```

## Configuration Options

### Chart Types
- `"type": "bar"` - Bar chart showing multiple metrics
- `"type": "radial"` or `"type": "doughnut"` - Circular chart showing single score

### Webhook Security (Optional)
Add authentication to your n8n webhook:
1. In n8n webhook node, enable "Authentication"
2. Add header check in JavaScript:
```javascript
headers: {
    'Content-Type': 'application/json',
    'X-API-Key': 'your-secret-key'
}
```

## Troubleshooting

### CORS Issues
If you encounter CORS errors:
1. In n8n webhook settings, add CORS headers
2. Or use a proxy service like cors-anywhere

### No Results Showing
1. Check browser console for errors
2. Verify webhook URL is correct
3. Test webhook directly with Postman/curl:
```bash
curl -X POST https://your-webhook-url \
  -H "Content-Type: application/json" \
  -d '{"city": "San Francisco", "type": "Buyer"}'
```

### Chart Not Rendering
1. Ensure Chart.js CDN is accessible
2. Check console for JavaScript errors
3. Verify chartData format matches expected structure

## Development Tips

### Local Testing
1. Use VS Code Live Server extension
2. Or Python simple server:
```bash
python -m http.server 8000
```

### API Keys Management
For production, consider:
1. Environment variables in Netlify
2. Netlify Functions for API key protection
3. Backend proxy to hide keys

## Future Enhancements

- [ ] Add more chart types (line, radar)
- [ ] Cache results for repeated queries
- [ ] Add city autocomplete
- [ ] Export results as PDF
- [ ] Historical data comparison
- [ ] Multi-city comparison

## License

MIT License - feel free to use for any purpose

## Support

For issues or questions:
1. Check n8n documentation: https://docs.n8n.io
2. Chart.js documentation: https://chartjs.org
3. Create an issue in the GitHub repository
