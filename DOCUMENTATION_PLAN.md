# Documentation Plan: Location Proofs Plugin Ecosystem

## Overview

Integrate the new `@location-proofs` plugin ecosystem into mintlify-docs, reflecting the architectural separation between SDK (framework) and plugins (implementations).

## Current State

**Existing docs in mintlify-docs:**
- `concepts/location-proofs.mdx` - Conceptual overview
- `concepts/plugins.mdx` - Plugin architecture
- `guides/proofmode-integration.mdx` - ProofMode usage guide
- `guides/witnesschain-integration.mdx` - WitnessChain usage guide
- `guides/writing-plugins.mdx` - Plugin development guide

**Navigation structure:**
```json
{
  "group": "Core Concepts",
  "pages": [
    "concepts/location-proofs",
    "concepts/plugins"
  ]
},
{
  "group": "Guides",
  "pages": [
    "guides/creating-location-proofs",
    "guides/proofmode-integration",
    "guides/witnesschain-integration",
    "guides/writing-plugins"
  ]
}
```

## Proposed Updates

### 1. Update Existing Content

#### `concepts/plugins.mdx`
**Current:** Likely explains plugin architecture conceptually
**Update to include:**
- New architectural separation (verify vs evaluate)
- `@location-proofs` ecosystem introduction
- Link to https://github.com/location-proofs
- Plugin comparison table (ProofMode vs WitnessChain)
- NPM package installation instructions

**Key points to emphasize:**
```markdown
## Plugin Architecture

Plugins handle **internal validation**:
- Cryptographic signature verification
- Signal structure and consistency checks
- Proof system-specific invariants

The SDK handles **evaluation**:
- Spatial distance measurements (haversine)
- Temporal overlap calculations
- Multidimensional credibility scoring
- Cross-stamp correlation

## Available Plugins

| Plugin | NPM Package | Repository | Type |
|--------|-------------|------------|------|
| ProofMode | `@location-proofs/plugin-proofmode` | [GitHub](https://github.com/location-proofs/plugin-proofmode) | Device-based |
| WitnessChain | `@location-proofs/plugin-witnesschain` | [GitHub](https://github.com/location-proofs/plugin-witnesschain) | Infrastructure |
```

#### `guides/proofmode-integration.mdx`
**Update:**
- Change imports from `@decentralized-geo/plugin-proofmode` → `@location-proofs/plugin-proofmode`
- Remove any evaluation examples (now handled by SDK)
- Add destructured vs un-destructured verification paths (Phase 2-3 feature)
- Link to plugin README on GitHub

**Example structure:**
```markdown
---
title: "ProofMode Integration"
description: "Use ProofMode for device-based location proofs"
---

## Installation

\`\`\`bash
npm install @location-proofs/plugin-proofmode
\`\`\`

## What is ProofMode?

ProofMode is a Guardian Project mobile app that captures cryptographically
verifiable location and sensor data when photos/videos are taken...

## Quick Start

\`\`\`typescript
import { ProofModePlugin } from '@location-proofs/plugin-proofmode';
import { AstralSDK } from '@decentralized-geo/astral-sdk';

const astral = new AstralSDK({ chainId: 84532, signer: wallet });
astral.plugins.register(new ProofModePlugin());

// Parse ProofMode bundle
const plugin = new ProofModePlugin();
const bundle = plugin.parseBundle(zipData);
const stamp = plugin.createStampFromBundle(bundle);

// Create proof
const claim = { ... };
const proof = astral.proofs.create(claim, [stamp]);

// SDK evaluates credibility
const credibility = await astral.proofs.verify(proof);
\`\`\`

## Verification vs Evaluation

The plugin's \`verify()\` method checks:
- PGP signature validity
- SafetyNet JWT structure
- Signal consistency

The SDK's \`proofs.verify()\` method evaluates:
- Spatial accuracy (distance from claim)
- Temporal overlap
- Multifactor credibility

## API Reference

[Full API documentation](https://github.com/location-proofs/plugin-proofmode#api-reference)
```

#### `guides/witnesschain-integration.mdx`
**Update:**
- Change imports from `@decentralized-geo/plugin-witnesschain` → `@location-proofs/plugin-witnesschain`
- Remove evaluation examples
- Add configuration options (proverId, apiUrl)
- Note about historical vs live challenges (POINTS tokens)

#### `guides/writing-plugins.mdx`
**Update:**
- Remove `evaluate()` from plugin interface examples
- Emphasize verify = internal validation only
- Link to location-proofs CONTRIBUTING.md
- Add section on publishing to `@location-proofs` scope

**Example interface documentation:**
```typescript
interface LocationProofPlugin {
  // Metadata
  readonly name: string;
  readonly version: string;
  readonly runtimes: Runtime[];

  // Optional: Collect raw signals from proof system
  collect?(options?: CollectOptions): Promise<RawSignals>;

  // Required: Transform signals into LocationStamp
  create(signals: RawSignals): Promise<UnsignedLocationStamp>;

  // Optional: Add cryptographic signature
  sign?(stamp: UnsignedLocationStamp, signer: StampSigner): Promise<LocationStamp>;

  // Required: Verify internal validity (signatures, structure, consistency)
  verify(stamp: LocationStamp): Promise<StampVerificationResult>;

  // NOTE: evaluate() removed - SDK handles evaluation
}
```

### 2. Add New Content

#### `plugins/overview.mdx` (NEW)
Create a dedicated "Plugins" section in navigation with overview page.

**Content:**
- What are location proof plugins?
- Why multifactor verification?
- Plugin ecosystem architecture diagram
- Available plugins table
- Quick start with multiple plugins
- Link to location-proofs org

**Navigation addition:**
```json
{
  "group": "Plugins",
  "pages": [
    "plugins/overview",
    "plugins/proofmode",
    "plugins/witnesschain",
    "plugins/development",
    "plugins/publishing"
  ]
}
```

#### `plugins/proofmode.mdx` (NEW)
Dedicated ProofMode plugin reference (more detailed than the guide).

**Content:**
- Installation
- Configuration options
- API reference (all methods)
- Verification process details
- SafetyNet attestation explanation
- OpenTimestamps proof explanation
- Bundle structure breakdown
- Trust model and limitations

#### `plugins/witnesschain.mdx` (NEW)
Dedicated WitnessChain plugin reference.

**Content:**
- Installation
- Configuration (proverId, POINTS tokens)
- API reference
- Challenge process explanation
- RTT measurement details
- IP geolocation verification
- Trust model and limitations
- Comparison with device-based proofs

#### `plugins/development.mdx` (NEW)
Comprehensive plugin development guide (expanded from existing `guides/writing-plugins.mdx`).

**Content:**
- Plugin interface detailed spec
- Verification vs evaluation architecture
- Signal structure best practices
- Testing strategies
- Example plugin walkthrough
- Publishing to `@location-proofs`
- CI/CD setup

#### `plugins/publishing.mdx` (NEW)
Guide for publishing community plugins.

**Content:**
- Fork plugin template
- NPM scope options (`@location-proofs` vs custom)
- GitHub repo setup
- README requirements
- CI/CD setup (tests, type checking, publishing)
- Submitting to location-proofs org

### 3. Navigation Restructure

**Before:**
```json
"navigation": [
  {
    "group": "Core Concepts",
    "pages": [
      "concepts/location-proofs",
      "concepts/plugins"
    ]
  },
  {
    "group": "Guides",
    "pages": [
      "guides/proofmode-integration",
      "guides/witnesschain-integration",
      "guides/writing-plugins"
    ]
  }
]
```

**After:**
```json
"navigation": [
  {
    "group": "Core Concepts",
    "pages": [
      "concepts/location-attestations",
      "concepts/geospatial-operations",
      "concepts/policy-attestations",
      "concepts/eas-resolvers",
      "concepts/verifiable-computation",
      "concepts/location-proofs"  // Keep high-level concept
    ]
  },
  {
    "group": "Plugins",
    "pages": [
      "plugins/overview",
      "plugins/proofmode",
      "plugins/witnesschain",
      {
        "group": "Development",
        "pages": [
          "plugins/development",
          "plugins/publishing"
        ]
      }
    ]
  },
  {
    "group": "Guides",
    "pages": [
      "guides/location-gated-nft",
      "guides/geofenced-token",
      "guides/delivery-verification",
      "guides/creating-location-proofs"
      // Remove plugin integration guides (now in Plugins section)
    ]
  }
]
```

### 4. Update SDK Reference

#### `sdk/overview.mdx`
**Add section:**
```markdown
## Plugin System

The SDK provides a plugin registry for location proof systems:

\`\`\`typescript
import { AstralSDK } from '@decentralized-geo/astral-sdk';
import { ProofModePlugin } from '@location-proofs/plugin-proofmode';
import { WitnessChainPlugin } from '@location-proofs/plugin-witnesschain';

const astral = new AstralSDK({ chainId: 84532, signer: wallet });

// Register plugins
astral.plugins.register(new ProofModePlugin());
astral.plugins.register(new WitnessChainPlugin({ proverId: '0x...' }));

// Use plugins via namespace
const stamps = await Promise.all([
  astral.stamps.collect('proofmode'),
  astral.stamps.collect('witnesschain')
]);
\`\`\`

See [Plugins](/plugins/overview) for available plugins.
```

### 5. Add Cross-Links

**Update all relevant pages to link:**
- Plugin GitHub repos → https://github.com/location-proofs/plugin-*
- Org profile → https://github.com/location-proofs
- Contributing guide → https://github.com/location-proofs/.github/blob/main/CONTRIBUTING.md
- Research repo → https://github.com/location-proofs/location-proofs-research

## Implementation Phases

### Phase 1: Update Existing Content (Immediate)
- [ ] Update `concepts/plugins.mdx` - Add `@location-proofs` ecosystem, architecture changes
- [ ] Update `guides/proofmode-integration.mdx` - Change imports, remove evaluate
- [ ] Update `guides/witnesschain-integration.mdx` - Change imports, remove evaluate
- [ ] Update `guides/writing-plugins.mdx` - Remove evaluate from interface
- [ ] Update `sdk/overview.mdx` - Add plugin system section

### Phase 2: Add Plugin Reference Section (Week 1)
- [ ] Create `plugins/overview.mdx` - Ecosystem overview
- [ ] Create `plugins/proofmode.mdx` - ProofMode detailed reference
- [ ] Create `plugins/witnesschain.mdx` - WitnessChain detailed reference
- [ ] Update navigation in `mint.json`

### Phase 3: Add Development Guides (Week 2)
- [ ] Create `plugins/development.mdx` - Plugin development guide
- [ ] Create `plugins/publishing.mdx` - Publishing guide
- [ ] Add plugin template repo (optional)

### Phase 4: Enhancements (Future)
- [ ] Add interactive plugin demo (playground integration)
- [ ] Add plugin comparison matrix (interactive table)
- [ ] Add verification flow diagrams (Mermaid.js)
- [ ] Add video tutorials for plugin usage

## Key Messaging

**Emphasize throughout docs:**
1. **Multifactor verification** - Combining plugins increases confidence
2. **Composability** - Plugins are independent, swappable components
3. **Community ecosystem** - `@location-proofs` is open to contributions
4. **Clear separation** - Plugins verify, SDK evaluates
5. **Trust model transparency** - Each plugin has different trust assumptions

## Migration Notes

**Breaking changes:**
- Package names changed from `@decentralized-geo/plugin-*` to `@location-proofs/plugin-*`
- `evaluate()` method removed from plugin interface
- Evaluation now happens at SDK level via `proofs.verify()`

**Update code examples:**
```diff
- import { ProofModePlugin } from '@decentralized-geo/plugin-proofmode';
+ import { ProofModePlugin } from '@location-proofs/plugin-proofmode';

- const credibility = await plugin.evaluate(stamp, claim);
+ const proof = astral.proofs.create(claim, [stamp]);
+ const credibility = await astral.proofs.verify(proof);
```

## Success Metrics

- [ ] All plugin examples use `@location-proofs` scope
- [ ] No references to removed `evaluate()` method
- [ ] Clear distinction between verify (plugin) and evaluate (SDK)
- [ ] Links to location-proofs GitHub org work
- [ ] Navigation flows logically (concept → plugin → guide → reference)

## Next Steps

1. Review this plan with team
2. Create issues for each phase
3. Assign documentation tasks
4. Set up docs review process
5. Launch alongside plugin v0.1.0 release
