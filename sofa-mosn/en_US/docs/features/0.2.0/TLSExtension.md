# 0.2.0 TLS extension
## Extended functions
+ Extension to get `tls.Certificate`. Golang natively supports obtaining certificate or private key in plaintext only.
     + `tls.Certificate` is returned by extension, and the `PrivateKey` in the returned `tls.Certificate` can also be extended to meet the keyless requirement.
     
+ Extension of the `VerifyPeerCertificate` function, which is an extension supported by Golang's `tls.Config`. The function can modify the method to verify TLS certificate.
+ Extension to get trusted CA

## Reconstruction results
+ Added new extensions and extended Factory. TLS gets the factory according to the registered type when it is in use, and then generates the corresponding extension according to the configuration to provide the TLS certificate.
+ Relevant configuration files and the configuration file parsing changed.
+ Context and contextManager changed.
+ `tls.Config` conducts processing on the extension.