DID Resolution Security and Trust Profile
=========================================

**Specification Status:** [Draft](https://github.com/decentralized-identity/org/blob/master/work-item-lifecycle.md)

**Latest Draft:**
  [https://peacekeeper.github.io/did-resolution-security-trust-profile/](https://peacekeeper.github.io/did-resolution-security-trust-profile/)

**Editors:**
~ [Markus Sabadello](https://www.linkedin.com/in/markus-sabadello-353a0821/) (Danube Tech)
~ Others?
<!-- -->
**Authors:**
~ [Markus Sabadello](https://www.linkedin.com/in/markus-sabadello-353a0821/) (Danube Tech)
~ Others?
<!-- -->
**Participate:**
~ [GitHub repo](https://github.com/peacekeeper/did-resolution-security-trust-profile)
~ [File a bug](https://github.com/peacekeeper/did-resolution-security-trust-profile/issues)
~ [Commit history](https://github.com/peacekeeper/did-resolution-security-trust-profile/commits/master)

------------------------------------

## Abstract

[Decentralized identifiers](https://www.w3.org/TR/did-1.1/) (DIDs) are a new type of identifier that
enables verifiable, decentralized digital identity. A DID refers to any subject (e.g., a person,
organization, thing, data model, abstract entity, etc.) as determined by the controller of the DID.

Each DID method specifies how to create/resolve/update/deactivate DIDs within a given verifiable data
registry. How exactly this works can be very different depending on the DID method and may involve
various steps, architectural components, and network communication. The process of resolving a DID
to a DID document by executing the read() operation is well-understood and specified in the
[DID Resolution](https://www.w3.org/TR/did-resolution/) specification.

This document extends the process of DID Resolution by defining
[DID Parameters](https://www.w3.org/TR/did-resolution/#did-parameters),
[DID Document Metadata properties](https://www.w3.org/TR/did-resolution/#did-document-metadata)
and [DID Resolution Metadata properties](https://www.w3.org/TR/did-resolution/#did-resolution-metadata)
related to security and trust aspects of DIDs and DID Resolution.

This work is related to and references various other specifications and documents, 

## Status of This Document

DID Security and Trust Profile is a draft specification under development, and designed to incorporate the
requirements and learnings from related work of the most active industry players
into a shared specification that meets the collective needs of the community.
This spec is regularly updated to reflect relevant changes, and we encourage
active engagement on GitHub (see above) and other mediums (e.g. DIF).

This document may be contributed to a formal standardization body, such as W3C or CEN TC 224.

## Terminology

Term | Definition
:--- | :---------
Decentralized Identifier (DID) | A globally unique persistent identifier that does not require a centralized registration authority and is often generated and/or registered cryptographically.
DID Method | A definition of how a specific DID scheme implementeds the precise operations by which DIDs are created, resolved and deactivated and DID documents are written and updated.
DID Document | A set of data describing the DID subject, service and verification methods, that the DID subject or a DID delegate can use to authenticate itself and prove its association with the DID.
DID Resolver | A software and/or hardware component that implements the DID resolution function.

## DID Document Validity

This section defines extension [DID Parameters](https://www.w3.org/TR/did-resolution/#did-parameters)
and [DID Resolution Metadata properties](https://www.w3.org/TR/did-resolution/#did-resolution-metadata)
related to validity of the DID document.

### DID Parameter: `checkValidDidDocument`

This DID Parameter is used to check if a resolved DID document is valid, i.e. conformant with the [DID](https://www.w3.org/TR/did-1.1/) data model. Possible values are `ignore`, `warn`, `error`.

The error code `INVALID_DID_DOCUMENT` is returned in case of an error.

#### Example Request

```
https://<did-resolver>/identifiers/<did>?checkValidDidDocument=error"
```

#### Example Response

```json
{
  "didDocument": null,
  "didResolutionMetadata": {
    "error": {
      "type": "INVALID_DID_DOCUMENT",
      "title": "Invalid DID document.",
      "detail": "Error INVALID_DID_DOCUMENT from resolver: Invalid DID document",
      "validationProblems": [{
        "value": "id",
        "error": "there must be one 'id' in 'service'"
      }]
    }
  },
  "didDocumentMetadata": {}
}
```

## DID Document Policies

This section defines extension [DID Parameters](https://www.w3.org/TR/did-resolution/#did-parameters)
and [DID Resolution Metadata properties](https://www.w3.org/TR/did-resolution/#did-resolution-metadata)
related to policies about the DID and DID document, using allow-lists and deny-lists.

### DID Parameter: `checkMethod`

This DID Parameter is used to check if the DID method is not allowed, based on an allow-list and deny-list. Possible values are `ignore`, `warn`, `error`.

The error code `NOT_ALLOWED_METHOD` is returned in case of an error.

#### Example Response

```json
{
  "didDocument": null,
  "didResolutionMetadata": {
    "error": {
      "type": "NOT_ALLOWED_METHOD",
      "title": "This DID method is not allowed by policy.",
      "detail": "Not allowed method: example"
    }
  },
  "didDocumentMetadata": {}
}
```

### DID Parameter: `checkKeyType`

This DID Parameter is used to check if the DID document contains a key type that is not allowed, based on an allow-list and deny-list. Possible values are `ignore`, `warn`, `error`.

The error code `NOT_ALLOWED_KEY_TYPE` is returned in case of an error.

#### Example Response

```json
{
  "didDocument": null,
  "didResolutionMetadata": {
    "error": {
      "type": "NOT_ALLOWED_KEY_TYPE",
      "title": "This key type is not allowed by policy.",
      "detail": "Not allowed key type: Ed25519"
    }
  },
  "didDocumentMetadata": {}
}
```

### DID Parameter: `checkVerificationMethodType`

This DID Parameter is used to check if the DID document contains a verification method with a type that is not allowed, based on an allow-list and deny-list. Possible values are `ignore`, `warn`, `error`.

The error code `NOT_ALLOWED_VERIFICATION_METHOD_TYPE` is returned in case of an error.

#### Example Response

```json
{
  "didDocument": null,
  "didResolutionMetadata": {
    "error": {
      "type": "NOT_ALLOWED_VERIFICATION_METHOD_TYPE",
      "title": "This verification method type is not allowed by policy.",
      "detail": "Not allowed verification method type: JsonWebKey2020"
    }
  },
  "didDocumentMetadata": {}
}
```

## DID Document Key Security

This section defines extension [DID Parameters](https://www.w3.org/TR/did-resolution/#did-parameters)
and [DID Resolution Metadata properties](https://www.w3.org/TR/did-resolution/#did-resolution-metadata)
related to security aspects of the cryptographic keys found in the DID document.

### DID Parameter: `checkLocalDerivedKey`

This DID Parameter is used to check if derived keys have been detected in the DID document, e.g. an X25519 key derived from an Ed25519 key. Possible values are `ignore`, `warn`, `error`.

The error code `NOT_ALLOWED_LOCAL_DERIVED_KEY` is returned in case of an error.

#### Example Request

```
https://<did-resolver>/identifiers/<did>?checkLocalDerivedKey=error"
```

#### Example Response

```json
{
  "didDocument": null,
  "didResolutionMetadata": {
    "error": {
      "type": "NOT_ALLOWED_LOCAL_DERIVED_KEY",
      "title": "Derived keys are not allowed by policy.",
      "detail": "Not allowed derived local verification methods: <did>#<key-1>, <did>#<key-2>",
      "derivedVerificationMethodId": "<did>#<key-1>",
      "derivingVerificationMethodId": "<did>#<key-2>"
    }
  },
  "didDocumentMetadata": {}
}
```

### DID Parameter: `checkLocalDuplicateKey`

This DID Parameter is used to check if duplicate keys have been detected in the DID document. Possible values are `ignore`, `warn`, `error`.

The error code `NOT_ALLOWED_LOCAL_DUPLICATE_KEY` is returned in case of an error.

#### Example Request

```
https://<did-resolver>/identifiers/<did>?checkLocalDuplicateKey=error"
```

#### Example Response

```json
{
  "didDocument": null,
  "didResolutionMetadata": {
    "error": {
      "type": "NOT_ALLOWED_LOCAL_DUPLICATE_KEY",
      "title": "Duplicate keys are not allowed by policy.",
      "detail": "Not allowed duplicate local verification methods: <did>#<key-1>, <did>#<key-2>",
      "duplicateLocalVerificationMethods": [
        "<did>#<key-1>",
        "<did>#<key-2>"
      ]
    }
  },
  "didDocumentMetadata": {}
}
```

### DID Parameter: `checkGlobalDuplicateKey`

This DID Parameter is used to check if duplicate keys have been detected between the DID document and other globally known DID documents. Possible values are `ignore`, `warn`, `error`.

The error code `NOT_ALLOWED_GLOBAL_DUPLICATE_KEY` is returned in case of an error.

#### Example Response

```json
{
  "didDocument": null,
  "didResolutionMetadata": {
    "error": {
      "type": "NOT_ALLOWED_GLOBAL_DUPLICATE_KEY",
      "title": "Duplicate keys are not allowed by policy.",
      "detail": "Not allowed duplicate global verification methods: <did-1>#<key>, <did-2>#<key>",
      "duplicateGlobalVerificationMethods": [
        "<did-1>#<key>",
        "<did-2>#<key>"
      ]
    }
  },
  "didDocumentMetadata": {}
}
```

## High-Assurance Identifier Binding Verification

The [High Assurance DIDs with DNS](https://github.com/CIRALabs/high-assurance-dids-with-dns) specification
defines mechanisms for bridging DIDs with other identifier and trust
domains such as X.509 certificates and Domain Name System (DNS) records. It accomplished this with
cryptographic bridges (using control of public/private key pairs) and non-cryptographic bridges (using
references).

This section defines extension [DID Parameters](https://www.w3.org/TR/did-resolution/#did-parameters)
and [DID Resolution Metadata properties](https://www.w3.org/TR/did-resolution/#did-resolution-metadata)
that enable a DID Resolver to verify the bridge(s) between the DID that is being resolved, and other
identifier and trust domains.

### DID Parameter: `verifyDnsBridge`

This DID Parameter is used to check if a DID document contains a bridge to a domain name that cannot be verified via
DNS, according to the [High Assurance DIDs with DNS](https://github.com/CIRALabs/high-assurance-dids-with-dns) specification, based on an allow-list and deny-list.
Possible values are `ignore`, `warn`, `error`.

The error code `UNVERIFIED_DNS_BRIDGE` is returned in case of an error.

#### Example Request

```
https://<did-resolver>/identifiers/did:web:danubetech.com:did:test7?verifyDnsBridge=error"
```

#### Example DID Document

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/ed25519-2018/v1"
  ],
  "id": "did:web:danubetech.com:did:test7",
  "verificationMethod": [
    {
      "id": "did:web:danubetech.com:did:test7#key-1",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:web:danubetech.com:did:test7",
      "publicKeyBase58": "EjaHrfMn3TGVBZAX3Mi1cfStuEhCLYVMNjbdE3ZQwX24"
    }
  ],
  "assertionMethod": [
    "did:web:danubetech.com:did:test7#key-1"
  ],
  "authentication": [
    "did:web:danubetech.com:did:test7#key-1"
  ],
  "dnsValidationDomain": "rdapregistrygroup1.trustregistry.nborbit.ca"
}
```

#### Example Response

```json
{
    "didDocument": null,
    "didResolutionMetadata": {
      "error": {
        "type": "UNVERIFIED_DNS_BRIDGE",
        "title": "The DID/DNS bridge could not be verified.",
        "detail": "Error UNVERIFIED_DNS_BRIDGE from resolver: DID did:web:danubetech.com:did:test7 not found in DNS URI records for domain name rdapregistrygroup1.trustregistry.nborbit.ca.",
        "domainName": "rdapregistrygroup1.trustregistry.nborbit.ca",
        "uriRecordTargets": [
          "did:web:rdapregistrygroup1.trustregistry.nborbit.ca"
        ]
      }
    },
    "didDocumentMetadata": {}
}
```

### DID Parameter: `verifyCertificateBridge`

This DID Parameter is used to check if a DID document contains a key that cannot be traced back to a trusted
certificate authority, according to the [High Assurance DIDs with DNS](https://github.com/CIRALabs/high-assurance-dids-with-dns) specification, based on an allow-list
and deny-list. Possible values are `ignore`, `warn`, `error`.

The error code `UNVERIFIED_CERTIFICATE_BRIDGE` is returned in case of an error.

#### Example Request

```
https://<did-resolver>/identifiers/did:web:danubetech.com:did:test4?verifyCertificateBridge=error"
```

#### Example DID Document

```json
{
    "@context": [
        "https://www.w3.org/ns/did/v1",
        "https://w3id.org/security/suites/jws-2020/v1",
        {
            "x509CertificateChain": "https://w3id.org/security#x509CertificateChain"
        }
    ],
    "id": "did:web:danubetech.com:did:test4",
    "verificationMethod": [
        {
            "id": "did:web:danubetech.com:did:test4#key-1",
            "type": "EcdsaSecp384r1VerificationKey2019",
            "controller": "did:web:danubetech.com:did:test4",
            "publicKeyMultibase": "z7B4TwhJKG6mnuybT2M15rSSRMARfMKp452YYmRph2qS7CdWei3SfF9huhSnqnoxYgoEANMPog4Dkod5oo6grXAU6hMPggKSWP5WDLWHK6bvbboh2M54j4XEUvUCCXGEh95rQ",
            "x509CertificateChain": [
                "MIIBuzCCAUCgAwIBAgICEAEwCgYIKoZIzj0EAwIwGjEYMBYGA1UEAwwPSW50ZXJt\nZWRpYXRlLUNBMB4XDTI0MTAyNjE5NTg0M1oXDTI1MTAyNjE5NTg0M1owMTEvMC0G\nA1UEAwwmZGlkOndlYjpkYW51YmV0ZWNoLmNvbTpkaWQ6dGVzdDQja2V5LTEwdjAQ\nBgcqhkjOPQIBBgUrgQQAIgNiAAQPb7s7JTrvooZ3X8AYcRZDpVxrsWeVgq63KTvd\nJYz7C/T6kpAjOnaCLfLv/bJSkINXTpSI3T9BZ6UapfNh891zzxQelZL6OolI+CPU\nK3TXGCI2eCzHTqcrsCs1e5g7mmGjQjBAMB0GA1UdDgQWBBTBXZ7pDVp09aqYOyqJ\nKdTtRtpajDAfBgNVHSMEGDAWgBQZ1BR4a4uQDKsqcs7ZLPuZDeNYqDAKBggqhkjO\nPQQDAgNpADBmAjEAwjaf5uOitXSd2Jk+TIxuePHXVIcOPDvlIhP0LMNbY2mumDZT\nAAYI4QKpyd/aiWSZAjEAtAn8HLEvTU+B7Fu/zzphqe1QVEkSLbnTW/HDgZRUBOui\nQEF38w33IZaG5SkpJiQz\n",
                "MIIBqDCCAS+gAwIBAgICEAAwCgYIKoZIzj0EAwIwEjEQMA4GA1UEAwwHUm9vdC1D\nQTAeFw0yNDEwMjYxOTU4MzlaFw0yNTEwMjYxOTU4MzlaMBoxGDAWBgNVBAMMD0lu\ndGVybWVkaWF0ZS1DQTB2MBAGByqGSM49AgEGBSuBBAAiA2IABFtPSi8sEDi/Y4R2\n90JHC5IX/JYwnJZ3sDP5OtOS+PUQDwyMO9Ge0I6//JSDORqxV/EfKSkGSILs1OtW\nnRuJyy+PLwMMhR3OfjZ+WzWRNY1Na5pVZUwGQqfYCjf9iOTTo6NQME4wDAYDVR0T\nBAUwAwEB/zAdBgNVHQ4EFgQUGdQUeGuLkAyrKnLO2Sz7mQ3jWKgwHwYDVR0jBBgw\nFoAUqIddikSUeLEqHBgtUfwGsj2kzz8wCgYIKoZIzj0EAwIDZwAwZAIwEZSRdzSC\nbYtmA1cV1y4CaLjpIDT8RgD1IbhHsvgKud87wc/FkMv0AUSnHfmAntPgAjAc7GGO\n2GV1wm6U2uckCKYtY/KzWYJ0W7xdW64b4DvvREbgixD5B2gv+VFV3XEh6SQ=\n",
                "MIIBkjCCARigAwIBAgIUbtyxzwCCX8ncASrGWZkaW1VEZGgwCgYIKoZIzj0EAwIw\nEjEQMA4GA1UEAwwHUm9vdC1DQTAeFw0yNDEwMjYxOTU4MzZaFw0zNDEwMjQxOTU4\nMzZaMBIxEDAOBgNVBAMMB1Jvb3QtQ0EwdjAQBgcqhkjOPQIBBgUrgQQAIgNiAAT3\nxAcQwZqqtqCBQhRHfBHE8iZsu7GWvsNltWa0ZZu3mvTHeet8IaOH40tTjGMeKLrL\nd8Yhskk7t17gsT1FHDdA3AoDqfgOsyFM1OzDCSlAgVAWjU0p8RP/FvpattQauPSj\nLzAtMAwGA1UdEwQFMAMBAf8wHQYDVR0OBBYEFKiHXYpElHixKhwYLVH8BrI9pM8/\nMAoGCCqGSM49BAMCA2gAMGUCMQDJ6/2jcUw2lWdmiBLjltVv83kLGky6YpPwJT47\nbSrOEueUs+L36xaOs4dtPwdvD1ECMDs6qBv73EZ6vDJ0PVMeLfbBRek2MNDO7U1I\nRpeRBU/sV9vWw4qr2APlYOYcObhyng==\n"
            ]
        }
    ],
    "assertionMethod": [
        "did:web:danubetech.com:did:test4#key-1"
    ],
    "authentication": [
        "did:web:danubetech.com:did:test4#key-1"
    ]
}
```

#### Example Response

```json
{
  "didDocument": null,
  "didResolutionMetadata": {
    "error": {
      "type": "UNVERIFIED_CERTIFICATE_BRIDGE",
      "title": "The DID/Certificate bridge could not be verified.",
      "detail": "Error UNVERIFIED_CERTIFICATE_BRIDGE from resolver: Verification method's public key does not match certificate",
      "certificate": "4097",
      "verificationMethodId": "did:web:danubetech.com:did:test4#key-1"
    }
  },
  "didDocumentMetadata": {}
}
```

## Trust Registry Lookups

Trust registry infrastructures such as [[spec:TRAIN]], [[spec:EBSI]] [[spec:TRQP]] define mechanisms for obtaining
information about trust relationships, associated with DIDs and other identifiers, for example to answer the question
“Is the entity identified by DID <X> authorized to issue/verify a credential of type <Y>?”

This section defines extension [DID Document Metadata properties](https://www.w3.org/TR/did-resolution/#did-metadata-metadata)
that enable a DID Resolver to query trust registry infrastructures like the above, and to return information about
trust relationships as part of the [DID Resolution Result](https://www.w3.org/TR/did-resolution/#did-resolution-result).

### TRQP

For the [Trust Registry Query Protocol](https://trustoverip.github.io/tswg-trust-registry-protocol/) (TRQP), this section
defines the following [DID Parameters](https://www.w3.org/TR/did-resolution/#did-metadata-metadata) and
[DID Resolution Metadata properties](https://www.w3.org/TR/did-resolution/#did-resolution-metadata):

DID Parameters:

- `trqpEcosystemid`: An ecosystem identifier as defined by TRQP.
- `trqpAuthorizationId`: One of the defined authorization types defined by TRQP.
- `trqpTime`: A timestamp in RFC3339 UTC format indicating when to evaluate the TRQP query.

DID Resolution Metadata properties:

- `trqpAuthorizationStatus`: One of the authorization status codes defined by TRQP. Possible values are: `authorized`, `not-authorized`, `revoked`, `unknown-subject`, `error`.

#### Example Request

```
https://<did-resolver>/identifiers/did:web:danubetech.com?trqpEcosystemid=ecosystemA&trqpAuthorizationId=DriversLicenseIssuer"
```

#### Example Response

```json
{
    "didDocument": {
        "@context": [
            "https://www.w3.org/ns/did/v1",
            "https://w3id.org/security/suites/jws-2020/v1"
        ],
        "id": "did:web:danubetech.com",
        "service": [
            {
                "id": "did:web:danubetech.com#godiddy-docs-apis",
                "serviceEndpoint": "https://docs.godiddy.com/en/apis/"
            },
            {
                "id": "did:web:danubetech.com#github",
                "serviceEndpoint": "https://github.com/danubetech/"
            }
        ],
        "verificationMethod": [
            {
                "id": "did:web:danubetech.com#key-1",
                "type": "JsonWebKey2020",
                "controller": "did:web:danubetech.com",
                "publicKeyJwk": {
                    "kty": "EC",
                    "crv": "P-384",
                    "x": "68CilmUEdVWmxJrFOKdTNYaMPJayWkoTGP03qe_WErehZPu4XKhw2qxgpNZ2vBG7",
                    "y": "4IQgfsVSw0OrYprQNJYCHC3RYGtgC87rQtQrMNVZAricEDw78F7Qq9uJ3wMXj5LG"
                }
            }
        ],
        "assertionMethod": [
            "did:web:danubetech.com#key-1"
        ],
        "authentication": [
            "did:web:danubetech.com#key-1"
        ]
    },
    "didResolutionMetadata": {
        "trqpAuthorizationStatus": "not-authorized"
    },
    "didDocumentMetadata": { }
}
```

### TRAIN

For [TRAIN](https://www.hci.iao.fraunhofer.de/de/identity-management/identity-und-accessmanagement/TRAIN.html) (TRQP), this section
defines the following [DID Parameters](https://www.w3.org/TR/did-resolution/#did-metadata-metadata) and
[DID Resolution Metadata properties](https://www.w3.org/TR/did-resolution/#did-resolution-metadata):

DID Parameters:

- `trainTrustSchemePointers`

DID Resolution Metadata properties:

- `trainVerificationStatus`: Result of the TRAIN trust verification process. Possible values are: `OK`, `FAILED`.
- `trainVerificationResult`: Additional properties about the TRAIN trust verification process.

#### Example Request

```
https://<did-resolver>/identifiers/did:cheqd:testnet:975f1941-9313-41d4-ac8b-88fedda7ce80?trainTrustSchemePointers=example.federation1.de"
```

#### Example Response

```json
{
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/suites/ed25519-2018/v1"
    ],
    "id": "did:cheqd:testnet:975f1941-9313-41d4-ac8b-88fedda7ce80",
    "controller": [
      "did:cheqd:testnet:975f1941-9313-41d4-ac8b-88fedda7ce80"
    ],
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:975f1941-9313-41d4-ac8b-88fedda7ce80#key-1",
        "type": "Ed25519VerificationKey2018",
        "controller": "did:cheqd:testnet:975f1941-9313-41d4-ac8b-88fedda7ce80",
        "publicKeyBase58": "4WKYEuGoVAsGPwWWwK95fKH3CBb9RWnc2fDw6Ea1vzA2"
      }
    ],
    "authentication": [
      "did:cheqd:testnet:975f1941-9313-41d4-ac8b-88fedda7ce80#key-1"
    ]
  },
  "didResolutionMetadata": {
    "trainVerificationStatus": "OK",
    "trainVerificationResult": {
      "FindingCorrespondingTrustSchemeInitiated": "OK",
      "TrustListDiscoveryInitiated": "OK",
      "FoundIssuer": "string",
      "VerifyIssuer": "OK",
      "VerificationSuccessful": "OK"
    }
  },
  "didDocumentMetadata": {}
}
```

### EBSI

For the [European Blockchain Service Infrastructure](https://hub.ebsi.eu/vc-framework/trust-model) (EBSI), this section
defines the following [DID Document Metadata properties](https://www.w3.org/TR/did-resolution/#did-metadata-metadata):

DID Document Metadata properties:

- `issuerType`: Possible values: `TI`, `TAO`, `RootTAO`
- `trustChain`:
  - `ti`: The Trusted Issuer (TI) associated with this DID
  - `tao`: The Trusted Accreditation Organization (TAO) associated with this DID
  - `rootTao`: The Root Trusted Accreditation Organization (RootTAO) associated with this DID

#### Example Request

```
https://<did-resolver>/identifiers/did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3"
```

#### Example Response

```json
{
    "didDocument": {
        "@context": [
            "https://www.w3.org/ns/did/v1",
            "https://w3id.org/security/suites/jws-2020/v1"
        ],
        "id": "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3",
        "verificationMethod": [
            {
                "id": "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3#lEvEATg9BM78yHKXsyBG_rNdJ2yrKJka9pPK-DTmfqc",
                "type": "JsonWebKey2020",
                "controller": "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3",
                "publicKeyJwk": {
                    "crv": "secp256k1",
                    "kty": "EC",
                    "x": "abpQQdrnCMfASIuTdaskiry0KFM1RhpyM9611ca9pHU",
                    "y": "L72Ae7G4WvbzdNY3zVAc_LbstkJkdJgRuLbUxVctWPw"
                }
            },
            {
                "id": "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3#uSpMieUU_GFvNxS86zajPf0sgRHLl8wjxXc4NKxy8hU",
                "type": "JsonWebKey2020",
                "controller": "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3",
                "publicKeyJwk": {
                    "crv": "P-256",
                    "kty": "EC",
                    "x": "Ww8QJYP7ObsbL3Fy2Cbvikj9Zrq0E1_xnjUP5mIMu3Y",
                    "y": "un_3NeABs9kyY1vARA-n4eY794vblNGey-NZuv18UsA"
                }
            }
        ],
        "authentication": [
            "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3#lEvEATg9BM78yHKXsyBG_rNdJ2yrKJka9pPK-DTmfqc",
            "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3#uSpMieUU_GFvNxS86zajPf0sgRHLl8wjxXc4NKxy8hU"
        ],
        "assertionMethod": [
            "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3#uSpMieUU_GFvNxS86zajPf0sgRHLl8wjxXc4NKxy8hU"
        ],
        "capabilityInvocation": [
            "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3#lEvEATg9BM78yHKXsyBG_rNdJ2yrKJka9pPK-DTmfqc"
        ]
    },
    "didResolutionMetadata": { },
    "didDocumentMetadata": {
        "issuerType": "TAO",
        "trustChain": {
            "ti": null,
            "tao": {
                "did": "did:ebsi:zxE9ucTwx5V7Aean5Kj6Lz3",
                "attribute": {
                    "hash": "2df9d159399040387fa9f0c50bcf1e67fbe307e950963004f10d72a5d39f0a64",
                    "body": "eyJhbGciOiJFUzI1NiIsImtpZCI6ImRpZDplYnNpOnpqVW5FeHN5eXdlUTlwNGN5M252clZjI2l5Z1VZZURYM0ZvSklSTzdzWmI5bXN2enRxVmZJVU5rZFpwWlA1MmoxLUkiLCJ0eXAiOiJKV1QifQ.eyJleHAiOjE3MzM2NDQ3NTMsImlhdCI6MTczMjc4MDc1MywiaXNzIjoiZGlkOmVic2k6empVbkV4c3l5d2VROXA0Y3kzbnZyVmMiLCJqdGkiOiJ1cm46dXVpZDo3YWRiMTU5Yi1lMmUyLTQwNTEtODJkYi1hMGI4NTU1ZWYyYTgiLCJuYmYiOjE3MzI3ODA3NTMsInN1YiI6ImRpZDplYnNpOnp4RTl1Y1R3eDVWN0FlYW41S2o2THozIiwidmMiOnsiQGNvbnRleHQiOlsiaHR0cHM6Ly93d3cudzMub3JnLzIwMTgvY3JlZGVudGlhbHMvdjEiXSwiY3JlZGVudGlhbFNjaGVtYSI6eyJpZCI6Imh0dHBzOi8vYXBpLXBpbG90LmVic2kuZXUvdHJ1c3RlZC1zY2hlbWFzLXJlZ2lzdHJ5L3YzL3NjaGVtYXMvejNNZ1VGVWtiNzIydXE0eDNkdjV5QUptbk5tekRGZUs1VUM4eDgzUW9lTEpNIiwidHlwZSI6IkZ1bGxKc29uU2NoZW1hVmFsaWRhdG9yMjAyMSJ9LCJjcmVkZW50aWFsU3ViamVjdCI6eyJhY2NyZWRpdGVkRm9yIjpbeyJsaW1pdEp1cmlzZGljdGlvbiI6Imh0dHBzOi8vcHVibGljYXRpb25zLmV1cm9wYS5ldS9yZXNvdXJjZS9hdXRob3JpdHkvYXR1L0ZJTiIsInNjaGVtYUlkIjoiaHR0cHM6Ly9hcGktcGlsb3QuZWJzaS5ldS90cnVzdGVkLXNjaGVtYXMtcmVnaXN0cnkvdjMvc2NoZW1hcy96M01nVUZVa2I3MjJ1cTR4M2R2NXlBSm1uTm16REZlSzVVQzh4ODNRb2VMSk0iLCJ0eXBlcyI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlZlcmlmaWFibGVBdHRlc3RhdGlvbiIsIkNUUmV2b2NhYmxlIl19XSwiaWQiOiJkaWQ6ZWJzaTp6eEU5dWNUd3g1VjdBZWFuNUtqNkx6MyIsInJlc2VydmVkQXR0cmlidXRlSWQiOiJjY2EzY2E5NDA2YjQ1YThmN2E5NDUzZGM0OGU5OWFlZDBmNzJkZTcwNDc1ZTllN2UxMDdkYjg1YjczZTc0ZmNiIn0sImV4cGlyYXRpb25EYXRlIjoiMjAyNC0xMi0wOFQwNzo1OToxMy4wMDBaIiwiaWQiOiJ1cm46dXVpZDo3YWRiMTU5Yi1lMmUyLTQwNTEtODJkYi1hMGI4NTU1ZWYyYTgiLCJpc3N1YW5jZURhdGUiOiIyMDI0LTExLTI4VDA3OjU5OjEzLjAwMFoiLCJpc3N1ZWQiOiIyMDI0LTExLTI4VDA3OjU5OjEzLjAwMFoiLCJpc3N1ZXIiOiJkaWQ6ZWJzaTp6alVuRXhzeXl3ZVE5cDRjeTNudnJWYyIsInRlcm1zT2ZVc2UiOnsiaWQiOiJodHRwczovL2FwaS1waWxvdC5lYnNpLmV1L3RydXN0ZWQtaXNzdWVycy1yZWdpc3RyeS92NS9pc3N1ZXJzL2RpZDplYnNpOnpqVW5FeHN5eXdlUTlwNGN5M252clZjL2F0dHJpYnV0ZXMvOWFiNDM0Y2FmYjNiMzMyNzk0MWY5NDIyZTg4ZWZkOTNjNmNmZjlkZGE2NzhmOGFhNDdmZGMyMzhmOGJlYzNjZSIsInR5cGUiOiJJc3N1YW5jZUNlcnRpZmljYXRlIn0sInR5cGUiOlsiVmVyaWZpYWJsZUNyZWRlbnRpYWwiLCJWZXJpZmlhYmxlQXR0ZXN0YXRpb24iLCJWZXJpZmlhYmxlQWNjcmVkaXRhdGlvblRvQWNjcmVkaXQiXSwidmFsaWRGcm9tIjoiMjAyNC0xMS0yOFQwNzo1OToxMy4wMDBaIn19.X5ezyPZEsbmy5wU_-RFcov7mfVfWUaSHhHopplJpiIhMRSionvge7gVwT-N37qPDvpZUIzd42iSHYrTulBI6rA",
                    "issuerType": "TAO",
                    "tao": "did:ebsi:zjUnExsyyweQ9p4cy3nvrVc",
                    "rootTao": "did:ebsi:zjUnExsyyweQ9p4cy3nvrVc"
                },
                "accreditedFor": [
                    {
                        "schemaId": "https://api-pilot.ebsi.eu/trusted-schemas-registry/v3/schemas/z3MgUFUkb722uq4x3dv5yAJmnNmzDFeK5UC8x83QoeLJM",
                        "types": [
                            "VerifiableCredential",
                            "VerifiableAttestation",
                            "CTRevocable"
                        ],
                        "limitJurisdiction": "https://publications.europa.eu/resource/authority/atu/FIN"
                    }
                ]
            },
            "rootTao": {
                "did": "did:ebsi:zjUnExsyyweQ9p4cy3nvrVc",
                "attribute": {
                    "hash": "831cb86b4c05f3a8930d83f3329126c397f4a28da110ef394ff9a1ca948474f2",
                    "body": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6ImRpZDplYnNpOnpaZUt5RUpmVVRHd2FqaE55Tlg5Mjh6I1NvMUhJUnQwS3FmMF9CU3dxdjRWTU5na2F4M21DRjlJamNRSndNbkIzYnMifQ.eyJpYXQiOjE3MzAyOTUxMDAsImp0aSI6InVybjp1dWlkOjIxYjhjZTJjLThiYTgtNGJhZC05OGQwLTNmMjRkNTMyZTc1MiIsIm5iZiI6MTczMDI5NTEwMCwiZXhwIjoxODg3OTc1MTAwLCJzdWIiOiJkaWQ6ZWJzaTp6alVuRXhzeXl3ZVE5cDRjeTNudnJWYyIsInZjIjp7IkBjb250ZXh0IjpbImh0dHBzOi8vd3d3LnczLm9yZy8yMDE4L2NyZWRlbnRpYWxzL3YxIl0sImlkIjoidXJuOnV1aWQ6MjFiOGNlMmMtOGJhOC00YmFkLTk4ZDAtM2YyNGQ1MzJlNzUyIiwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlZlcmlmaWFibGVBdHRlc3RhdGlvbiIsIlZlcmlmaWFibGVBdXRob3Jpc2F0aW9uRm9yVHJ1c3RDaGFpbiJdLCJpc3N1ZXIiOiJkaWQ6ZWJzaTp6WmVLeUVKZlVUR3dhamhOeU5YOTI4eiIsImlzc3VhbmNlRGF0ZSI6IjIwMjQtMTAtMzBUMTM6MzE6NDBaIiwiaXNzdWVkIjoiMjAyNC0xMC0zMFQxMzozMTo0MFoiLCJ2YWxpZEZyb20iOiIyMDI0LTEwLTMwVDEzOjMxOjQwWiIsImV4cGlyYXRpb25EYXRlIjoiMjAyOS0xMC0yOVQxMzozMTo0MFoiLCJjcmVkZW50aWFsU3ViamVjdCI6eyJpZCI6ImRpZDplYnNpOnpqVW5FeHN5eXdlUTlwNGN5M252clZjIiwiYWNjcmVkaXRlZEZvciI6W3sic2NoZW1hSWQiOiJodHRwczovL2FwaS1waWxvdC5lYnNpLmV1L3RydXN0ZWQtc2NoZW1hcy1yZWdpc3RyeS92My9zY2hlbWFzL3ozTWdVRlVrYjcyMnVxNHgzZHY1eUFKbW5ObXpERmVLNVVDOHg4M1FvZUxKTSIsInR5cGVzIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiVmVyaWZpYWJsZUF0dGVzdGF0aW9uIiwiQ1RSZXZvY2FibGUiXSwibGltaXRKdXJpc2RpY3Rpb24iOiJodHRwczovL3B1YmxpY2F0aW9ucy5ldXJvcGEuZXUvcmVzb3VyY2UvYXV0aG9yaXR5L2F0dS9FVVIifV0sInJlc2VydmVkQXR0cmlidXRlSWQiOiI5YWI0MzRjYWZiM2IzMzI3OTQxZjk0MjJlODhlZmQ5M2M2Y2ZmOWRkYTY3OGY4YWE0N2ZkYzIzOGY4YmVjM2NlIn0sInRlcm1zT2ZVc2UiOnsiaWQiOiJodHRwczovL2FwaS1waWxvdC5lYnNpLmV1L3RydXN0ZWQtaXNzdWVycy1yZWdpc3RyeS92NS9pc3N1ZXJzL2RpZDplYnNpOnpaZUt5RUpmVVRHd2FqaE55Tlg5Mjh6L2F0dHJpYnV0ZXMvNDQ3ODY3YmFmYWI0YzRhZDI2ZDRmNDY1MGUyMzkwZjkyYTgzMWI2MzQzYjdjMzZlY2I3YzUxZmVlMzM2ZmExNCIsInR5cGUiOiJJc3N1YW5jZUNlcnRpZmljYXRlIn0sImNyZWRlbnRpYWxTY2hlbWEiOnsiaWQiOiJodHRwczovL2FwaS1waWxvdC5lYnNpLmV1L3RydXN0ZWQtc2NoZW1hcy1yZWdpc3RyeS92My9zY2hlbWFzL3ozTWdVRlVrYjcyMnVxNHgzZHY1eUFKbW5ObXpERmVLNVVDOHg4M1FvZUxKTSIsInR5cGUiOiJGdWxsSnNvblNjaGVtYVZhbGlkYXRvcjIwMjEifX0sImlzcyI6ImRpZDplYnNpOnpaZUt5RUpmVVRHd2FqaE55Tlg5Mjh6In0.lO8em9KUPLh3ehzI98eOZyzUjPgL-fGYs5-4A1YRf-k1oc40OPzNnu7tUy7gR4tbuHYP_qmj5SzqwpCWBlQwvw",
                    "issuerType": "RootTAO",
                    "tao": "did:ebsi:zjUnExsyyweQ9p4cy3nvrVc",
                    "rootTao": "did:ebsi:zjUnExsyyweQ9p4cy3nvrVc"
                },
                "accreditedFor": [
                    {
                        "schemaId": "https://api-pilot.ebsi.eu/trusted-schemas-registry/v3/schemas/z3MgUFUkb722uq4x3dv5yAJmnNmzDFeK5UC8x83QoeLJM",
                        "types": [
                            "VerifiableCredential",
                            "VerifiableAttestation",
                            "CTRevocable"
                        ],
                        "limitJurisdiction": "https://publications.europa.eu/resource/authority/atu/EUR"
                    }
                ]
            }
        }
    }
}
```

## DID Resolution Interceptors

This section defines an architecture that allows a DID Resolver to invoke an external service called a 
DID Resolution Interceptor (via a webhook-like endpoint), which can effectively "intercept" a DID Resolution
operation, for purposes of logging, caching, analysis, policy enforcement, augmentation of the DID Resolution
Result, or similar purposes. The DID Resolution Interceptor can also "override" the DID Resolution Result, or
return an error result.

![Diagram showing DID Resolution Interception](images/diagram-interceptor.png)

At the end of the DID Resolution process, after the DID Resolution Result has been produced, the DID Resolver calls
the endpoint of a DID Resolution Interceptor with the following inputs and outputs:

Inputs:
- `did`: The DID that is being resolved
- `didResolutionOptions`: The DID Resolution Options that have been passed by the Client to the DID Resolver
- `didResolutionResult`: The DID Resolution Result produced by the DID Resolver

Outputs:
- `didResolutionResult`: The DID Resolution Result augmented by the DID Resolution Interceptor

## References

[[spec]]

## Acknowledgements

<img align="left" src="images/logo-cyberstand.png" width="115">

Supported by [Cyberstand.eu](https://cyberstand.eu/), which is made possible with financial support from the European Commission.

## Appendix

### Other Resources

The following projects are working on DID resolution across different DID methods.

* [DIF Universal Resolver](https://github.com/decentralized-identity/universal-resolver)
