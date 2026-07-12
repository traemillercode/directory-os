# Google Gemini Integration Guide for DirectoryOS

## Overview

Google Gemini is integrated into DirectoryOS as a complementary AI model to Claude, providing:
- Alternative perspectives on content generation
- Specialized capabilities for specific tasks
- Fallback when Claude API is rate-limited
- Cost optimization through model selection

## Integration Points

### 1. Comparative Description Generation

**Use Case**: User wants to see multiple AI perspectives on listing descriptions

**Flow**:
1. User clicks "Generate Alternatives"
2. System calls both Claude and Gemini APIs in parallel
3. Display side-by-side comparison
4. User selects preferred version
5. Use Claude or Gemini for future generations (preference-based)

**Prompt Engineering**:

```typescript
// Claude Prompt
const claudePrompt = `
You are an expert marketing copywriter.
Generate a compelling business description that:
- Is 150-300 words
- Includes relevant keywords
- Has a strong call-to-action
- Matches the ${tone} tone
- Focuses on customer benefits

Business: ${businessName}
Category: ${category}
Services: ${services}
`;

// Gemini Prompt (slightly different angle)
const geminiPrompt = `
You are a persuasive copywriter for small businesses.
Write a business description that:
- Connects emotionally with customers
- Showcases unique value proposition
- Is 150-300 words
- Naturally includes industry keywords
- Has a strong, specific call-to-action
- Reads naturally and authentically

Business: ${businessName}
Category: ${category}
What makes this business unique: ${uniqueValue}
`;
```

**Comparative Strengths**:
- **Claude**: More structured, keyword-optimized, formulaic
- **Gemini**: More creative, emotionally engaging, unique angles

### 2. Recommendation & Optimization

**Use Case**: Recommend improvements based on industry standards

**Gemini Capabilities**:
- Analyze competitor listings (via description input)
- Suggest differentiation strategies
- Identify gaps compared to industry benchmarks
- Generate growth recommendations

**Prompt**:
```typescript
const recommendationPrompt = `
You are a business growth consultant.
Analyze this business profile and suggest specific, actionable improvements:

Business: ${businessName}
Description: ${description}
Category: ${category}
Current leads/month: ${leads}
Current view count: ${views}

Provide:
1. Top 3 competitive advantages to emphasize
2. 3 keywords that competitors rank for (but you don't)
3. Content gap analysis (what's missing)
4. Growth recommendations (what could improve leads)
5. Unique positioning strategy

Be specific and data-driven where possible.
`;
```

### 3. Natural Language Interactions

**Use Case**: Conversational AI for user assistance

**Gemini Advantages**:
- Better conversational flow
- Handles follow-up questions naturally
- Multimodal (text + image analysis planned)

**Implementation**:
```typescript
// Support chatbot context
const systemMessage = `
You are a helpful DirectoryOS assistant.
You help users:
- Optimize their business listings
- Improve their online presence
- Capture more leads
- Understand analytics

You are knowledgeable about SEO, marketing, and small business growth.
Always be friendly, encouraging, and provide specific advice.
`;
```

### 4. Content Analysis & Insights

**Use Case**: Deep analysis of listing performance

**Analysis Tasks**:
- Sentiment analysis of reviews/feedback
- Competitor comparison
- Industry trend analysis
- Content quality benchmarking

**Prompt Example**:
```typescript
const analysisPrompt = `
Analyze this business listing and provide insights:

Listing: ${listingName}
Description: ${description}
Category: ${category}
Recent feedback: ${feedback}
Current metrics: Views ${views}, Leads ${leads}

Provide:
1. Strengths (what's working)
2. Weaknesses (what needs improvement)
3. Competitive positioning
4. Industry trend analysis
5. Specific improvement roadmap
6. Success metrics to track

Use industry benchmarks where relevant.
`;
```

## API Integration

### Setup

```typescript
// lib/ai/gemini.ts
import { GoogleGenerativeAI } from '@google/generative-ai';

const genAI = new GoogleGenerativeAI(process.env.GOOGLE_API_KEY!);

export const geminiModel = genAI.getGenerativeModel({
  model: 'gemini-1.5-pro',
});

export async function generateWithGemini(
  prompt: string,
  options?: GenerationOptions
): Promise<string> {
  try {
    const result = await geminiModel.generateContent({
      contents: [{ role: 'user', parts: [{ text: prompt }] }],
      generationConfig: {
        maxOutputTokens: options?.maxTokens || 1024,
        temperature: options?.temperature || 0.7,
      },
    });

    const response = result.response.text();
    return response;
  } catch (error) {
    console.error('Gemini generation error:', error);
    throw new Error('Failed to generate content with Gemini');
  }
}
```

### Rate Limiting

```typescript
// Rate limits (Phase 1)
const GEMINI_LIMITS = {
  requests_per_minute: 60,
  requests_per_day: 1000,
  tokens_per_minute: 1000000,
};

// Fallback logic
if (isClaudeRateLimited()) {
  // Use Gemini as fallback
  return generateWithGemini(prompt);
}
```

## Cost Optimization

### Model Selection Strategy

```typescript
interface AITask {
  type: 'description' | 'analysis' | 'recommendation' | 'conversation';
  complexity: 'low' | 'medium' | 'high';
  priority: 'fast' | 'quality' | 'balanced';
}

function selectAIModel(task: AITask): 'claude' | 'gemini' {
  // Use cheaper models for simple tasks
  if (task.type === 'description' && task.complexity === 'low') {
    return 'gemini'; // Cheaper
  }

  // Use Claude for complex analysis
  if (task.type === 'analysis' && task.complexity === 'high') {
    return 'claude'; // Better quality
  }

  // Default to Claude for consistency
  return 'claude';
}
```

### Cost Tracking

```typescript
interface AIUsage {
  model: 'claude' | 'gemini';
  tokens_used: number;
  cost_usd: number;
  timestamp: Date;
}

// Log all AI usage for cost analysis
async function trackAIUsage(usage: AIUsage) {
  await db.aiUsageLog.create({ data: usage });
}
```

## User Experience

### Disclosure & Transparency

```typescript
// Always disclose AI-generated content
const disclaimerText = `
This description was generated by AI. 
Please review and personalize before publishing.
`;

// Option to see which model generated content
<div className="text-xs text-gray-500">
  Generated by: {model === 'claude' ? 'Claude AI' : 'Google Gemini'}
</div>
```

### Comparative UI

```tsx
// Show side-by-side comparison
<div className="grid grid-cols-2 gap-4">
  <div>
    <h3>Claude's Version</h3>
    <p>{claudeDescription}</p>
    <button onClick={() => selectVersion('claude')}>Use this</button>
  </div>
  <div>
    <h3>Gemini's Version</h3>
    <p>{geminiDescription}</p>
    <button onClick={() => selectVersion('gemini')}>Use this</button>
  </div>
</div>
```

## Future Enhancements (Phase 2+)

### Multimodal Analysis

```typescript
// Gemini can analyze images
export async function analyzeListingImages(
  imageUrls: string[]
): Promise<ImageAnalysis> {
  const prompt = `
  Analyze these listing images:
  - Image quality assessment
  - Professional appearance
  - Recommendations for improvement
  - Emotional impact
  `;

  // Gemini's multimodal capabilities
  const result = await geminiModel.generateContent({
    contents: [
      {
        role: 'user',
        parts: [
          { text: prompt },
          ...imageUrls.map(url => ({ inline_data: { data: url } })),
        ],
      },
    ],
  });

  return parseAnalysis(result.response.text());
}
```

### Real-time Suggestions

```typescript
// Streaming responses for real-time feedback
export async function streamRecommendations(
  listing: Listing,
  onChunk: (chunk: string) => void
) {
  const stream = await geminiModel.generateContentStream({
    contents: [{ role: 'user', parts: [{ text: generatePrompt(listing) }] }],
  });

  for await (const chunk of stream) {
    onChunk(chunk.text());
  }
}
```

## Best Practices

✅ **DO**:
- Use Gemini for creative/alternative perspectives
- Use Claude for consistent, quality-assured output
- Always show which model generated content
- Allow users to choose preferred model
- Monitor costs and adjust strategy quarterly
- Test outputs before publishing to users

❌ **DON'T**:
- Hide which model generated content
- Use Gemini output without review
- Mix Gemini and Claude without explanation
- Over-rely on AI without human review
- Publish AI content as user's own work (always disclose)

## Monitoring & Evaluation

### Quality Metrics

```typescript
interface AIQualityMetric {
  model: 'claude' | 'gemini';
  task_type: string;
  user_satisfaction: number; // 1-5
  adoption_rate: number; // % of users who use output
  revision_rate: number; // % needing edits
}
```

### When to Switch Models

- Gemini has >80% adoption for task type → optimize costs
- Claude has >90% adoption → prioritize quality
- Feedback shows Gemini better for category X → use by default
- Cost becomes prohibitive → shift to Gemini

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Gemini API rate limited | Fall back to Claude |
| Gemini generating low quality | Adjust temperature, use better prompt |
| Cost too high | Use Gemini for more tasks, monitor model selection |
| User prefers Claude | Store preference, use for future generations |
