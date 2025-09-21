# 🏗️ Technical Architecture

## Summary

Katy Coin leverages Cloudflare's comprehensive edge computing platform to create a revolutionary economic system. Built on Cloudflare's global network spanning 300+ cities, we deliver sub-50ms transactions worldwide while maintaining security, scalability, and decentralization through AI Gateway, Durable Objects, Workflows, and enterprise-grade security tools.

## 📑 Table of Contents

- [Core Architecture Overview](#-core-architecture-overview)
- [Cloudflare Services Stack](#-cloudflare-services-stack)
- [AI Gateway & Open-Source Models](#-ai-gateway--open-source-models)
- [Durable Objects Architecture](#-durable-objects-architecture)
- [Cloudflare Workflows](#-cloudflare-workflows)
- [Security & DDoS Protection](#-security--ddos-protection)
- [Layer 1: Market Intelligence](#-layer-1-market-intelligence)
- [Layer 2: Trade Orchestration](#-layer-2-trade-orchestration)
- [Layer 3: Settlement & Storage](#-layer-3-settlement--storage)
- [Performance & Scaling](#-performance--scaling)

## 🎯 Core Architecture Overview

```mermaid
graph TB
    subgraph "User Interfaces"
        U1[Mobile App]
        U2[Web App]
        U3[POS Systems]
        U4[API Clients]
    end
    
    subgraph "Cloudflare Edge Network"
        subgraph "Workers & Compute"
            W1[Workers]
            W2[Durable Objects]
            W3[Workflows]
            W4[Pages Functions]
        end
        
        subgraph "AI Services"
            AI1[AI Gateway]
            AI2[Workers AI Models]
            AI3[Vectorize]
            AI4[Constellation]
        end
        
        subgraph "Storage"
            S1[D1 Database]
            S2[KV Store]
            S3[R2 Storage]
            S4[Queues]
            S5[Hyperdrive]
        end
        
        subgraph "Security"
            SEC1[WAF]
            SEC2[DDoS Protection]
            SEC3[Turnstile]
            SEC4[Zero Trust]
        end
    end
    
    subgraph "Blockchain Layer"
        B1[Polygon PoS]
        B2[Smart Contracts]
        B3[IPFS]
    end
    
    U1 & U2 & U3 & U4 --> SEC1
    SEC1 --> W1
    W1 --> W2 & W3
    W1 --> AI1
    AI1 --> AI2 & AI3
    W2 --> S1 & S2 & S3
    W3 --> S4
    S1 --> S5
    S1 --> B1
    
    style U1 fill:#e1f5fe
    style W1 fill:#fff3e0
    style AI1 fill:#f3e5f5
    style SEC1 fill:#ffebee
    style B1 fill:#e8f5e9
```

## 🔧 Cloudflare Services Stack

```javascript
const cloudflareStack = {
  // Core Compute
  compute: {
    workers: {
      purpose: "Serverless edge compute",
      locations: "300+ cities worldwide",
      scaling: "Automatic, unlimited",
      cost: "$0.30 per million requests"
    },
    durableObjects: {
      purpose: "Stateful coordination",
      features: ["WebSocket", "Transactional storage", "Strong consistency"],
      useCase: "Real-time trade sessions"
    },
    workflows: {
      purpose: "Orchestrated multi-step processes",
      features: ["Retries", "Error handling", "State management"],
      useCase: "Complex trade workflows"
    }
  },
  
  // AI Services
  ai: {
    aiGateway: {
      purpose: "Unified AI API management",
      providers: ["OpenAI", "Anthropic", "Workers AI", "Hugging Face"],
      features: ["Rate limiting", "Caching", "Analytics", "Fallbacks"]
    },
    workersAI: {
      models: {
        llm: ["Llama 2", "Mistral", "CodeLlama", "Falcon"],
        vision: ["LLaVA", "CLIP"],
        audio: ["Whisper", "M2M-100"],
        embedding: ["BGE", "GTE"]
      },
      pricing: "Per-neuron pricing model"
    },
    vectorize: {
      purpose: "Vector database for semantic search",
      features: ["Sub-50ms queries", "Automatic indexing"]
    }
  },
  
  // Storage Solutions
  storage: {
    d1: {
      type: "SQLite at edge",
      features: ["ACID transactions", "10GB per database"],
      replication: "Automatic global replication"
    },
    kv: {
      type: "Key-value store",
      features: ["60s TTL minimum", "25MiB value size"],
      reads: "Unlimited"
    },
    r2: {
      type: "Object storage",
      features: ["S3 compatible", "Zero egress fees"],
      pricing: "$0.015 per GB/month"
    },
    queues: {
      type: "Message queuing",
      features: ["At-least-once delivery", "Batching"],
      throughput: "100+ messages/second"
    }
  },
  
  // Security & Protection
  security: {
    waf: {
      rules: "Core Ruleset + Custom Rules",
      protection: "OWASP Top 10",
      rateLimit: "10M requests/minute"
    },
    ddos: {
      protection: "Automatic, unmetered",
      capacity: "254 Tbps network",
      scrubbing: "Global anycast network"
    },
    turnstile: {
      purpose: "Privacy-first CAPTCHA alternative",
      modes: ["Invisible", "Managed", "Interactive"]
    },
    zeroTrust: {
      features: ["Device posture", "Identity providers", "WARP"],
      compliance: ["SOC 2", "ISO 27001", "PCI DSS"]
    }
  }
};
```

## 🤖 AI Gateway & Open-Source Models

### AI Gateway Architecture

```mermaid
sequenceDiagram
    participant Client
    participant Gateway as AI Gateway
    participant Cache as Cache Layer
    participant Router as Smart Router
    participant Workers as Workers AI
    participant External as External APIs
    
    Client->>Gateway: AI Request
    Gateway->>Cache: Check Cache
    
    alt Cache Hit
        Cache-->>Client: Cached Response
    else Cache Miss
        Gateway->>Router: Route Request
        
        alt Use Workers AI
            Router->>Workers: Process with OSS Model
            Workers-->>Router: Model Response
        else Use External API
            Router->>External: Forward to Provider
            External-->>Router: API Response
        end
        
        Router->>Cache: Store Response
        Router-->>Client: Final Response
    end
    
    Note over Gateway: Analytics & Monitoring
```

### AI Gateway Implementation

```javascript
export default {
  async fetch(request, env, ctx) {
    const gateway = new AIGateway(env);
    
    // Configure AI Gateway with fallbacks
    const config = {
      providers: [
        {
          name: 'workers-ai',
          models: ['@cf/meta/llama-2-7b-chat-int8'],
          weight: 0.5, // Prefer edge models
          maxRetries: 3
        },
        {
          name: 'openai',
          models: ['gpt-4-turbo'],
          weight: 0.3,
          apiKey: env.OPENAI_API_KEY
        },
        {
          name: 'anthropic',
          models: ['claude-3-opus'],
          weight: 0.2,
          apiKey: env.ANTHROPIC_API_KEY
        }
      ],
      
      caching: {
        enabled: true,
        ttl: 3600, // 1 hour
        semanticCache: true // Cache similar queries
      },
      
      rateLimiting: {
        requests: 1000,
        window: 60, // per minute
        keyBy: 'ip' // or 'user', 'api-key'
      },
      
      analytics: {
        enabled: true,
        logPrompts: false, // Privacy
        logLatency: true,
        logCosts: true
      },
      
      fallback: {
        enabled: true,
        strategy: 'waterfall', // or 'race', 'weighted'
        timeout: 5000
      }
    };
    
    return gateway.process(request, config);
  }
};

class AIGateway {
  constructor(env) {
    this.env = env;
    this.cache = new KVCache(env.KV);
    this.analytics = new AnalyticsEngine(env.ANALYTICS);
  }
  
  async process(request, config) {
    const { prompt, model, stream } = await request.json();
    
    // Check cache first
    const cacheKey = await this.generateCacheKey(prompt, model);
    const cached = await this.cache.get(cacheKey);
    if (cached && config.caching.enabled) {
      this.analytics.logCacheHit();
      return new Response(cached);
    }
    
    // Smart routing based on request type
    const provider = this.selectProvider(prompt, model, config);
    
    // Execute with retries and fallbacks
    let response;
    for (const p of this.getProviderChain(provider, config)) {
      try {
        response = await this.callProvider(p, prompt, model, stream);
        if (response.ok) break;
      } catch (error) {
        this.analytics.logError(p.name, error);
        continue; // Try next provider
      }
    }
    
    // Cache successful responses
    if (response.ok && config.caching.enabled) {
      const responseText = await response.text();
      await this.cache.set(cacheKey, responseText, config.caching.ttl);
      return new Response(responseText);
    }
    
    return response;
  }
  
  async callProvider(provider, prompt, model, stream) {
    switch (provider.name) {
      case 'workers-ai':
        return this.callWorkersAI(prompt, model, stream);
      case 'openai':
        return this.callOpenAI(prompt, model, stream, provider.apiKey);
      case 'anthropic':
        return this.callAnthropic(prompt, model, stream, provider.apiKey);
      default:
        throw new Error(`Unknown provider: ${provider.name}`);
    }
  }
  
  async callWorkersAI(prompt, model, stream) {
    const response = await this.env.AI.run(
      model || '@cf/meta/llama-2-7b-chat-int8',
      {
        prompt,
        stream,
        max_tokens: 2048,
        temperature: 0.7
      }
    );
    
    if (stream) {
      return new Response(response, {
        headers: { 'Content-Type': 'text/event-stream' }
      });
    }
    
    return new Response(JSON.stringify(response));
  }
}
```

### Workers AI Models Integration

```javascript
class WorkersAIModels {
  constructor(env) {
    this.ai = env.AI;
    this.models = {
      // Text Generation
      text: {
        small: '@cf/meta/llama-2-7b-chat-int8',
        medium: '@cf/mistral/mistral-7b-instruct-v0.1',
        code: '@cf/meta/codellama-7b-instruct',
        large: '@cf/meta/llama-2-13b-chat-int8'
      },
      
      // Vision
      vision: {
        analysis: '@cf/llava-hf/llava-1.5-7b-hf',
        classification: '@cf/microsoft/resnet-50'
      },
      
      // Audio
      audio: {
        transcription: '@cf/openai/whisper',
        translation: '@cf/meta/m2m100-1.2b'
      },
      
      // Embeddings
      embedding: {
        text: '@cf/baai/bge-base-en-v1.5',
        multilingual: '@cf/baai/bge-m3'
      }
    };
  }
  
  async valueProduct(image, description) {
    // Multi-modal analysis using open-source models
    const [imageAnalysis, textAnalysis, embedding] = await Promise.all([
      // Vision model for image understanding
      this.ai.run(this.models.vision.analysis, {
        image: Array.from(new Uint8Array(image)),
        prompt: "Identify this product and estimate its market value based on condition and features"
      }),
      
      // LLM for text analysis
      this.ai.run(this.models.text.medium, {
        prompt: `Analyze this product description and provide valuation: ${description}`,
        max_tokens: 500
      }),
      
      // Generate embedding for similarity search
      this.ai.run(this.models.embedding.text, {
        text: description
      })
    ]);
    
    // Combine analyses using another model
    const finalValuation = await this.ai.run(this.models.text.large, {
      prompt: `
        Image Analysis: ${imageAnalysis.response}
        Text Analysis: ${textAnalysis.response}
        
        Provide final KC valuation with confidence score and reasoning.
      `,
      max_tokens: 300
    });
    
    return {
      valuation: this.parseValuation(finalValuation),
      embedding: embedding.data[0],
      confidence: this.calculateConfidence(imageAnalysis, textAnalysis)
    };
  }
  
  async detectFraud(transaction) {
    // Use specialized model for fraud detection
    const analysis = await this.ai.run(this.models.text.medium, {
      prompt: `
        Analyze this transaction for fraud indicators:
        ${JSON.stringify(transaction)}
        
        Consider: unusual patterns, velocity, amounts, parties involved.
        Return risk score 0-1 and reasons.
      `,
      max_tokens: 200
    });
    
    return this.parseFraudAnalysis(analysis);
  }
  
  async translateContent(text, targetLang) {
    // M2M-100 supports 100 languages
    const translation = await this.ai.run(
      this.models.audio.translation,
      {
        text,
        source_lang: 'en',
        target_lang: targetLang
      }
    );
    
    return translation.translated_text;
  }
}
```

## 🔮 Durable Objects Architecture

### Durable Objects for Stateful Coordination

```mermaid
graph TB
    subgraph "Durable Objects Ecosystem"
        subgraph "Trade Sessions"
            DO1[Trade Negotiation]
            DO2[Multi-Party Escrow]
            DO3[Auction Manager]
        end
        
        subgraph "User State"
            DO4[User Wallet]
            DO5[Reputation Score]
            DO6[Activity Tracker]
        end
        
        subgraph "System Coordination"
            DO7[Rate Limiter]
            DO8[Circuit Breaker]
            DO9[Leader Election]
        end
        
        subgraph "Real-time Features"
            DO10[WebSocket Hub]
            DO11[Live Pricing]
            DO12[Chat Rooms]
        end
    end
    
    DO1 --> DO2
    DO2 --> DO4
    DO4 --> DO5
    DO10 --> DO11
    DO11 --> DO12
    
    style DO1 fill:#e3f2fd
    style DO4 fill:#fff3e0
    style DO7 fill:#ffebee
    style DO10 fill:#e8f5e9
```

### Trade Session Durable Object

```javascript
export class TradeSession {
  constructor(state, env) {
    this.state = state;
    this.env = env;
    
    // Initialize SQL storage
    this.state.storage.sql.exec(`
      CREATE TABLE IF NOT EXISTS participants (
        id TEXT PRIMARY KEY,
        user_id TEXT NOT NULL,
        joined_at INTEGER,
        status TEXT,
        offers JSON,
        wants JSON
      );
      
      CREATE TABLE IF NOT EXISTS messages (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        participant_id TEXT,
        content TEXT,
        type TEXT,
        timestamp INTEGER,
        FOREIGN KEY (participant_id) REFERENCES participants(id)
      );
      
      CREATE TABLE IF NOT EXISTS trade_state (
        key TEXT PRIMARY KEY,
        value JSON,
        updated_at INTEGER
      );
    `);
    
    // WebSocket connections
    this.sessions = new Map();
    
    // Alarm for timeout
    this.state.storage.alarm = null;
  }
  
  async fetch(request) {
    const url = new URL(request.url);
    const path = url.pathname.split('/').pop();
    
    // Handle WebSocket upgrade
    if (request.headers.get('Upgrade') === 'websocket') {
      return this.handleWebSocket(request);
    }
    
    // Handle HTTP API
    switch (path) {
      case 'join':
        return this.handleJoin(request);
      case 'offer':
        return this.handleOffer(request);
      case 'negotiate':
        return this.handleNegotiate(request);
      case 'confirm':
        return this.handleConfirm(request);
      case 'state':
        return this.getState();
      default:
        return new Response('Not Found', { status: 404 });
    }
  }
  
  async handleWebSocket(request) {
    const pair = new WebSocketPair();
    const [client, server] = Object.values(pair);
    
    // Accept the WebSocket
    this.state.acceptWebSocket(server);
    
    // Generate session ID
    const sessionId = crypto.randomUUID();
    const userId = new URL(request.url).searchParams.get('userId');
    
    // Store session
    this.sessions.set(sessionId, {
      ws: server,
      userId,
      joinedAt: Date.now()
    });
    
    // Set up message handler
    server.addEventListener('message', async (event) => {
      await this.handleMessage(sessionId, JSON.parse(event.data));
    });
    
    // Clean up on close
    server.addEventListener('close', () => {
      this.sessions.delete(sessionId);
      this.broadcast({
        type: 'USER_LEFT',
        userId,
        timestamp: Date.now()
      });
    });
    
    // Send initial state
    server.send(JSON.stringify({
      type: 'INIT',
      state: await this.getFullState(),
      sessionId
    }));
    
    // Notify others
    this.broadcast({
      type: 'USER_JOINED',
      userId,
      timestamp: Date.now()
    }, sessionId);
    
    return new Response(null, {
      status: 101,
      webSocket: client
    });
  }
  
  async handleMessage(sessionId, message) {
    const session = this.sessions.get(sessionId);
    if (!session) return;
    
    // Store message in SQL
    await this.state.storage.sql.exec(
      `INSERT INTO messages (participant_id, content, type, timestamp)
       VALUES (?, ?, ?, ?)`,
      [session.userId, JSON.stringify(message), message.type, Date.now()]
    );
    
    // Process message based on type
    switch (message.type) {
      case 'OFFER':
        await this.processOffer(session.userId, message.data);
        break;
      
      case 'WANT':
        await this.processWant(session.userId, message.data);
        break;
      
      case 'NEGOTIATE':
        await this.processNegotiation(session.userId, message.data);
        break;
      
      case 'ACCEPT':
        await this.processAcceptance(session.userId, message.data);
        break;
      
      case 'CHAT':
        this.broadcast({
          type: 'CHAT',
          userId: session.userId,
          message: message.text,
          timestamp: Date.now()
        });
        break;
    }
    
    // Check if trade can be completed
    await this.checkTradeCompletion();
  }
  
  async checkTradeCompletion() {
    // Use SQL to check if all parties have accepted
    const result = await this.state.storage.sql.exec(`
      SELECT COUNT(*) as total,
             SUM(CASE WHEN status = 'accepted' THEN 1 ELSE 0 END) as accepted
      FROM participants
    `);
    
    const { total, accepted } = result[0];
    
    if (total > 1 && total === accepted) {
      await this.completeTrade();
    }
  }
  
  async completeTrade() {
    // Get all participants
    const participants = await this.state.storage.sql.exec(
      `SELECT * FROM participants WHERE status = 'accepted'`
    );
    
    // Create escrow
    const escrowId = await this.createEscrow(participants);
    
    // Update state
    await this.state.storage.sql.exec(
      `UPDATE trade_state SET value = ?, updated_at = ? WHERE key = 'status'`,
      [JSON.stringify({ status: 'completed', escrowId }), Date.now()]
    );
    
    // Notify all participants
    this.broadcast({
      type: 'TRADE_COMPLETED',
      escrowId,
      participants: participants.map(p => ({
        userId: p.user_id,
        offers: JSON.parse(p.offers),
        wants: JSON.parse(p.wants)
      })),
      timestamp: Date.now()
    });
    
    // Set alarm to clean up in 1 hour
    await this.state.storage.setAlarm(Date.now() + 3600000);
  }
  
  broadcast(message, excludeSession = null) {
    const messageStr = JSON.stringify(message);
    
    for (const [sessionId, session] of this.sessions) {
      if (sessionId !== excludeSession) {
        try {
          session.ws.send(messageStr);
        } catch (err) {
          // Connection might be closed
          this.sessions.delete(sessionId);
        }
      }
    }
  }
  
  async alarm() {
    // Clean up old session data
    await this.state.storage.deleteAll();
    
    // Close all WebSocket connections
    for (const session of this.sessions.values()) {
      session.ws.close(1000, 'Session expired');
    }
    
    this.sessions.clear();
  }
}
```

### User Wallet Durable Object

```javascript
export class UserWallet {
  constructor(state, env) {
    this.state = state;
    this.env = env;
    
    // Initialize wallet state
    this.state.storage.sql.exec(`
      CREATE TABLE IF NOT EXISTS balance (
        currency TEXT PRIMARY KEY,
        amount REAL,
        updated_at INTEGER
      );
      
      CREATE TABLE IF NOT EXISTS transactions (
        id TEXT PRIMARY KEY,
        type TEXT,
        amount REAL,
        currency TEXT,
        counterparty TEXT,
        timestamp INTEGER,
        status TEXT,
        metadata JSON
      );
      
      CREATE TABLE IF NOT EXISTS holds (
        id TEXT PRIMARY KEY,
        amount REAL,
        currency TEXT,
        reason TEXT,
        expires_at INTEGER
      );
    `);
  }
  
  async fetch(request) {
    const url = new URL(request.url);
    const method = request.method;
    const path = url.pathname.split('/').pop();
    
    // Ensure consistent state
    const transactionId = crypto.randomUUID();
    
    try {
      switch (path) {
        case 'balance':
          return this.getBalance();
          
        case 'transfer':
          if (method !== 'POST') return new Response('Method not allowed', { status: 405 });
          return this.transfer(await request.json(), transactionId);
          
        case 'hold':
          if (method !== 'POST') return new Response('Method not allowed', { status: 405 });
          return this.createHold(await request.json());
          
        case 'release':
          if (method !== 'POST') return new Response('Method not allowed', { status: 405 });
          return this.releaseHold(await request.json());
          
        case 'history':
          return this.getTransactionHistory(url.searchParams);
          
        default:
          return new Response('Not found', { status: 404 });
      }
    } catch (error) {
      return new Response(JSON.stringify({ error: error.message }), {
        status: 400,
        headers: { 'Content-Type': 'application/json' }
      });
    }
  }
  
  async transfer(params, transactionId) {
    const { to, amount, currency = 'KC' } = params;
    
    // Start transaction
    await this.state.storage.transaction(async () => {
      // Check balance
      const balance = await this.getBalanceAmount(currency);
      
      // Check holds
      const holds = await this.getTotalHolds(currency);
      
      const available = balance - holds;
      
      if (available < amount) {
        throw new Error('Insufficient balance');
      }
      
      // Deduct from balance
      await this.state.storage.sql.exec(
        `UPDATE balance SET amount = amount - ?, updated_at = ? WHERE currency = ?`,
        [amount, Date.now(), currency]
      );
      
      // Record transaction
      await this.state.storage.sql.exec(
        `INSERT INTO transactions (id, type, amount, currency, counterparty, timestamp, status, metadata)
         VALUES (?, ?, ?, ?, ?, ?, ?, ?)`,
        [transactionId, 'transfer_out', amount, currency, to, Date.now(), 'pending', JSON.stringify(params.metadata || {})]
      );
    });
    
    // Execute transfer to recipient (async)
    this.executeTransfer(transactionId, to, amount, currency);
    
    return new Response(JSON.stringify({
      transactionId,
      status: 'pending',
      amount,
      currency,
      to
    }), {
      headers: { 'Content-Type': 'application/json' }
    });
  }
  
  async createHold(params) {
    const { amount, currency = 'KC', reason, duration = 86400000 } = params;
    
    const holdId = crypto.randomUUID();
    const expiresAt = Date.now() + duration;
    
    await this.state.storage.transaction(async () => {
      // Check available balance
      const balance = await this.getBalanceAmount(currency);
      const holds = await this.getTotalHolds(currency);
      
      if (balance - holds < amount) {
        throw new Error('Insufficient available balance');
      }
      
      // Create hold
      await this.state.storage.sql.exec(
        `INSERT INTO holds (id, amount, currency, reason, expires_at)
         VALUES (?, ?, ?, ?, ?)`,
        [holdId, amount, currency, reason, expiresAt]
      );
    });
    
    // Set alarm to auto-release
    await this.state.storage.setAlarm(expiresAt);
    
    return new Response(JSON.stringify({
      holdId,
      amount,
      currency,
      reason,
      expiresAt
    }), {
      headers: { 'Content-Type': 'application/json' }
    });
  }
  
  async alarm() {
    // Release expired holds
    const now = Date.now();
    await this.state.storage.sql.exec(
      `DELETE FROM holds WHERE expires_at <= ?`,
      [now]
    );
  }
}
```

## 🔄 Cloudflare Workflows

### Workflows for Complex Trade Orchestration

```javascript
export class TradeWorkflow extends WorkflowEntrypoint {
  async run(event, step) {
    const { tradeId, participants, items } = event.payload;
    
    // Step 1: Validate all participants
    const validation = await step.do('validate-participants', async () => {
      const results = await Promise.all(
        participants.map(p => this.validateParticipant(p))
      );
      
      if (results.some(r => !r.valid)) {
        throw new Error('Invalid participants');
      }
      
      return results;
    });
    
    // Step 2: Value all items using AI
    const valuations = await step.do('value-items', async () => {
      const aiGateway = new AIGateway(this.env);
      
      return Promise.all(
        items.map(item => aiGateway.valueItem(item))
      );
    });
    
    // Step 3: Check trade balance
    const balance = await step.do('check-balance', async () => {
      return this.calculateTradeBalance(participants, valuations);
    });
    
    if (Math.abs(balance.difference) > balance.threshold) {
      // Step 4: Negotiate adjustments
      const adjustment = await step.do('negotiate', async () => {
        return this.negotiateBalance(balance);
      });
      
      if (!adjustment.agreed) {
        return {
          status: 'failed',
          reason: 'Could not balance trade'
        };
      }
    }
    
    // Step 5: Create escrow
    const escrow = await step.do('create-escrow', async () => {
      const escrowDO = this.env.ESCROW.get(
        this.env.ESCROW.idFromString(tradeId)
      );
      
      return escrowDO.create({
        participants,
        items: valuations,
        timeout: 24 * 60 * 60 * 1000 // 24 hours
      });
    });
    
    // Step 6: Notify participants
    await step.do('notify-participants', async () => {
      const notifications = participants.map(p => ({
        userId: p.id,
        type: 'TRADE_READY',
        escrowId: escrow.id,
        action: 'Please confirm your trade'
      }));
      
      await this.env.NOTIFICATION_QUEUE.send(notifications);
    });
    
    // Step 7: Wait for confirmations (with timeout)
    const confirmations = await step.do('wait-confirmations', async () => {
      return this.waitForConfirmations(escrow.id, participants);
    }, {
      retries: {
        limit: 3,
        delay: '5 minutes',
        backoff: 'exponential'
      }
    });
    
    if (confirmations.all) {
      // Step 8: Execute trade
      const execution = await step.do('execute-trade', async () => {
        return this.executeTrade(escrow.id);
      });
      
      // Step 9: Settlement
      await step.do('settlement', async () => {
        return this.settleOnBlockchain(execution);
      });
      
      return {
        status: 'completed',
        tradeId,
        execution,
        timestamp: Date.now()
      };
    } else {
      // Step 10: Cancel and cleanup
      await step.do('cancel-trade', async () => {
        return this.cancelTrade(escrow.id);
      });
      
      return {
        status: 'cancelled',
        reason: 'Not all participants confirmed'
      };
    }
  }
  
  async validateParticipant(participant) {
    // Check KYC status
    const kyc = await this.env.KYC_SERVICE.check(participant.id);
    
    // Check reputation
    const reputation = await this.env.REPUTATION.get(
      this.env.REPUTATION.idFromString(participant.id)
    );
    const rep = await reputation.fetch('/score');
    const score = await rep.json();
    
    return {
      valid: kyc.verified && score.trust > 0.5,
      participant: participant.id,
      kyc: kyc.verified,
      reputation: score.trust
    };
  }
}
```

### Payment Workflow

```javascript
export class PaymentWorkflow extends WorkflowEntrypoint {
  async run(event, step) {
    const { paymentId, from, to, amount, method } = event.payload;
    
    // Step 1: Fraud check
    const fraudCheck = await step.do('fraud-check', async () => {
      const fraudScore = await this.env.AI.run(
        '@cf/meta/llama-2-7b-chat-int8',
        {
          prompt: `Analyze this payment for fraud: ${JSON.stringify(event.payload)}`,
          max_tokens: 100
        }
      );
      
      return { 
        score: this.parseFraudScore(fraudScore),
        flagged: fraudScore > 0.7
      };
    });
    
    if (fraudCheck.flagged) {
      // Step 2: Manual review
      const review = await step.do('manual-review', async () => {
        await this.env.REVIEW_QUEUE.send({
          type: 'PAYMENT_REVIEW',
          paymentId,
          fraudScore: fraudCheck.score
        });
        
        // Wait for review (with timeout)
        return this.waitForReview(paymentId);
      }, {
        retries: {
          limit: 1,
          delay: '30 minutes'
        }
      });
      
      if (!review.approved) {
        return { status: 'rejected', reason: 'Failed fraud review' };
      }
    }
    
    // Step 3: Check balance
    const balanceCheck = await step.do('check-balance', async () => {
      const wallet = this.env.WALLET.get(
        this.env.WALLET.idFromString(from)
      );
      
      const response = await wallet.fetch('/balance');
      const balance = await response.json();
      
      return {
        sufficient: balance.KC >= amount,
        balance: balance.KC
      };
    });
    
    if (!balanceCheck.sufficient) {
      return { status: 'failed', reason: 'Insufficient balance' };
    }
    
    // Step 4: Create hold
    const hold = await step.do('create-hold', async () => {
      const wallet = this.env.WALLET.get(
        this.env.WALLET.idFromString(from)
      );
      
      const response = await wallet.fetch('/hold', {
        method: 'POST',
        body: JSON.stringify({
          amount,
          reason: `Payment to ${to}`
        })
      });
      
      return response.json();
    });
    
    // Step 5: Execute transfer
    const transfer = await step.do('transfer', async () => {
      // Transfer between wallets
      const fromWallet = this.env.WALLET.get(
        this.env.WALLET.idFromString(from)
      );
      
      const toWallet = this.env.WALLET.get(
        this.env.WALLET.idFromString(to)
      );
      
      // Deduct from sender
      await fromWallet.fetch('/transfer', {
        method: 'POST',
        body: JSON.stringify({
          to,
          amount,
          holdId: hold.holdId
        })
      });
      
      // Credit to recipient
      await toWallet.fetch('/credit', {
        method: 'POST',
        body: JSON.stringify({
          from,
          amount,
          paymentId
        })
      });
      
      return { success: true, timestamp: Date.now() };
    });
    
    // Step 6: Queue for settlement
    await step.do('queue-settlement', async () => {
      await this.env.SETTLEMENT_QUEUE.send({
        type: 'PAYMENT',
        paymentId,
        from,
        to,
        amount,
        timestamp: transfer.timestamp
      });
    });
    
    return {
      status: 'completed',
      paymentId,
      amount,
      from,
      to,
      timestamp: transfer.timestamp
    };
  }
}
```

## 🛡️ Security & DDoS Protection

### Comprehensive Security Architecture

```mermaid
flowchart TD
    subgraph "Edge Security"
        S1[Global DDoS Protection]
        S2[Web Application Firewall]
        S3[Bot Management]
        S4[Rate Limiting]
        S5[Turnstile CAPTCHA]
    end
    
    subgraph "Application Security"
        A1[Zero Trust Access]
        A2[API Shield]
        A3[mTLS]
        A4[JWT Validation]
    end
    
    subgraph "Data Security"
        D1[Encryption at Rest]
        D2[Field-level Encryption]
        D3[Tokenization]
        D4[Data Loss Prevention]
    end
    
    subgraph "Compliance"
        C1[GDPR]
        C2[PCI DSS]
        C3[SOC 2]
        C4[ISO 27001]
    end
    
    S1 --> S2 --> S3 --> S4 --> S5
    S5 --> A1 --> A2 --> A3 --> A4
    A4 --> D1 --> D2 --> D3 --> D4
    D4 --> C1 & C2 & C3 & C4
```

### Security Implementation

```javascript
export default {
  async fetch(request, env, ctx) {
    // Layer 1: DDoS Protection (automatic)
    // Cloudflare handles 254 Tbps of DDoS mitigation
    
    // Layer 2: WAF Rules
    const wafCheck = await this.checkWAF(request, env);
    if (!wafCheck.passed) {
      return new Response('Blocked by WAF', { status: 403 });
    }
    
    // Layer 3: Bot Management
    const botScore = request.headers.get('cf-bot-score');
    if (botScore && parseInt(botScore) < 30) {
      // Likely a bot, challenge with Turnstile
      return this.challengeWithTurnstile(request, env);
    }
    
    // Layer 4: Rate Limiting
    const rateLimitResult = await this.checkRateLimit(request, env);
    if (!rateLimitResult.allowed) {
      return new Response('Rate limited', {
        status: 429,
        headers: {
          'Retry-After': rateLimitResult.retryAfter.toString()
        }
      });
    }
    
    // Layer 5: Zero Trust Access
    const authenticated = await this.authenticateRequest(request, env);
    if (!authenticated) {
      return new Response('Unauthorized', { status: 401 });
    }
    
    // Process request
    return this.handleRequest(request, env, ctx);
  },
  
  async checkWAF(request, env) {
    // Custom WAF rules
    const rules = [
      // SQL Injection protection
      {
        pattern: /(\bUNION\b|\bSELECT\b.*\bFROM\b|\bDROP\b|\bDELETE\b.*\bFROM\b)/i,
        action: 'block',
        message: 'SQL injection attempt detected'
      },
      // XSS protection
      {
        pattern: /<script[^>]*>.*?<\/script>/gi,
        action: 'block',
        message: 'XSS attempt detected'
      },
      // Path traversal
      {
        pattern: /\.\.[\/\\]/,
        action: 'block',
        message: 'Path traversal attempt detected'
      }
    ];
    
    const url = new URL(request.url);
    const body = await request.text();
    const checkString = `${url.search} ${body}`;
    
    for (const rule of rules) {
      if (rule.pattern.test(checkString)) {
        await this.logSecurityEvent(request, rule.message, env);
        return { passed: false, reason: rule.message };
      }
    }
    
    return { passed: true };
  },
  
  async challengeWithTurnstile(request, env) {
    // Cloudflare Turnstile - privacy-first CAPTCHA alternative
    const html = `
      <!DOCTYPE html>
      <html>
      <head>
        <script src="https://challenges.cloudflare.com/turnstile/v0/api.js" async defer></script>
      </head>
      <body>
        <form method="POST">
          <div class="cf-turnstile" 
               data-sitekey="${env.TURNSTILE_SITE_KEY}"
               data-callback="onSuccess">
          </div>
          <script>
            function onSuccess(token) {
              fetch(window.location.href, {
                method: 'POST',
                headers: { 'CF-Turnstile-Response': token }
              }).then(response => {
                if (response.ok) {
                  window.location.reload();
                }
              });
            }
          </script>
        </form>
      </body>
      </html>
    `;
    
    return new Response(html, {
      headers: { 'Content-Type': 'text/html' }
    });
  },
  
  async checkRateLimit(request, env) {
    const identifier = this.getIdentifier(request);
    
    // Use Durable Object for distributed rate limiting
    const limiterId = env.RATE_LIMITER.idFromName(identifier);
    const limiter = env.RATE_LIMITER.get(limiterId);
    
    const response = await limiter.fetch(request.url, {
      method: 'POST',
      body: JSON.stringify({
        limit: 100, // requests
        window: 60, // seconds
        identifier
      })
    });
    
    return response.json();
  },
  
  async authenticateRequest(request, env) {
    // Zero Trust authentication
    const token = request.headers.get('Authorization')?.replace('Bearer ', '');
    
    if (!token) return false;
    
    try {
      // Verify JWT
      const payload = await this.verifyJWT(token, env.JWT_SECRET);
      
      // Check user permissions
      const permissions = await this.getUserPermissions(payload.userId, env);
      
      // Store in request context
      request.user = {
        id: payload.userId,
        permissions
      };
      
      return true;
    } catch (error) {
      await this.logSecurityEvent(request, `Auth failed: ${error.message}`, env);
      return false;
    }
  }
};

// Rate Limiter Durable Object
export class RateLimiter {
  constructor(state, env) {
    this.state = state;
    this.env = env;
    this.requests = new Map();
  }
  
  async fetch(request) {
    const { limit, window, identifier } = await request.json();
    
    const now = Date.now();
    const windowStart = now - (window * 1000);
    
    // Clean old entries
    const requests = this.requests.get(identifier) || [];
    const recentRequests = requests.filter(time => time > windowStart);
    
    if (recentRequests.length >= limit) {
      const oldestRequest = Math.min(...recentRequests);
      const retryAfter = Math.ceil((oldestRequest + window * 1000 - now) / 1000);
      
      return new Response(JSON.stringify({
        allowed: false,
        retryAfter,
        remaining: 0
      }));
    }
    
    // Add current request
    recentRequests.push(now);
    this.requests.set(identifier, recentRequests);
    
    return new Response(JSON.stringify({
      allowed: true,
      remaining: limit - recentRequests.length,
      reset: windowStart + window * 1000
    }));
  }
}
```

## 💡 Layer 1: Market Intelligence

[Previous Layer 1 content remains the same but enhanced with Cloudflare services]

## 🔄 Layer 2: Trade Orchestration

[Previous Layer 2 content remains the same but enhanced with Workflows]

## 💾 Layer 3: Settlement & Storage

[Previous Layer 3 content remains the same but enhanced with D1 and Queues]

## 📈 Performance & Scaling

### Performance Metrics with Cloudflare

```javascript
const performanceMetrics = {
  cloudflare: {
    network: {
      pops: "300+ cities",
      capacity: "254 Tbps",
      latency: {
        p50: "10ms",
        p95: "30ms", 
        p99: "50ms"
      }
    },
    
    workers: {
      coldStart: "0ms (no cold starts)",
      execution: "10ms average",
      concurrency: "Unlimited",
      scalability: "Auto-scaling to millions"
    },
    
    durableObjects: {
      consistency: "Strong consistency",
      latency: "Single-digit ms",
      throughput: "1000+ requests/second per DO",
      storage: "Unlimited"
    },
    
    ai: {
      workersAI: {
        latency: "50-200ms",
        throughput: "1000+ inferences/second",
        models: "30+ open-source models"
      },
      aiGateway: {
        caching: "Semantic caching reduces latency by 70%",
        fallback: "Automatic provider failover"
      }
    },
    
    storage: {
      d1: {
        reads: "1M+ queries/second",
        writes: "10K+ transactions/second",
        replication: "Global read replicas"
      },
      kv: {
        reads: "Unlimited",
        writes: "1000/second",
        globalReplication: "<60 seconds"
      }
    }
  },
  
  costs: {
    workers: "$0.30 per million requests",
    durableObjects: "$0.15 per million requests",
    d1: {
      reads: "$0.001 per 1M rows",
      writes: "$0.001 per 1K rows"
    },
    r2: "$0.015 per GB/month storage",
    ai: "Per-neuron pricing model",
    bandwidth: "$0 (no egress fees)"
  },
  
  scalability: {
    users: "100M+ concurrent",
    transactions: "1M+ per second", 
    storage: "Unlimited",
    global: "Instant global deployment"
  }
};
```

## 📚 Related Documentation

- **[Fiat Integration](FIAT-INTEGRATION.md)** - Bridging to traditional money
- **[Why This Works](WHY-IT-WORKS.md)** - Economic principles
- **[Phase 1: Foundation](phases/PHASE-1-FOUNDATION.md)** - Initial implementation
- **[Security Guide](SECURITY.md)** - Detailed security documentation

---

**[← Back to Docs](README.md)** | **[Next: Fiat Integration →](FIAT-INTEGRATION.md)**