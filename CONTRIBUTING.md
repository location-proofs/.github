# Contributing to Location Proofs

Thank you for your interest in contributing to the Location Proofs ecosystem!

## Ways to Contribute

- 🐛 **Report bugs** - Open issues for bugs or unexpected behavior
- 💡 **Propose features** - Suggest new plugin types or improvements
- 📝 **Improve documentation** - Fix typos, add examples, clarify concepts
- 🔌 **Build plugins** - Implement new proof-of-location systems
- 🧪 **Write tests** - Improve test coverage and verification quality

## Plugin Development

### Prerequisites

- Node.js ≥ 18
- pnpm (recommended) or npm
- Familiarity with TypeScript
- Understanding of location proof systems

### Creating a New Plugin

1. **Fork the template** (coming soon) or study existing plugins:
   - [`plugin-proofmode`](https://github.com/location-proofs/plugin-proofmode) - Device-based proofs
   - [`plugin-witnesschain`](https://github.com/location-proofs/plugin-witnesschain) - Infrastructure proofs

2. **Implement the interface:**
   ```typescript
   import type { LocationProofPlugin } from '@decentralized-geo/astral-sdk/plugins';

   export class MyPlugin implements LocationProofPlugin {
     readonly name = 'my-plugin';
     readonly version = '0.1.0';
     readonly runtimes = ['node', 'browser'];

     async create(signals: RawSignals): Promise<UnsignedLocationStamp> {
       // Transform your proof system's signals into a LocationStamp
     }

     async verify(stamp: LocationStamp): Promise<StampVerificationResult> {
       // Validate signatures, structure, signal consistency
     }
   }
   ```

3. **Write tests:**
   - Unit tests for all public methods
   - Integration tests with real proof data
   - Test fixtures for common scenarios

4. **Document:**
   - README with installation, usage, API reference
   - JSDoc comments on all public methods
   - Examples for common use cases

5. **Submit:**
   - Open an issue to discuss the plugin
   - Create a PR with your implementation
   - Respond to review feedback

### Plugin Guidelines

**Do:**
- ✅ Follow the `LocationProofPlugin` interface exactly
- ✅ Return detailed verification results (not just boolean)
- ✅ Handle errors gracefully with descriptive messages
- ✅ Include type definitions for all public APIs
- ✅ Test with real proof data from your system
- ✅ Document trust assumptions and limitations

**Don't:**
- ❌ Implement evaluation logic (SDK handles this)
- ❌ Add blockchain dependencies (keep plugins chain-agnostic)
- ❌ Include large dependencies unnecessarily
- ❌ Make breaking changes without major version bump

### Verification vs Evaluation

**Plugins implement `verify()` for internal validation:**
- Check cryptographic signatures
- Validate signal structure and ranges
- Verify proof system-specific invariants
- Return detailed verification results

**SDK handles `evaluate()` for credibility scoring:**
- Spatial distance measurements
- Temporal overlap calculations
- Multidimensional credibility vectors
- Cross-stamp correlation

This separation keeps plugins focused on proof-system-specific logic while the SDK provides generalized evaluation.

## Code Style

- **TypeScript:** Strict mode, no `any` types
- **Formatting:** Prettier with default settings
- **Linting:** ESLint with recommended rules
- **Naming:** camelCase for variables/functions, PascalCase for classes/types
- **Commits:** Conventional commits format (`feat:`, `fix:`, `docs:`, etc.)

## Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation only
- `refactor:` - Code refactoring
- `test:` - Add/update tests
- `chore:` - Maintenance tasks

**Example:**
```
feat(verify): add SafetyNet attestation validation

Verify SafetyNet JWT signature and check device integrity
claims. Adds bonus credibility for passing attestation.

Refs: #42
```

## Testing

Run tests before submitting:

```bash
pnpm test          # Run all tests
pnpm test:watch    # Watch mode
pnpm typecheck     # Type checking
pnpm lint          # Linting
```

**Test coverage expectations:**
- All public methods must have tests
- Edge cases and error paths must be covered
- Integration tests with real proof data preferred

## Pull Request Process

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feat/my-feature`)
3. **Commit** your changes with conventional commits
4. **Push** to your fork
5. **Open** a pull request with:
   - Clear description of changes
   - Link to related issues
   - Test results/screenshots if applicable

6. **Respond** to review feedback
7. **Merge** after approval (maintainers will merge)

## Code Review

All submissions require review. We look for:
- ✅ Correct implementation of plugin interface
- ✅ Comprehensive tests
- ✅ Clear documentation
- ✅ No breaking changes (or properly versioned)
- ✅ Clean, readable code

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

## Questions?

- Open an issue for technical questions
- Join our Discord (coming soon) for real-time discussion
- Email: [developers@astral.global](mailto:developers@astral.global)

---

**Thank you for helping build the future of verifiable location!** 🌍
