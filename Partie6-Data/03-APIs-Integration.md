# 📖 Partie VI - Chapitre 3 : Interfaçage avec APIs

## 🎯 Objectif du chapitre

Maîtriser l'intégration avec les APIs modernes comme OpenAI, Firecrawl, et autres services tiers pour enrichir les applications avec des capacités d'IA avancées.

## 🤖 OpenAI API : Intégration complète

### 1. 🏗️ OpenAI Integration Architecture

```
Tu es un expert OpenAI API et AI integrations.

TA TÂCHE : Implémenter une intégration OpenAI complète pour [APPLICATION]

INTÉGRATIONS OPENAI :
1. **Chat Completions** : AI conversations, code assistance
2. **Function Calling** : Custom tools, API integration
3. **Embeddings** : Vector search, similarity matching
4. **Moderation** : Content safety, toxicity detection
5. **Assistants** : Persistent AI assistants, knowledge base
6. **Fine-tuning** : Custom model training, domain adaptation

CONTRAINTES :
- API rate limits et cost optimization
- Error handling et retry logic
- Authentication security (API keys)
- Context management et conversation history
- Performance optimization
- User experience

FORMAT DE SORTIE :
1. OpenAI architecture design
2. API integration implementation
3. Function calling setup
4. Error handling strategies
5. Cost optimization
6. Security considerations
7. Performance optimization

QUALITÉ : Intégration OpenAI robuste, optimisée, sécurisée
```

**Architecture d'intégration OpenAI :**
```typescript
// src/services/openai/OpenAIClient.ts
import OpenAI from 'openai';
import { z } from 'zod';

export class OpenAIClient {
  private client: OpenAI;
  private rateLimiter: RateLimiter;
  private cache: Cache;
  private metrics: MetricsCollector;

  constructor(config: OpenAIConfig) {
    this.client = new OpenAI({
      apiKey: config.apiKey,
      organization: config.organization,
      project: config.project,
      timeout: config.timeout || 30000,
      maxRetries: config.maxRetries || 3
    });

    this.rateLimiter = new RateLimiter(config.rateLimits);
    this.cache = new Cache(config.cacheSettings);
    this.metrics = new MetricsCollector();
  }

  async chatCompletion(params: ChatCompletionParams): Promise<ChatCompletionResponse> {
    const cacheKey = this.generateCacheKey(params);

    // Check cache first
    const cachedResponse = await this.cache.get(cacheKey);
    if (cachedResponse) {
      this.metrics.recordCacheHit();
      return cachedResponse;
    }

    // Rate limiting
    await this.rateLimiter.waitForLimit();

    try {
      this.metrics.recordRequestStart();

      const response = await this.client.chat.completions.create({
        model: params.model || 'gpt-4',
        messages: this.buildMessages(params),
        functions: params.functions,
        function_call: params.functionCall,
        temperature: params.temperature || 0.1,
        max_tokens: params.maxTokens || 2000,
        top_p: params.topP || 1,
        frequency_penalty: params.frequencyPenalty || 0,
        presence_penalty: params.presencePenalty || 0,
        stop: params.stop,
        stream: params.stream || false,
        tools: params.tools,
        tool_choice: params.toolChoice
      });

      this.metrics.recordRequestSuccess();

      const result = this.parseResponse(response);

      // Cache successful responses
      if (params.cache !== false) {
        await this.cache.set(cacheKey, result, params.cacheTtl);
      }

      return result;

    } catch (error) {
      this.metrics.recordRequestError(error);
      throw this.handleError(error);
    }
  }

  async createEmbedding(text: string | string[]): Promise<EmbeddingResponse> {
    const cacheKey = `embedding:${Array.isArray(text) ? text.join('|') : text}`;

    // Check cache
    const cached = await this.cache.get(cacheKey);
    if (cached) {
      return cached;
    }

    await this.rateLimiter.waitForLimit();

    try {
      const response = await this.client.embeddings.create({
        model: 'text-embedding-3-small',
        input: text,
        encoding_format: 'float'
      });

      const result = {
        embeddings: response.data.map(d => d.embedding),
        usage: response.usage
      };

      // Cache embeddings (longer TTL since they're static)
      await this.cache.set(cacheKey, result, 24 * 60 * 60 * 1000); // 24 hours

      return result;

    } catch (error) {
      throw this.handleError(error);
    }
  }

  async moderateContent(content: string): Promise<ModerationResponse> {
    try {
      const response = await this.client.moderations.create({
        input: content
      });

      return {
        flagged: response.results[0].flagged,
        categories: response.results[0].categories,
        scores: response.results[0].category_scores
      };

    } catch (error) {
      throw this.handleError(error);
    }
  }

  private buildMessages(params: ChatCompletionParams): ChatCompletionMessageParam[] {
    const messages: ChatCompletionMessageParam[] = [];

    // Add system message if provided
    if (params.systemMessage) {
      messages.push({
        role: 'system',
        content: params.systemMessage
      });
    }

    // Add conversation history
    if (params.conversationHistory) {
      messages.push(...params.conversationHistory);
    }

    // Add current user message
    messages.push({
      role: 'user',
      content: params.message
    });

    return messages;
  }

  private parseResponse(response: any): ChatCompletionResponse {
    const choice = response.choices[0];

    return {
      content: choice.message.content,
      functionCall: choice.message.function_call,
      toolCalls: choice.message.tool_calls,
      usage: {
        promptTokens: response.usage.prompt_tokens,
        completionTokens: response.usage.completion_tokens,
        totalTokens: response.usage.total_tokens,
        cost: this.calculateCost(response.usage, response.model)
      },
      finishReason: choice.finish_reason,
      model: response.model
    };
  }

  private calculateCost(usage: any, model: string): number {
    // Pricing as of 2024 (simplified)
    const pricing: Record<string, { input: number; output: number }> = {
      'gpt-4': { input: 0.03, output: 0.06 },
      'gpt-4-32k': { input: 0.06, output: 0.12 },
      'gpt-3.5-turbo': { input: 0.0015, output: 0.002 },
      'text-embedding-3-small': { input: 0.00002, output: 0 }
    };

    const modelPricing = pricing[model] || pricing['gpt-4'];
    return (usage.prompt_tokens / 1000 * modelPricing.input) +
           (usage.completion_tokens / 1000 * modelPricing.output);
  }

  private generateCacheKey(params: ChatCompletionParams): string {
    const keyData = {
      model: params.model,
      messages: params.conversationHistory?.map(m => m.content).join('|') || '',
      currentMessage: params.message,
      temperature: params.temperature,
      maxTokens: params.maxTokens
    };

    return `chat:${Buffer.from(JSON.stringify(keyData)).toString('base64')}`;
  }

  private handleError(error: any): OpenAIError {
    if (error.code === 'rate_limit_exceeded') {
      return new RateLimitError('OpenAI rate limit exceeded', error);
    }

    if (error.code === 'insufficient_quota') {
      return new QuotaError('OpenAI quota exceeded', error);
    }

    if (error.code === 'invalid_api_key') {
      return new AuthenticationError('Invalid OpenAI API key', error);
    }

    return new OpenAIError('OpenAI API error', error);
  }
}

// Custom error classes
class OpenAIError extends Error {
  constructor(message: string, public originalError: any) {
    super(message);
    this.name = 'OpenAIError';
  }
}

class RateLimitError extends OpenAIError {
  constructor(message: string, originalError: any) {
    super(message, originalError);
    this.name = 'RateLimitError';
  }
}

class QuotaError extends OpenAIError {
  constructor(message: string, originalError: any) {
    super(message, originalError);
    this.name = 'QuotaError';
  }
}

class AuthenticationError extends OpenAIError {
  constructor(message: string, originalError: any) {
    super(message, originalError);
    this.name = 'AuthenticationError';
  }
}
```

### 2. 🔧 Function Calling : Fonctions personnalisées

```
Tu es un expert OpenAI function calling et tool integration.

TA TÂCHE : Implémenter function calling pour [USE CASES]

FONCTIONS À IMPLÉMENTER :
1. **Code Analysis** : Static analysis, complexity metrics
2. **Database Query** : SQL generation, execution, optimization
3. **File Operations** : Read, write, search in codebase
4. **API Testing** : Generate and run API tests
5. **Documentation** : Generate docs from code
6. **Security Scanning** : Vulnerability detection, OWASP compliance

CONTRAINTES :
- Function calling API
- Parameter validation
- Error handling
- Performance optimization
- Security compliance

FORMAT DE SORTIE :
1. Function calling architecture
2. Function definitions
3. Parameter validation
4. Error handling
5. Performance optimization
6. Security considerations
7. Testing strategies

QUALITÉ : Function calling robuste, sécurisé, performant
```

**Exemple de function calling avancé :**
```typescript
// Advanced Function Calling Implementation
class AIFunctionRegistry {
  private functions: Map<string, AIFunction> = new Map();
  private openai: OpenAIClient;

  constructor(openaiClient: OpenAIClient) {
    this.openai = openaiClient;
    this.registerDefaultFunctions();
  }

  registerFunction(functionDef: AIFunction) {
    this.functions.set(functionDef.name, functionDef);
  }

  async executeWithFunctions(
    message: string,
    context: ExecutionContext = {}
  ): Promise<ExecutionResult> {
    const availableFunctions = Array.from(this.functions.values());

    try {
      const response = await this.openai.chatCompletion({
        message,
        functions: availableFunctions.map(f => ({
          name: f.name,
          description: f.description,
          parameters: f.parameters
        })),
        functionCall: 'auto',
        systemMessage: `You are an AI assistant with access to various development tools.
                       Use the available functions to help with development tasks.
                       Always explain your reasoning before calling functions.`
      });

      if (response.functionCall) {
        const functionResult = await this.executeFunction(
          response.functionCall.name,
          response.functionCall.arguments,
          context
        );

        // Get final response with function result
        const finalResponse = await this.openai.chatCompletion({
          message: `Based on the function execution result, provide your analysis:

          Function: ${response.functionCall.name}
          Result: ${JSON.stringify(functionResult)}

          Please provide a comprehensive response based on this information.`,
          conversationHistory: [
            { role: 'user', content: message },
            { role: 'assistant', content: response.content },
            {
              role: 'function',
              name: response.functionCall.name,
              content: JSON.stringify(functionResult)
            }
          ]
        });

        return {
          response: finalResponse.content,
          functionUsed: response.functionCall.name,
          functionResult,
          cost: response.usage.cost + (finalResponse.usage?.cost || 0)
        };
      }

      return {
        response: response.content,
        cost: response.usage.cost
      };

    } catch (error) {
      throw new Error(`Function execution failed: ${error.message}`);
    }
  }

  private async executeFunction(
    functionName: string,
    argumentsJson: string,
    context: ExecutionContext
  ): Promise<any> {
    const func = this.functions.get(functionName);
    if (!func) {
      throw new Error(`Function ${functionName} not found`);
    }

    try {
      const args = JSON.parse(argumentsJson);

      // Validate parameters
      const validationResult = this.validateParameters(func.parameters, args);
      if (!validationResult.valid) {
        throw new Error(`Parameter validation failed: ${validationResult.errors.join(', ')}`);
      }

      // Execute function with context
      return await func.execute(args, context);

    } catch (error) {
      if (error instanceof SyntaxError) {
        throw new Error(`Invalid function arguments: ${error.message}`);
      }
      throw error;
    }
  }

  private validateParameters(schema: any, args: any): ValidationResult {
    try {
      // Simple validation - in production, use Zod or Yup
      if (schema.required) {
        for (const required of schema.required) {
          if (!(required in args)) {
            return { valid: false, errors: [`Missing required parameter: ${required}`] };
          }
        }
      }

      return { valid: true, errors: [] };
    } catch (error) {
      return { valid: false, errors: [error.message] };
    }
  }

  private registerDefaultFunctions() {
    // Database Query Function
    this.registerFunction({
      name: 'execute_database_query',
      description: 'Execute SQL queries and provide analysis',
      parameters: {
        type: 'object',
        properties: {
          query: {
            type: 'string',
            description: 'SQL query to execute'
          },
          database: {
            type: 'string',
            description: 'Database type (postgresql, mysql, etc.)',
            enum: ['postgresql', 'mysql', 'sqlite']
          },
          explain: {
            type: 'boolean',
            description: 'Include query execution plan',
            default: false
          }
        },
        required: ['query', 'database']
      },
      execute: async (args: any, context: ExecutionContext) => {
        const dbClient = new DatabaseClient(args.database);

        try {
          // Validate query (basic security check)
          if (this.containsDangerousKeywords(args.query)) {
            throw new Error('Query contains potentially dangerous keywords');
          }

          const result = await dbClient.executeQuery(args.query);

          if (args.explain) {
            const explainResult = await dbClient.explainQuery(args.query);
            return {
              data: result,
              executionPlan: explainResult,
              rowCount: result.length,
              executionTime: Date.now() - context.startTime
            };
          }

          return {
            data: result,
            rowCount: result.length,
            executionTime: Date.now() - context.startTime
          };

        } catch (error) {
          return {
            error: error.message,
            sqlState: error.sqlState,
            query: args.query
          };
        }
      }
    });

    // File System Function
    this.registerFunction({
      name: 'read_file',
      description: 'Read and analyze file content',
      parameters: {
        type: 'object',
        properties: {
          path: {
            type: 'string',
            description: 'File path to read'
          },
          analyze: {
            type: 'boolean',
            description: 'Perform code analysis',
            default: true
          },
          maxSize: {
            type: 'number',
            description: 'Maximum file size in bytes',
            default: 1000000
          }
        },
        required: ['path']
      },
      execute: async (args: any, context: ExecutionContext) => {
        try {
          // Security: Validate file path
          if (!this.isSafePath(args.path)) {
            throw new Error('Access to this file path is not allowed');
          }

          const content = await fs.readFile(args.path, 'utf8');

          // Check file size
          if (content.length > args.maxSize) {
            throw new Error(`File too large: ${content.length} bytes`);
          }

          if (args.analyze) {
            const analysis = await this.analyzeCode(content, args.path);
            return {
              content,
              analysis,
              size: content.length,
              language: this.detectLanguage(args.path)
            };
          }

          return {
            content,
            size: content.length,
            language: this.detectLanguage(args.path)
          };

        } catch (error) {
          return {
            error: error.message,
            path: args.path
          };
        }
      }
    });

    // Code Generation Function
    this.registerFunction({
      name: 'generate_code',
      description: 'Generate code based on specifications',
      parameters: {
        type: 'object',
        properties: {
          specification: {
            type: 'string',
            description: 'Code specification in natural language'
          },
          language: {
            type: 'string',
            description: 'Programming language',
            enum: ['typescript', 'javascript', 'python', 'php', 'java']
          },
          framework: {
            type: 'string',
            description: 'Framework or library',
            enum: ['react', 'vue', 'angular', 'laravel', 'express', 'django']
          },
          type: {
            type: 'string',
            description: 'Type of code to generate',
            enum: ['component', 'service', 'utility', 'test', 'api']
          },
          requirements: {
            type: 'array',
            items: { type: 'string' },
            description: 'Specific requirements or constraints'
          }
        },
        required: ['specification', 'language', 'type']
      },
      execute: async (args: any, context: ExecutionContext) => {
        try {
          const prompt = this.buildCodeGenerationPrompt(args);
          const generatedCode = await this.openai.chatCompletion({
            message: prompt,
            model: 'gpt-4',
            temperature: 0.2
          });

          // Validate generated code
          const validation = await this.validateGeneratedCode(
            generatedCode.content,
            args.language
          );

          return {
            code: generatedCode.content,
            validation,
            suggestions: this.getCodeImprovements(generatedCode.content, args),
            usage: generatedCode.usage
          };

        } catch (error) {
          return {
            error: error.message,
            specification: args.specification
          };
        }
      }
    });
  }

  private containsDangerousKeywords(query: string): boolean {
    const dangerous = ['DROP', 'DELETE', 'UPDATE', 'INSERT', 'ALTER', 'TRUNCATE'];
    return dangerous.some(keyword => query.toUpperCase().includes(keyword));
  }

  private isSafePath(path: string): boolean {
    // Prevent directory traversal
    const normalizedPath = path.replace(/\.\./g, '');
    return !normalizedPath.includes('..') && normalizedPath.startsWith('./') || normalizedPath.startsWith('/');
  }

  private detectLanguage(filePath: string): string {
    const ext = filePath.split('.').pop()?.toLowerCase();
    const langMap: Record<string, string> = {
      'ts': 'typescript',
      'tsx': 'typescript',
      'js': 'javascript',
      'jsx': 'javascript',
      'py': 'python',
      'php': 'php',
      'java': 'java'
    };
    return langMap[ext] || 'text';
  }
}
```

## 🌐 Firecrawl API : Web Scraping et Content Extraction

### 1. 🕷️ Web Scraping Architecture

```
Tu es un expert web scraping et Firecrawl API.

TA TÂCHE : Implémenter web scraping avec Firecrawl pour [USE CASE]

FIRECRAWL INTÉGRATION :
1. **Content Extraction** : Clean text extraction, HTML parsing
2. **URL Crawling** : Website crawling, sitemap following
3. **Data Processing** : JSON transformation, filtering
4. **Rate Limiting** : Respectful scraping, delays
5. **Content Analysis** : Text analysis, summarization
6. **Storage** : Data persistence, indexing

CONTRAINTES :
- robots.txt compliance
- Rate limiting
- Data quality
- Error handling
- Cost optimization

FORMAT DE SORTIE :
1. Firecrawl integration architecture
2. Content extraction implementation
3. URL crawling setup
4. Data processing pipelines
5. Rate limiting configuration
6. Error handling
7. Storage optimization

QUALITÉ : Web scraping respectueux, efficace, scalable
```

**Intégration Firecrawl :**
```typescript
// src/services/firecrawl/FirecrawlClient.ts
export class FirecrawlClient {
  private apiKey: string;
  private baseUrl: string = 'https://api.firecrawl.dev';
  private rateLimiter: RateLimiter;

  constructor(apiKey: string) {
    this.apiKey = apiKey;
    this.rateLimiter = new RateLimiter({
      requestsPerMinute: 20, // Firecrawl limit
      burstLimit: 5
    });
  }

  async scrapeUrl(url: string, options: ScrapeOptions = {}): Promise<ScrapedContent> {
    await this.rateLimiter.waitForLimit();

    try {
      const response = await fetch(`${this.baseUrl}/scrape`, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${this.apiKey}`,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          url,
          ...options
        })
      });

      if (!response.ok) {
        throw new FirecrawlError(`HTTP ${response.status}: ${response.statusText}`);
      }

      const result = await response.json();

      return {
        url: result.url,
        content: result.content,
        markdown: result.markdown,
        title: result.title,
        description: result.description,
        language: result.language,
        sourceURL: result.sourceURL,
        statusCode: result.statusCode,
        error: result.error,
        creditsUsed: result.creditsUsed
      };

    } catch (error) {
      throw new FirecrawlError(`Scraping failed: ${error.message}`);
    }
  }

  async crawlWebsite(
    url: string,
    options: CrawlOptions = {}
  ): Promise<CrawlResult> {
    await this.rateLimiter.waitForLimit();

    try {
      const response = await fetch(`${this.baseUrl}/crawl`, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${this.apiKey}`,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          url,
          ...options
        })
      });

      if (!response.ok) {
        throw new FirecrawlError(`HTTP ${response.status}: ${response.statusText}`);
      }

      const result = await response.json();

      return {
        id: result.id,
        url: result.url,
        status: result.status,
        total: result.total,
        completed: result.completed,
        creditsUsed: result.creditsUsed,
        expiresAt: result.expiresAt,
        data: result.data || []
      };

    } catch (error) {
      throw new FirecrawlError(`Crawling failed: ${error.message}`);
    }
  }

  async getCrawlStatus(crawlId: string): Promise<CrawlStatus> {
    await this.rateLimiter.waitForLimit();

    try {
      const response = await fetch(`${this.baseUrl}/crawl/${crawlId}`, {
        headers: {
          'Authorization': `Bearer ${this.apiKey}`
        }
      });

      if (!response.ok) {
        throw new FirecrawlError(`HTTP ${response.status}: ${response.statusText}`);
      }

      const result = await response.json();

      return {
        id: result.id,
        url: result.url,
        status: result.status,
        total: result.total,
        completed: result.completed,
        creditsUsed: result.creditsUsed,
        expiresAt: result.expiresAt,
        data: result.data || []
      };

    } catch (error) {
      throw new FirecrawlError(`Status check failed: ${error.message}`);
    }
  }

  async crawlAndProcess(
    url: string,
    processors: ContentProcessor[] = []
  ): Promise<ProcessedContent[]> {
    // Start crawl
    const crawlResult = await this.crawlWebsite(url, {
      maxPages: 100,
      allowBackwardLinks: false,
      allowExternalLinks: false,
      ignoreSitemap: false
    });

    // Wait for completion
    let status = crawlResult;
    while (status.status !== 'completed' && status.status !== 'failed') {
      await new Promise(resolve => setTimeout(resolve, 5000)); // Wait 5 seconds
      status = await this.getCrawlStatus(status.id);
    }

    if (status.status === 'failed') {
      throw new FirecrawlError('Crawling failed');
    }

    // Process each page
    const processedContent: ProcessedContent[] = [];

    for (const page of status.data) {
      let content = await this.scrapeUrl(page.url, {
        onlyMainContent: true,
        removeTags: ['nav', 'header', 'footer', 'aside', 'script', 'style'],
        format: 'markdown'
      });

      // Apply processors
      for (const processor of processors) {
        content = await processor.process(content);
      }

      processedContent.push({
        url: page.url,
        title: content.title,
        content: content.content,
        markdown: content.markdown,
        metadata: page.metadata,
        processed: true
      });
    }

    return processedContent;
  }

  async searchAndScrape(
    query: string,
    options: SearchOptions = {}
  ): Promise<ScrapedContent[]> {
    // This would integrate with search APIs like Google Custom Search
    // or use Firecrawl's search capabilities if available

    const searchResults = await this.performWebSearch(query, options);

    const scrapedContent: ScrapedContent[] = [];

    // Scrape top results
    for (const result of searchResults.slice(0, options.maxResults || 10)) {
      try {
        const content = await this.scrapeUrl(result.url, {
          onlyMainContent: true,
          format: 'markdown'
        });

        scrapedContent.push({
          ...content,
          searchQuery: query,
          searchRank: result.rank
        });

      } catch (error) {
        console.error(`Failed to scrape ${result.url}:`, error);
      }
    }

    return scrapedContent;
  }

  private async performWebSearch(query: string, options: SearchOptions): Promise<SearchResult[]> {
    // Implementation would depend on search API used
    // Example with SerpAPI or similar service
    const searchResponse = await fetch('https://serpapi.com/search.json', {
      method: 'GET',
      headers: {
        'Authorization': `Bearer ${process.env.SERP_API_KEY}`
      }
    });

    const searchData = await searchResponse.json();

    return searchData.organic_results.map((result: any, index: number) => ({
      url: result.link,
      title: result.title,
      description: result.snippet,
      rank: index + 1
    }));
  }
}

// Content processors
interface ContentProcessor {
  process(content: ScrapedContent): Promise<ScrapedContent>;
}

class ContentSummarizer implements ContentProcessor {
  async process(content: ScrapedContent): Promise<ScrapedContent> {
    // Use OpenAI to summarize content
    const summary = await this.summarizeText(content.content);

    return {
      ...content,
      summary,
      wordCount: content.content.split(' ').length,
      readingTime: Math.ceil(content.content.split(' ').length / 200) // 200 words per minute
    };
  }

  private async summarizeText(text: string): Promise<string> {
    // Implementation using OpenAI
    return "Summary generated by AI";
  }
}

class ContentCategorizer implements ContentProcessor {
  async process(content: ScrapedContent): Promise<ScrapedContent> {
    // Categorize content using AI
    const categories = await this.categorizeContent(content.content);

    return {
      ...content,
      categories,
      tags: this.extractTags(content.content)
    };
  }

  private async categorizeContent(text: string): Promise<string[]> {
    // Implementation using OpenAI for categorization
    return ['technology', 'programming'];
  }

  private extractTags(text: string): string[] {
    // Extract relevant tags from content
    return ['tag1', 'tag2'];
  }
}
```

## 🔌 Third-Party API Integrations : Intégrations tierces

### 1. 🌐 API Integration Patterns : Patterns d'intégration

```
Tu es un expert API integration patterns et best practices.

TA TÂCHE : Implémenter des patterns d'intégration API pour [SERVICES]

PATTERNS À IMPLÉMENTER :
1. **Retry Pattern** : Exponential backoff, circuit breaker
2. **Rate Limiting** : Token bucket, request throttling
3. **Caching** : Response caching, cache invalidation
4. **Authentication** : API keys, OAuth2, JWT
5. **Error Handling** : Graceful degradation, fallback
6. **Monitoring** : Metrics, logging, alerting

CONTRAINTES :
- API rate limits
- Cost optimization
- Security compliance
- Performance requirements
- Reliability standards

FORMAT DE SORTIE :
1. Integration patterns architecture
2. Retry implementation
3. Rate limiting setup
4. Caching strategies
5. Authentication flows
6. Error handling
7. Monitoring setup

QUALITÉ : Intégrations API robustes, sécurisées, performantes
```

**Exemple de patterns d'intégration :**
```typescript
// src/integrations/ApiIntegrationManager.ts
export class ApiIntegrationManager {
  private integrations: Map<string, ApiIntegration> = new Map();
  private circuitBreakers: Map<string, CircuitBreaker> = new Map();
  private rateLimiters: Map<string, RateLimiter> = new Map();

  registerIntegration(name: string, integration: ApiIntegration) {
    this.integrations.set(name, integration);

    // Setup circuit breaker
    this.circuitBreakers.set(name, new CircuitBreaker({
      failureThreshold: integration.circuitBreakerConfig?.failureThreshold || 5,
      recoveryTimeout: integration.circuitBreakerConfig?.recoveryTimeout || 60000,
      monitoringPeriod: integration.circuitBreakerConfig?.monitoringPeriod || 10000
    }));

    // Setup rate limiter
    this.rateLimiters.set(name, new RateLimiter({
      requestsPerMinute: integration.rateLimitConfig?.requestsPerMinute || 60,
      burstLimit: integration.rateLimitConfig?.burstLimit || 10
    }));
  }

  async executeRequest<T>(
    integrationName: string,
    request: ApiRequest
  ): Promise<ApiResponse<T>> {
    const integration = this.integrations.get(integrationName);
    const circuitBreaker = this.circuitBreakers.get(integrationName);
    const rateLimiter = this.rateLimiters.get(integrationName);

    if (!integration || !circuitBreaker || !rateLimiter) {
      throw new Error(`Integration ${integrationName} not found`);
    }

    // Check circuit breaker
    if (circuitBreaker.state === 'OPEN') {
      throw new CircuitBreakerError('Circuit breaker is open');
    }

    // Wait for rate limit
    await rateLimiter.waitForLimit();

    const startTime = Date.now();

    try {
      // Execute request with retry logic
      const response = await this.executeWithRetry(integration, request);

      // Record success
      circuitBreaker.recordSuccess();
      this.recordMetrics(integrationName, 'success', Date.now() - startTime);

      return response;

    } catch (error) {
      // Record failure
      circuitBreaker.recordFailure();
      this.recordMetrics(integrationName, 'error', Date.now() - startTime);

      throw error;
    }
  }

  private async executeWithRetry<T>(
    integration: ApiIntegration,
    request: ApiRequest
  ): Promise<ApiResponse<T>> {
    const maxRetries = integration.retryConfig?.maxRetries || 3;
    const baseDelay = integration.retryConfig?.baseDelay || 1000;

    let lastError: Error;

    for (let attempt = 1; attempt <= maxRetries; attempt++) {
      try {
        return await integration.execute<T>(request);

      } catch (error) {
        lastError = error;

        // Don't retry on certain errors
        if (this.isNonRetryableError(error)) {
          break;
        }

        // Exponential backoff
        if (attempt < maxRetries) {
          const delay = baseDelay * Math.pow(2, attempt - 1);
          await new Promise(resolve => setTimeout(resolve, delay));
        }
      }
    }

    throw lastError;
  }

  private isNonRetryableError(error: any): boolean {
    const nonRetryableCodes = [400, 401, 403, 404, 422];
    return error.status && nonRetryableCodes.includes(error.status);
  }

  private recordMetrics(integrationName: string, status: string, duration: number) {
    // Record metrics for monitoring
    console.log(`API ${integrationName}: ${status} in ${duration}ms`);
  }

  async getIntegrationHealth(integrationName: string): Promise<HealthStatus> {
    const circuitBreaker = this.circuitBreakers.get(integrationName);

    if (!circuitBreaker) {
      return { status: 'unknown', message: 'Integration not found' };
    }

    const health: HealthStatus = {
      status: circuitBreaker.state === 'CLOSED' ? 'healthy' : 'degraded',
      circuitBreakerState: circuitBreaker.state,
      metrics: {
        successRate: circuitBreaker.successRate,
        failureRate: circuitBreaker.failureRate,
        averageResponseTime: circuitBreaker.averageResponseTime
      }
    };

    return health;
  }
}

// Circuit Breaker Implementation
class CircuitBreaker {
  private failures: number = 0;
  private successes: number = 0;
  private requests: number = 0;
  private state: 'CLOSED' | 'OPEN' | 'HALF_OPEN' = 'CLOSED';
  private lastFailureTime?: number;

  constructor(private config: CircuitBreakerConfig) {}

  recordSuccess() {
    this.successes++;
    this.requests++;

    if (this.state === 'HALF_OPEN') {
      this.state = 'CLOSED';
      this.failures = 0;
    }
  }

  recordFailure() {
    this.failures++;
    this.requests++;
    this.lastFailureTime = Date.now();

    if (this.failures >= this.config.failureThreshold) {
      this.state = 'OPEN';
    }
  }

  get successRate(): number {
    return this.requests > 0 ? this.successes / this.requests : 0;
  }

  get failureRate(): number {
    return this.requests > 0 ? this.failures / this.requests : 0;
  }

  get averageResponseTime(): number {
    // Would track actual response times in a real implementation
    return 0;
  }
}

// Rate Limiter Implementation
class RateLimiter {
  private requests: number[] = [];

  constructor(private config: RateLimitConfig) {}

  async waitForLimit(): Promise<void> {
    const now = Date.now();
    const windowStart = now - 60000; // 1 minute window

    // Remove old requests
    this.requests = this.requests.filter(time => time > windowStart);

    // Check if under limit
    if (this.requests.length >= this.config.requestsPerMinute) {
      // Calculate wait time
      const oldestRequest = Math.min(...this.requests);
      const waitTime = 60000 - (now - oldestRequest);

      if (waitTime > 0) {
        await new Promise(resolve => setTimeout(resolve, waitTime));
        return this.waitForLimit(); // Recheck after waiting
      }
    }

    // Record this request
    this.requests.push(now);
  }
}
```

### 2. 🔐 Authentication et Security : Authentification et sécurité

```
Tu es un expert API security et authentication.

TA TÂCHE : Sécuriser les intégrations API pour [SERVICES]

SÉCURITÉ À IMPLÉMENTER :
1. **API Key Management** : Secure storage, rotation
2. **OAuth2 Flows** : Authorization code, client credentials
3. **JWT Tokens** : Generation, validation, refresh
4. **Rate Limiting** : Per-user, per-endpoint limits
5. **Request Signing** : HMAC, digital signatures
6. **Encryption** : Data in transit, at rest

CONTRAINTES :
- OWASP API Security Top 10
- GDPR compliance
- PCI-DSS (if payments)
- Cost optimization
- Performance impact

FORMAT DE SORTIE :
1. Security architecture
2. Authentication flows
3. API key management
4. Rate limiting setup
5. Request signing
6. Encryption implementation
7. Monitoring et auditing

QUALITÉ : Intégrations API sécurisées, compliant, auditables
```

## 📊 Monitoring et Analytics : Monitoring et analytics

### 1. 📈 API Monitoring : Monitoring des APIs

```
Tu es un expert API monitoring et observability.

TA TÂCHE : Implémenter monitoring pour [API INTEGRATIONS]

MONITORING À IMPLÉMENTER :
1. **Performance Monitoring** : Response times, throughput
2. **Error Tracking** : Error rates, patterns, alerts
3. **Usage Analytics** : API usage, costs, quotas
4. **Security Monitoring** : Unauthorized access, anomalies
5. **Business Metrics** : Conversion, engagement
6. **Health Checks** : Service availability, dependencies

CONTRAINTES :
- Real-time monitoring
- Cost optimization
- Actionable alerts
- Performance impact
- Team notifications

FORMAT DE SORTIE :
1. Monitoring architecture
2. Metrics collection
3. Alert configuration
4. Dashboard setup
5. Performance optimization
6. Cost monitoring
7. Team integration

QUALITÉ : Monitoring complet, actionable, cost-effective
```

## 📝 Templates d'APIs

### 🤖 Template OpenAI Integration
```
Tu es un expert OpenAI API et AI integrations.

TA TÂCHE : Intégrer OpenAI API pour [FONCTIONNALITÉ]

INTÉGRATIONS :
- Chat completions
- Function calling
- Embeddings
- Moderation

CONTRAINTES :
- Rate limits
- Cost optimization
- Error handling
- Security

FORMAT :
1. Integration architecture
2. API implementation
3. Function calling
4. Error handling
5. Cost optimization
6. Security setup
7. Performance tuning

QUALITÉ : Intégration OpenAI robuste, optimisée, sécurisée
```

### 🌐 Template Firecrawl Integration
```
Tu es un expert web scraping et Firecrawl API.

TA TÂCHE : Implémenter scraping avec Firecrawl pour [USE CASE]

FONCTIONNALITÉS :
- Content extraction
- URL crawling
- Data processing
- Rate limiting

CONTRAINTES :
- robots.txt compliance
- Data quality
- Cost optimization
- Performance

FORMAT :
1. Firecrawl architecture
2. Content extraction
3. URL crawling
4. Data processing
5. Rate limiting
6. Error handling
7. Storage optimization

QUALITÉ : Web scraping respectueux, efficace, scalable
```

### 🔌 Template Third-Party Integration
```
Tu es un expert API integrations et third-party services.

TA TÂCHE : Intégrer [API] pour [FONCTIONNALITÉ]

INTÉGRATION :
- Authentication setup
- API endpoints
- Rate limiting
- Error handling

CONTRAINTES :
- Security compliance
- Performance optimization
- Cost management
- Reliability

FORMAT :
1. Integration design
2. Authentication
3. API implementation
4. Error handling
5. Monitoring setup
6. Cost optimization
7. Documentation

QUALITÉ : Intégration API robuste, sécurisée, optimisée
```

## 🎯 Points clés à retenir

1. **OpenAI API** : Chat, function calling, embeddings, moderation
2. **Firecrawl API** : Web scraping, content extraction, crawling
3. **API Security** : Authentication, rate limiting, encryption
4. **Error Handling** : Retry logic, circuit breakers, graceful degradation
5. **Cost Optimization** : Caching, rate limiting, efficient usage
6. **Monitoring** : Performance, security, usage analytics

## 🚀 Conclusion de la Partie VI

Vous avez maintenant toutes les compétences pour intégrer des APIs modernes et créer des agents IA avancés. Passons maintenant à la productivité et gestion de projet.

---

## 📋 Checklist qualité

- ✅ OpenAI API integration
- ✅ Function calling implementation
- ✅ Firecrawl web scraping
- ✅ Third-party API integrations
- ✅ Security et authentication
- ✅ Monitoring et analytics

## 🎯 Test de compréhension

**Question :** Quelle est la principale différence entre OpenAI API et Firecrawl API ?

**Réponse attendue :** OpenAI API fournit des capacités d'IA (chat, embeddings, function calling) pour traiter du texte et raisonner. Firecrawl API est spécialisé dans le web scraping et l'extraction de contenu web, permettant de récupérer et structurer du contenu depuis internet.

---

**Temps de lecture estimé : 125 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + III + IV + V + Chapitres 1, 2**

---

🎉 **Félicitations !** Vous avez terminé la Partie VI - Data, IA & Automatisation.

**Prochaines étapes :** Partie VII - Productivité & Gestion de Projet

---

## 📊 Synthèse des acquis

### ✅ Compétences acquises :
- Prompt engineering avancé (CoT, few-shot, chaining)
- AI agents et autonomous systems
- Custom functions et tool calling
- Automated workflows (CI/CD, monitoring)
- OpenAI API integration complète
- Firecrawl web scraping
- Third-party API integrations
- Security et authentication

### 🎯 Niveau atteint :
- **AI Development** : Expert
- **API Integration** : Expert
- **Automation** : Expert
- **Security** : Expert

### 🚀 Prêt pour :
- Product management
- Team leadership
- Project management
- Business analysis
- System architecture
- Technical strategy
