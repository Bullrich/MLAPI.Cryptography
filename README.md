Fork of [MidLevel/MLAPI.Cryptography](https://github.com/MidLevel/MLAPI.Cryptography/) adapted to make it work as a OpenUPM

---

# MLAPI.Cryptography
MLAPI.Cryptography is a Unity friendly crypto library to fill the missing features of Unity's Mono runtime framework.

Currently it offers a BigInt, ECDHE, ECDHE_RSA and EllipticCurve implementation. Note that MLAPI.Cryptography is **NOT** designed to be an extensive crypto library such as NaCL or replace the .NET Framework. It's simply there to fill the missing gaps in the Unity Engine. Behind the scenes, MLAPI.Cryptography will use as much of the avalible .NET surface as possible. An example of this is the ECDHE_RSA implementation which uses MLAPI.Cryptographys BigInt, EllipticCurve and DiffieHellman implementations while it uses .NET's RSA implementation.


### ECDHE Usage
```csharp
// Both create their instances
ECDiffieHellman serverDiffie = new ECDiffieHellman();
ECDiffieHellman clientDiffie = new ECDiffieHellman();

// Exchange publics

/* START TRANSMISSION */
byte[] serverPublic = serverDiffie.GetPublicKey();
byte[] clientPublic = clientDiffie.GetPublicKey();
/* END TRANSMISSION */

// Calculate shared
byte[] key1 = serverDiffie.GetSharedSecretRaw(clientPublic);
byte[] key2 = clientDiffie.GetSharedSecretRaw(serverPublic);
```

### ECDHERSA Usage
```csharp
// Key pairs
RSAParameters privateKey;
RSAParameters publicKey;

// Generate keys, you can use X509Certificate2 instead of raw RSA keys.
using (RSACryptoServiceProvider rsaGen = new RSACryptoServiceProvider(2048))
{
    privateKey = rsaGen.ExportParameters(true);
    publicKey = rsaGen.ExportParameters(false);
}

using (RSACryptoServiceProvider serverRSA = new RSACryptoServiceProvider())
using (RSACryptoServiceProvider clientRSA = new RSACryptoServiceProvider())
{
    serverRSA.ImportParameters(privateKey);
    clientRSA.ImportParameters(publicKey);

    // Both create their instances, constructor can take certificate instead or RSA key.
    ECDiffieHellmanRSA serverDiffie = new ECDiffieHellmanRSA(serverRSA);
    ECDiffieHellmanRSA clientDiffie = new ECDiffieHellmanRSA(clientRSA);

    // Exchange publics

    /* START TRANSMISSION */
    byte[] serverPublic = serverDiffie.GetSecurePublicPart();
    byte[] clientPublic = clientDiffie.GetSecurePublicPart();
    /* END TRANSMISSION */

    // Calculate shared
    byte[] key1 = serverDiffie.GetVerifiedSharedPart(clientPublic);
    byte[] key2 = clientDiffie.GetVerifiedSharedPart(serverPublic);
}
```
### Timing Side Channel Attack Prevention
```csharp
byte[] array1 = new byte[120];
byte[] array2 = new byte[120];
array[50] = 67;

// This comparison will take constant time, no matter where the diff is (if any).
bool equal = ComparisonUtils.ConstTimeArrayEqual(array1, array2);
```

---

## Installation

### Adding the package to the Unity project manifest

* Navigate to the `Packages` directory of your project.
* Adjust the [project manifest file][Project-Manifest] `manifest.json` in a text editor.
    * Ensure `https://package.openupm.com` is part of `scopedRegistries`.
        * Ensure `io.midlevel.mlapi.cryptography` is part of `scopes`.
    * Add `io.midlevel.mlapi.cryptography` to `dependencies`, stating the latest version.

  A minimal example ends up looking like this.
  Please note that the version `X.Y.Z` stated here is to be replaced with the latest released version which is currently [![Release][Version-Release]][Releases].
  ```json
  {
    "scopedRegistries": [
      {
        "name": "package.openupm.com",
        "url": "https://package.openupm.com",
        "scopes": [
          "io.midlevel.mlapi.cryptography",
          "com.openupm"
        ]
      }
    ],
    "dependencies": {
      "io.midlevel.mlapi.cryptography": "X.Y.Z",
    }
  }
  ```
* Switch back to the Unity software and wait for it to finish importing the added package.

[Project-Manifest]: https://docs.unity3d.com/Manual/upm-manifestPrj.html
[Version-Release]: https://img.shields.io/npm/v/io.midlevel.mlapi.cryptography?label=openupm&registry_uri=https://package.openupm.com
[Releases]: https://openupm.com/packages/io.midlevel.mlapi.cryptography/