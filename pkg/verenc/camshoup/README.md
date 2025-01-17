<!-- Code generated by gomarkdoc. DO NOT EDIT -->

# camshoup

```go
import "github.com/coinbase/kryptology/pkg/verenc/camshoup"
```

### Verifiable encryption

Verifiable encryption addresses a problem about proving statements for encrypted data\. Verifiable encryption allows a prover to convince a verifier that a given ciphertext is an encryption under a given public key\. A verifiable encryption scheme proves that a ciphertext encrypts a plaintext satisfying a certain relation\. Our implementation is a non\-interactive zero\-knowledge proof \(NIZK\) i\.e\. the prover creates the proof and sends it to the verifier with no further interaction\. A verifiable encryption is transferred to a verifier and possibly to a trusted party for decryption\. It does not need to be stored at the prover’s side as it usually is not repeatedly needed\.

Why? Think of scenarios that allow signers or provers to remain anonymous but where some information about the prover needs to be known and is put into escrow\. The escrow service can be a trusted third party or the verifier themselves\. The prover encrypts an attribute to a specific public key and creates a NIZK proof of knowledge of the attribute for the resulting ciphertext\. To recover the attribute\, the opener simply decrypts the ciphertext\. It is recommended that a trusted escrow service serve this purpose and not the verifier such that only the trusted authority can actually recover the attribute\. The attribute can be anything including a secret key\. Another term for this can be a Verifiable Escrow Service\.

Note that a key escrow scheme cannot prevent parties from double\-encrypting messages under a non\-escrowed key\, or applying steganography to hide the fact that they are communicating altogether\. The goal\, therefore\, is rather to prevent “dishonest” usage of public\-key infrastructures\, e\.g\.\, by using it to certify non\-escrowed keys\.

### How it works

Camenisch\-Shoup Verifiable encryption is similar to RSA and based on Paillier's Decision Composite Residuosity http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.112.4035&rep=rep1&type=pdf\. A group of unknown order is created from two safe primes p\, q where p = 2p'\+1\, q = 2q'\+1 and p'\, q' are also prime and p &ne; q\.

The group parameters are computed as follows:

```
1. Set n = p * q
2. Sample a random g' < n^2
3. Set g = g'^2n
4. Set h = n + 1
```

### Secret keys are computed as follows

```
1. Sample three random values x_1,x_2,x_3 < n^2/4
2. The secret key is {x_1,x_2,x_3}
```

### Public keys are computed as follows

```
1. Compute y_1 = g^x1
2. Compute y_2 = g^x2
3. Compute y_3 = g^x3
4. The public key is {y_1, y_2, y_3}
```

The group parameters can be stored separate or together with each key\.

### Proving

Verifiable encryption not only produces a ciphertext that can be decrypted\, but also yields a proof that the plaintext is encrypted to a specific public key\. The ciphertext is represented as the triplet \{u\, e\, v\}\, the proof is represented as the Schnorr proof challenge c with responses m\, r\.

### References

Practical Verifiable Encryption and Decryption of Discrete Logarithms \- Jan Camenisch and Victor Shoup 2003\. https://eprint.iacr.org/2002/161.pdf

Specification of the Identity Mixer Cryptographic Library \- IBM Zurich 2009\. https://dominoweb.draco.res.ibm.com/reports/rz3730_revised.pdf

Public\-Key Cryptosystems Based on Composite Degree Residuosity Classes \- Pascall Paillier 1999\. http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.112.4035&rep=rep1&type=pdf

## Index

- [func NewKeys(numMsgs uint, group *PaillierGroup) (*EncryptionKey, *DecryptionKey, error)](<#func-newkeys>)
- [type CipherText](<#type-ciphertext>)
  - [func (c CipherText) MarshalBinary() ([]byte, error)](<#func-ciphertext-marshalbinary>)
  - [func (c *CipherText) UnmarshalBinary(data []byte) error](<#func-ciphertext-unmarshalbinary>)
- [type DecryptionKey](<#type-decryptionkey>)
  - [func (dk DecryptionKey) Decrypt(domain []byte, cipherText *CipherText) ([]*big.Int, error)](<#func-decryptionkey-decrypt>)
  - [func (dk DecryptionKey) EncryptionKey() *EncryptionKey](<#func-decryptionkey-encryptionkey>)
  - [func (dk DecryptionKey) MarshalBinary() ([]byte, error)](<#func-decryptionkey-marshalbinary>)
  - [func (dk *DecryptionKey) UnmarshalBinary(data []byte) error](<#func-decryptionkey-unmarshalbinary>)
- [type EncryptionKey](<#type-encryptionkey>)
  - [func (ek EncryptionKey) Encrypt(domain []byte, msgs []*big.Int) (*CipherText, error)](<#func-encryptionkey-encrypt>)
  - [func (ek EncryptionKey) EncryptAndProve(nonce []byte, msgs []*big.Int) (*CipherText, *ProofVerEnc, error)](<#func-encryptionkey-encryptandprove>)
  - [func (ek EncryptionKey) EncryptAndProveBlindings(nonce []byte, msgs []*big.Int, blindings []*big.Int) (*CipherText, *ProofVerEnc, error)](<#func-encryptionkey-encryptandproveblindings>)
  - [func (ek EncryptionKey) MarshalBinary() ([]byte, error)](<#func-encryptionkey-marshalbinary>)
  - [func (ek *EncryptionKey) UnmarshalBinary(data []byte) error](<#func-encryptionkey-unmarshalbinary>)
  - [func (ek EncryptionKey) VerifyEncryptProof(nonce []byte, ciphertext *CipherText, proof *ProofVerEnc) error](<#func-encryptionkey-verifyencryptproof>)
- [type PaillierGroup](<#type-pailliergroup>)
  - [func NewPaillierGroup() (*PaillierGroup, error)](<#func-newpailliergroup>)
  - [func NewPaillierGroupWithPrimes(p, q *big.Int) (*PaillierGroup, error)](<#func-newpailliergroupwithprimes>)
  - [func (pg PaillierGroup) Abs(a *big.Int) *big.Int](<#func-pailliergroup-abs>)
  - [func (pg PaillierGroup) Exp(base, exp *big.Int) *big.Int](<#func-pailliergroup-exp>)
  - [func (pg PaillierGroup) Gexp(exp *big.Int) *big.Int](<#func-pailliergroup-gexp>)
  - [func (pg PaillierGroup) Hash(u *big.Int, e []*big.Int, data []byte) (*big.Int, error)](<#func-pailliergroup-hash>)
  - [func (pg PaillierGroup) Hexp(exp *big.Int) *big.Int](<#func-pailliergroup-hexp>)
  - [func (pg PaillierGroup) Inv(val *big.Int) *big.Int](<#func-pailliergroup-inv>)
  - [func (pg PaillierGroup) MarshalBinary() ([]byte, error)](<#func-pailliergroup-marshalbinary>)
  - [func (pg PaillierGroup) Mul(lhs, rhs *big.Int) *big.Int](<#func-pailliergroup-mul>)
  - [func (pg PaillierGroup) Rand() (*big.Int, error)](<#func-pailliergroup-rand>)
  - [func (pg PaillierGroup) RandForEncrypt() (*big.Int, error)](<#func-pailliergroup-randforencrypt>)
  - [func (pg *PaillierGroup) UnmarshalBinary(data []byte) error](<#func-pailliergroup-unmarshalbinary>)
- [type ProofVerEnc](<#type-proofverenc>)
  - [func (pf ProofVerEnc) MarshalBinary() ([]byte, error)](<#func-proofverenc-marshalbinary>)
  - [func (pf *ProofVerEnc) UnmarshalBinary(data []byte) error](<#func-proofverenc-unmarshalbinary>)


## func [NewKeys](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/encryption_key.go#L34>)

```go
func NewKeys(numMsgs uint, group *PaillierGroup) (*EncryptionKey, *DecryptionKey, error)
```

## type [CipherText](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/ciphertext.go#L17-L20>)

CipherText represents verifiably encrypted ciphertext as described in section 3\.2 in \<https://shoup.net/papers/verenc.pdf\>\.

```go
type CipherText struct {
    // contains filtered or unexported fields
}
```

### func \(CipherText\) [MarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/ciphertext.go#L28>)

```go
func (c CipherText) MarshalBinary() ([]byte, error)
```

### func \(\*CipherText\) [UnmarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/ciphertext.go#L40>)

```go
func (c *CipherText) UnmarshalBinary(data []byte) error
```

## type [DecryptionKey](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/decryption_key.go#L27-L31>)

DecryptionKey decrypts verifiable ciphertext as described in section 3\.2 in \<https://shoup.net/papers/verenc.pdf\>

```go
type DecryptionKey struct {
    // contains filtered or unexported fields
}
```

### func \(DecryptionKey\) [Decrypt](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/decryption_key.go#L85>)

```go
func (dk DecryptionKey) Decrypt(domain []byte, cipherText *CipherText) ([]*big.Int, error)
```

Decrypt as described in section 3\.2 in \<https://shoup.net/papers/verenc.pdf\>

### func \(DecryptionKey\) [EncryptionKey](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/decryption_key.go#L34>)

```go
func (dk DecryptionKey) EncryptionKey() *EncryptionKey
```

EncryptionKey returns the corresponding encryption key for this decryption key

### func \(DecryptionKey\) [MarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/decryption_key.go#L47>)

```go
func (dk DecryptionKey) MarshalBinary() ([]byte, error)
```

MarshalBinary serializes a key to bytes

### func \(\*DecryptionKey\) [UnmarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/decryption_key.go#L64>)

```go
func (dk *DecryptionKey) UnmarshalBinary(data []byte) error
```

UnmarshalBinary deserializes a key from bytes

## type [EncryptionKey](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/encryption_key.go#L28-L32>)

EncryptionKey encrypts a message to a ciphertext from which zero\-knowledge proofs can be derived as described in section 3\.2 in \<https://shoup.net/papers/verenc.pdf\>\. n\, g are stored in the \`PaillierGroup\` struct

```go
type EncryptionKey struct {
    // contains filtered or unexported fields
}
```

### func \(EncryptionKey\) [Encrypt](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/encryption_key.go#L108>)

```go
func (ek EncryptionKey) Encrypt(domain []byte, msgs []*big.Int) (*CipherText, error)
```

Encrypt multiple messages as described in \<https://shoup.net/papers/verenc.pdf\> \`domain\` represents a domain separation tag or nonce

### func \(EncryptionKey\) [EncryptAndProve](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/proof_enc.go#L64>)

```go
func (ek EncryptionKey) EncryptAndProve(nonce []byte, msgs []*big.Int) (*CipherText, *ProofVerEnc, error)
```

EncryptAndProve is a NIZK where the ciphertext and commitments are computed \(t values\)\. The blindings are generated as part of calling this function Return ciphertext and proof created during encryption\. "The protocol" from section 5\.2 in \<https://shoup.net/papers/verenc.pdf\> Not using t = g^m\*h^s as the idemix protocol does not use it\. Guess is that since the knowledge of m is proved in the credential attribute proving protocol\. Use this function if the proof is by itself and not part of a bigger composite proof\.

### func \(EncryptionKey\) [EncryptAndProveBlindings](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/proof_enc.go#L83>)

```go
func (ek EncryptionKey) EncryptAndProveBlindings(nonce []byte, msgs []*big.Int, blindings []*big.Int) (*CipherText, *ProofVerEnc, error)
```

EncryptAndProveBlindings is a NIZK where the ciphertext and commitments are computed \(t values\)\. The blindings are generated prior to calling this function Return ciphertext and proof created during encryption\. "The protocol" from section 5\.2 in \<https://shoup.net/papers/verenc.pdf\> Not using t = g^m\*h^s as the idemix protocol does not use it\. Guess is that since the knowledge of m is proved in the credential attribute proving protocol\. Use this function if the proof will be part of more proofs\.

### func \(EncryptionKey\) [MarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/encryption_key.go#L69>)

```go
func (ek EncryptionKey) MarshalBinary() ([]byte, error)
```

MarshalBinary serializes a key to bytes

### func \(\*EncryptionKey\) [UnmarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/encryption_key.go#L86>)

```go
func (ek *EncryptionKey) UnmarshalBinary(data []byte) error
```

UnmarshalBinary deserializes a key from bytes

### func \(EncryptionKey\) [VerifyEncryptProof](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/proof_enc.go#L202>)

```go
func (ek EncryptionKey) VerifyEncryptProof(nonce []byte, ciphertext *CipherText, proof *ProofVerEnc) error
```

VerifyEncryptProof a Proof of Verifiable Encryption See section 6\.2\.19 in \<https://dominoweb.draco.res.ibm.com/reports/rz3730_revised.pdf\>

## type [PaillierGroup](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L30-L32>)

PaillierGroup holds public values for Verifiable Encryption g and h correspond to the symbols with the same name in the paper\. n = p \* q\, where p = 2p' \+ 1\, q = 2q' \+ 1\, p\, q\, p'\, q' are all prime See section 3\.1 and 3\.2 in verenc\.pdf\. nd4 = n / 4 integer division n2 = n^2 nd4 = n^2 / 4 integer division

```go
type PaillierGroup struct {
    // contains filtered or unexported fields
}
```

### func [NewPaillierGroup](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L41>)

```go
func NewPaillierGroup() (*PaillierGroup, error)
```

NewPaillierGroup creates a new Paillier group for verifiable encryption and generates safe primes for p and q\.

### func [NewPaillierGroupWithPrimes](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L47>)

```go
func NewPaillierGroupWithPrimes(p, q *big.Int) (*PaillierGroup, error)
```

NewPaillierGroupWithPrimes create a new Paillier group for verifiable encryption Order n^2 where n = p \* q

### func \(PaillierGroup\) [Abs](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L99>)

```go
func (pg PaillierGroup) Abs(a *big.Int) *big.Int
```

Abs computes a mod n^2 where 0 \< a \< n^2 or \(n^2 \- a\) mod n^2 if a \> n^2/2 See section 3\.2

### func \(PaillierGroup\) [Exp](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L111>)

```go
func (pg PaillierGroup) Exp(base, exp *big.Int) *big.Int
```

Exp computes base^exp mod n^2

### func \(PaillierGroup\) [Gexp](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L126>)

```go
func (pg PaillierGroup) Gexp(exp *big.Int) *big.Int
```

Gexp computes g^exp mod n^2

### func \(PaillierGroup\) [Hash](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L176>)

```go
func (pg PaillierGroup) Hash(u *big.Int, e []*big.Int, data []byte) (*big.Int, error)
```

Hash computes h\(u\, e\, L\) for encryption/decryption

### func \(PaillierGroup\) [Hexp](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L131>)

```go
func (pg PaillierGroup) Hexp(exp *big.Int) *big.Int
```

Hexp computes h^exp mod n^2

### func \(PaillierGroup\) [Inv](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L121>)

```go
func (pg PaillierGroup) Inv(val *big.Int) *big.Int
```

Inv computes val^\-1 mod n^2

### func \(PaillierGroup\) [MarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L146>)

```go
func (pg PaillierGroup) MarshalBinary() ([]byte, error)
```

MarshalBinary serializes a paillier group to a byte sequence

### func \(PaillierGroup\) [Mul](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L115>)

```go
func (pg PaillierGroup) Mul(lhs, rhs *big.Int) *big.Int
```

### func \(PaillierGroup\) [Rand](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L136>)

```go
func (pg PaillierGroup) Rand() (*big.Int, error)
```

Rand returns a random v ∈ \[1\, n^2 / 4\)

### func \(PaillierGroup\) [RandForEncrypt](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L141>)

```go
func (pg PaillierGroup) RandForEncrypt() (*big.Int, error)
```

RandForEncrypt returns a random v ∈ \[1\, n / 4\)

### func \(\*PaillierGroup\) [UnmarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/paillier_group.go#L157>)

```go
func (pg *PaillierGroup) UnmarshalBinary(data []byte) error
```

UnmarshalBinary deserializes a paillier group from a byte sequence

## type [ProofVerEnc](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/proof_enc.go#L19-L22>)

ProofVerEnc is a proof of verifiable encryption for a discrete log

```go
type ProofVerEnc struct {
    // contains filtered or unexported fields
}
```

### func \(ProofVerEnc\) [MarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/proof_enc.go#L30>)

```go
func (pf ProofVerEnc) MarshalBinary() ([]byte, error)
```

### func \(\*ProofVerEnc\) [UnmarshalBinary](<https://github.com/coinbase/kryptology/blob/master/pkg/verenc/camshoup/proof_enc.go#L42>)

```go
func (pf *ProofVerEnc) UnmarshalBinary(data []byte) error
```



Generated by [gomarkdoc](<https://github.com/princjef/gomarkdoc>)
