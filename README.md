# OQS - Post-Quantum Cryptography for Dart

[![pub package](https://img.shields.io/pub/v/oqs.svg)](https://pub.dev/packages/oqs)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Dart](https://img.shields.io/badge/dart-%3E%3D2.17.0-brightgreen.svg)](https://dart.dev)

A Dart FFI wrapper for [liboqs](https://github.com/open-quantum-safe/liboqs), providing access to post-quantum cryptographic algorithms including key encapsulation mechanisms (KEMs) and digital signatures.

## Features

- 🔐 **Key Encapsulation Mechanisms (KEMs)**: ML-KEM (Kyber), Classic McEliece, FrodoKEM, NTRU Prime, and more
- ✍️ **Digital Signatures**: ML-DSA (Dilithium), Falcon, SPHINCS+, and other post-quantum signature schemes
- 🌐 **Cross-Platform**: Support for Android, iOS, Linux, macOS, and Windows
- 🚀 **High Performance**: Direct FFI bindings with minimal overhead
- 🔧 **Flexible Loading**: Multiple library loading strategies with automatic fallbacks
- 📱 **Mobile Ready**: Optimized for Flutter applications

## Supported Algorithms

### Key Encapsulation Mechanisms (KEMs)
- ML-KEM-512, ML-KEM-768, ML-KEM-1024 (NIST standardized)
- Classic McEliece variants
- FrodoKEM variants
- NTRU Prime variants
- And more...

### Digital Signatures
- ML-DSA-44, ML-DSA-65, ML-DSA-87 (NIST standardized)
- Falcon-512, Falcon-1024
- SPHINCS+ variants
- Cross-Tree signatures
- And more...

## Installation

Add this to your package's `pubspec.yaml`:

```yaml
dependencies:
  oqs: ^1.0.0
```

Then run:

```bash
dart pub get
```

## Quick Start

### Key Encapsulation (KEM) Example

```dart
import 'package:oqs/oqs.dart';

void main() {
  // Initialize a KEM instance
  final kem = KEM('ML-KEM-768');
  
  // Generate a key pair
  final keyPair = kem.generateKeyPair();
  print('Public key length: ${keyPair.publicKey.length}');
  print('Secret key length: ${keyPair.secretKey.length}');
  
  // Encapsulate a shared secret
  final encapsulationResult = kem.encapsulate(keyPair.publicKey);
  print('Ciphertext length: ${encapsulationResult.ciphertext.length}');
  print('Shared secret length: ${encapsulationResult.sharedSecret.length}');
  
  // Decapsulate the shared secret
  final decapsulatedSecret = kem.decapsulate(
    encapsulationResult.ciphertext, 
    keyPair.secretKey
  );
  
  // Verify the shared secrets match
  print('Secrets match: ${_listsEqual(encapsulationResult.sharedSecret, decapsulatedSecret)}');
}

bool _listsEqual(List<int> a, List<int> b) {
  if (a.length != b.length) return false;
  for (int i = 0; i < a.length; i++) {
    if (a[i] != b[i]) return false;
  }
  return true;
}
```

### Digital Signature Example

```dart
import 'package:oqs/oqs.dart';
import 'dart:convert';

void main() {
  // Initialize a signature instance
  final sig = Signature('ML-DSA-65');
  
  // Generate a key pair
  final keyPair = sig.generateKeyPair();
  
  // Message to sign
  final message = utf8.encode('Hello, post-quantum world!');
  
  // Sign the message
  final signature = sig.sign(message, keyPair.secretKey);
  print('Signature length: ${signature.length}');
  
  // Verify the signature
  final isValid = sig.verify(message, signature, keyPair.publicKey);
  print('Signature valid: $isValid');
}
```

## Platform Setup

### Android

For Android applications, the native libraries are automatically included. The package supports the following architectures:

- `arm64-v8a` (64-bit ARM)
- `armeabi-v7a` (32-bit ARM)
- `x86_64` (64-bit Intel)
- `x86` (32-bit Intel)

### iOS

For iOS, add the following to your `ios/Runner/Info.plist` if you encounter any issues:

```xml
<key>CFBundleSupportedPlatforms</key>
<array>
    <string>iPhoneOS</string>
</array>
```

### Desktop Platforms

For Linux, macOS, and Windows, ensure the native libraries are accessible. The package will automatically locate them in most standard configurations.

## Advanced Usage

### Custom Library Path

You can specify a custom path to the liboqs library:

```dart
import 'package:oqs/oqs.dart';

void main() {
  // Load library from custom path
  final library = LibOQSLoader.loadLibrary(
    explicitPath: '/path/to/your/liboqs.so'
  );
  
  // Use with custom library
  final kem = KEM('ML-KEM-768', library: library);
}
```

### Environment Variable

Set the `LIBOQS_PATH` environment variable to specify library location:

```bash
export LIBOQS_PATH=/usr/local/lib/liboqs.so
dart run your_app.dart
```

### Available Algorithms

Get lists of supported algorithms at runtime:

```dart
import 'package:oqs/oqs.dart';

void main() {
  // List all supported KEMs
  final kemAlgorithms = KEM.supportedAlgorithms();
  print('Supported KEMs: $kemAlgorithms');
  
  // List all supported signature algorithms  
  final sigAlgorithms = Signature.supportedAlgorithms();
  print('Supported signatures: $sigAlgorithms');
  
  // Check if a specific algorithm is supported
  print('ML-KEM-768 supported: ${KEM.isSupported('ML-KEM-768')}');
}
```

## Performance Considerations

- **Memory Management**: The package handles memory allocation/deallocation automatically
- **Thread Safety**: liboqs operations are generally thread-safe, but avoid sharing instances across isolates
- **Key Reuse**: Generate fresh key pairs for each session when possible
- **Algorithm Selection**: ML-KEM and ML-DSA are NIST-standardized and recommended for new applications

## Security Notes

⚠️ **Important Security Considerations:**

- This package provides access to **experimental post-quantum algorithms**
- Some algorithms may not be suitable for production use
- Always validate algorithm choices against current security recommendations
- Keep the liboqs library updated to the latest version
- Use cryptographically secure random number generators for key generation
- Properly dispose of secret key material

## Building from Source

To build the native liboqs library:

1. Clone the liboqs repository:
```bash
git clone https://github.com/open-quantum-safe/liboqs.git
```

2. Build for your target platform:
```bash
cd liboqs
mkdir build && cd build
cmake -GNinja -DCMAKE_INSTALL_PREFIX=../install ..
ninja install
```

3. Copy the resulting libraries to the appropriate `bin/` directory in your project.


### Development Setup

1. Fork and clone the repository
2. Install dependencies: `dart pub get`
3. Run tests: `dart test`
4. Ensure code is formatted: `dart format .`
5. Run static analysis: `dart analyze`

## Examples

Check out the [example](example/) directory for more comprehensive examples including:

- Basic KEM and signature operations
- Performance benchmarking
- Error handling patterns
- Platform-specific considerations

## Troubleshooting

### Library Loading Issues

If you encounter library loading errors:

1. **Check library path**: Verify the liboqs library is in the expected location
2. **Set environment variable**: Use `LIBOQS_PATH` to specify custom location
3. **Verify architecture**: Ensure library matches your platform architecture
4. **Check dependencies**: Ensure all required system dependencies are installed

### Common Error Messages

- `LibraryLoadException: Failed to load liboqs library`: Library not found or incompatible
- `UnsupportedError: Algorithm 'X' is not supported`: Algorithm not compiled into your liboqs build
- `ArgumentError: Invalid key length`: Key material doesn't match expected size for algorithm

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- [Open Quantum Safe](https://openquantumsafe.org/) project for liboqs
- NIST Post-Quantum Cryptography Standardization effort
- The Dart and Flutter communities

## Related Projects

- [liboqs](https://github.com/open-quantum-safe/liboqs) - The underlying C library
- [OQS-OpenSSL](https://github.com/open-quantum-safe/openssl) - OpenSSL integration
- [Post-Quantum Cryptography](https://csrc.nist.gov/projects/post-quantum-cryptography) - NIST PQC project

---

For more information about post-quantum cryptography and the algorithms provided by this package, visit [OpenQuantumSafe.org](https://openquantumsafe.org/).