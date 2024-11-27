---
export_on_save:
  html: true

presentation:
  theme: league.css
  width: 1280
  height: 960
---

<!-- slide -->
# NDN Security
# Identity Management
### Used for CS217B
Zhiyi Zhang
Apr 2020

<!-- slide -->
## Identity, Key, and Certificate

* An identity is a name that is associated with a pair of public/private key pair.
  * E.g., /example is an identity

* Each key pair also has a name
  * E.g., /example/KEY/123
    * `example` is identity name
    * `KEY` is a const component in all NDN key names
    * `123` is key ID

* A certificate is an Data packet whose content is the public key bits and is signed by a CA
  * Show a real example

<!-- slide -->
## Their Relationship

* A device can have multiple identities.
  * E.g., My laptop owns three identities `/zhiyi-home/laptop`, `/ndn/edu/ucla/zhiyi`, `/ndn/edu/ucla/ndncert`

* An identity can have multiple keys.
  * E.g., My identity `/ndn/edu/ucla/zhiyi` has multiple keys because I keep renewing my key pair for best practice

* Each key can have multiple certificates.
  * E.g., My key `/ndn/edu/ucla/zhiyi/KEY/123` can have multiple certificates: a self-signed certificate, and a number of certificates issued by the NDN testbed CA.

<!-- slide -->
## Commonly Used Commands

* List identities, keys, and certificates: **ndnsec-ls-identity**
* Generate new identities, keys, and certificates: **ndnsec-key-gen**
* Delete identities, keys, and certificates: **ndnsec-delete**
* Signing a certificate for a key: **ndnsec-sign-req**, **ndnsec-cert-gen**, **ndnsec-cert-install**
* Dump certificate: **ndnsec-cert-dump**
* Transfer your private key: **ndnsec-export**, **ndnsec-import**

<!-- slide -->
## Review the Basic Concepts of Trust Schema

Trust schema is based over identities, keys, and certificates.

* A rule matches Interest/Data packets with certain name
  * E.g., `/home/<>*`: `/home/temperature`, `/home/bedroom/heater`
* The rule have a requirement of which **identity**'s **key** is trustworthy to sign this packet
  * E.g., `/home/zhiyi/KEY/<>`: only Zhiyi's key can be used to sign these Interest/Data packets
* The rule must contain a trust anchor **certificate**
  * Otherwise, an attacker can generate a key called `/home/zhiyi/KEY/123` and impersonate me.
  * The trust anchor must be installed by all system users in a secure way.
* Trust schema is a set of rules

<!-- slide -->
## A real example of trust schema rule

In ValidatorConfig's syntax that has been supported by ndn-cxx.
You can use it in your program :D

```ascii
rule
{
  id "home data rule"
  for data
  filter
  {
    type name
    name /home
    relation is-prefix-of
  }
  checker
  {
    type customized
    sig-type rsa-sha256
    key-locator
    {
      type name
      name /home/zhiyi
      relation is-prefix-of
    }
  }
}
trust-anchor
{
  type file
  file-name "anchor.cert"
}
```

Find more details from the [NDN website](https://named-data.net/doc/ndn-cxx/current/tutorials/security-validator-config.html).

<!-- slide -->
## Next Monday: Demonstrate the use of APIs of NDN security

* Introduction of ndn-cxx's security modules
* Straightforward signing and verification of Interest and Data packets using ndn-cxx
* Use of trust schema with ndn-cxx