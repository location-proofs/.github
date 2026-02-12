# Location Proofs

**Composable, verifiable location proof plugins for decentralized applications.**

---

## Overview

Location Proofs is an open ecosystem of location verification plugins that work with the [Astral SDK](https://github.com/DecentralizedGeo/astral-sdk). Each plugin implements a different proof-of-location system, from device-based GPS signatures to infrastructure network measurements.

**Multifactor location verification** - Combine multiple proof types to increase confidence:
- 🔐 **Device-based** - ProofMode (PGP-signed GPS + hardware attestation)
- 🌐 **Infrastructure-based** - WitnessChain (network latency triangulation)
- 🛰️ **Future** - Satellite signals, WiFi positioning, zkSNARKs, and more

## Available Plugins

| Plugin | Type | Status | NPM |
|--------|------|--------|-----|
| [**plugin-proofmode**](https://github.com/location-proofs/plugin-proofmode) | Device-based | ✅ Alpha | [`@location-proofs/plugin-proofmode`](https://www.npmjs.com/package/@location-proofs/plugin-proofmode) |
| [**plugin-witnesschain**](https://github.com/location-proofs/plugin-witnesschain) | Infrastructure | 🚧 In Development | *Not yet published* |

## Quick Start

```bash
npm install @decentralized-geo/astral-sdk @location-proofs/plugin-proofmode
```

```typescript
import { AstralSDK } from '@decentralized-geo/astral-sdk';
import { ProofModePlugin } from '@location-proofs/plugin-proofmode';

const astral = new AstralSDK({ chainId: 84532, signer: wallet });

// Register plugin
astral.plugins.register(new ProofModePlugin());

// Create location proof
const claim = {
  location: { type: 'Point', coordinates: [-73.9857, 40.7484] },
  radius: 100,
  time: { start: Date.now() / 1000, end: Date.now() / 1000 + 3600 }
};

const stamp = await astral.stamps.create('proofmode', proofModeSignals);
const proof = astral.proofs.create(claim, [stamp]);

// SDK evaluates credibility
const credibility = await astral.proofs.verify(proof);
console.log(credibility.score); // Multidimensional credibility vector
```

## Architecture

```
┌─────────────────────────────────────────┐
│     @decentralized-geo/astral-sdk       │  Core framework
│  (Plugin interface, proof verification) │
└─────────────────────────────────────────┘
                    ▲
                    │ implements
                    │
    ┌───────────────┴───────────────┬─────────────────┐
    │                               │                 │
┌───────────────┐        ┌──────────────────┐   ┌─────────┐
│  ProofMode    │        │  WitnessChain    │   │ Custom  │
│   Plugin      │        │     Plugin       │   │ Plugins │
└───────────────┘        └──────────────────┘   └─────────┘
  Device-based           Infrastructure-based    Community
```

**Plugins are responsible for:**
- ✅ Signal collection (GPS, network data, etc.)
- ✅ Stamp creation (wrapping raw signals in standard format)
- ✅ Internal verification (signatures, structure, signal consistency)

**SDK is responsible for:**
- ✅ Spatial/temporal evaluation (haversine distance, overlap)
- ✅ Multidimensional credibility scoring
- ✅ Cross-stamp correlation analysis
- ✅ On-chain attestation (via EAS)

## Plugin Comparison

| Aspect | ProofMode | WitnessChain |
|--------|-----------|--------------|
| **Trust Model** | Device self-signed | External validators |
| **Privacy** | High (on-device) | Medium (IP exposed) |
| **Accuracy** | GPS precision (~5-50m) | City/region (~10-100km) |
| **Hardware Required** | Smartphone | Any internet-connected device |
| **Verification Cost** | Low (crypto + SafetyNet check) | Medium (network measurements) |
| **Best For** | Photo/video geotagging | DePIN node verification |

**Multifactor approach:** Combine both to get high-precision device proof + independent infrastructure validation.

## Contributing a Plugin

Want to add a new proof-of-location system? See the [Plugin Development Guide](https://docs.astral.global/plugins/development).

**Plugin interface:**
```typescript
interface LocationProofPlugin {
  readonly name: string;
  readonly version: string;
  readonly runtimes: Runtime[];

  // Optional: Collect raw signals
  collect?(options?: CollectOptions): Promise<RawSignals>;

  // Required: Transform signals into stamps
  create(signals: RawSignals): Promise<UnsignedLocationStamp>;

  // Optional: Sign stamps
  sign?(stamp: UnsignedLocationStamp, signer: StampSigner): Promise<LocationStamp>;

  // Required: Verify stamp validity
  verify(stamp: LocationStamp): Promise<StampVerificationResult>;
}
```

## Research

Explore the academic foundations of proof-of-location systems:
- [**location-proofs-research**](https://github.com/location-proofs/location-proofs-research) - Survey of PoL systems, threat models, and verification methods

## Resources

- 📚 [Documentation](https://docs.astral.global)
- 🔧 [Astral SDK](https://github.com/DecentralizedGeo/astral-sdk)
- 💬 [Discord](https://discord.gg/astral) *(coming soon)*
- 🐦 [Twitter](https://twitter.com/AstralProtocol)

## Ecosystem

Location Proofs is part of the broader Astral ecosystem:
- [**Astral Protocol**](https://astral.global) - Verifiable location-based services
- [**DecentralizedGeo**](https://github.com/DecentralizedGeo) - Decentralized geospatial infrastructure
- [**Astral SDK**](https://github.com/DecentralizedGeo/astral-sdk) - Core SDK for location proofs

## License

All plugins are MIT licensed. See individual repositories for details.

---

**Built with ❤️ by the Astral community**
