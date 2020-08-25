![DK Hostmaster Logo](https://www.dk-hostmaster.dk/sites/default/files/dk-logo_0.png)

# DKHM RFC for Transfer Domain EPP Command

![Markdownlint Action](https://github.com/DK-Hostmaster/DKHM-RFC-Transfer/workflows/Markdownlint%20Action/badge.svg)
![Spellcheck Action](https://github.com/DK-Hostmaster/DKHM-RFC-Transfer/workflows/Spellcheck%20Action/badge.svg)

## Table of Contents

<!-- MarkdownTOC bracket=round levels="1,2,3,4" indent="  " autolink="true" autoanchor="true" -->

- [Introduction](#introduction)
  - [About this Document](#about-this-document)
  - [XML and XSD Examples](#xml-and-xsd-examples)
- [Description](#description)
- [Transfer](#transfer)
- [Withdraw](#withdraw)
- [XSD Definition](#xsd-definition)
- [References](#references)

<!-- /MarkdownTOC -->

<a id="introduction"></a>
## Introduction

This is a draft and proposal for implementation of the process for domain name transfer via the DK Hostmaster EPP portal/service.

<a id="about-this-document"></a>
### About this Document

We have adopted the term RFC (_Request For Comments_), due to the recognition in the term and concept, so this document is a process supporting document, aiming to serve the purpose of obtaining a common understanding of the proposed implementation and to foster discussion on the details of the implementation. The final specification will be lifted into the [DK Hostmaster EPP Service Specification][DKHMEPPSPEC] implementation and this document will be closed for comments and the document no longer be updated.

The overall [description of the concept][CONCEPT] of the registrar model offered by DK Hostmaster A/S provided as a general overview, where this RFC digs into the details of the cancellation/deletion of domain names in the context of an implementation proposal.

<a id="xml-and-xsd-examples"></a>
### XML and XSD Examples

All example XML files are available in the [DK Hostmaster EPP XSD repository][DKHMXSDSPEC].

The proposed extensions and XSD definitions are available in the  [3.2 candidate][DKHMXSD3.2] of the DK Hostmaster XSD, which is currently a draft and work in progress and marked as a  _pre-release_.

<a id="description"></a>
## Description

DK Hostmaster are extending the registry capabilities to support _one-stop-shop_ so registrars and registrants will have the option to pick and model for domain name administration with suit their requirements.

This paves the way for support of an implementation of the EPP transfer command.

The command will be extended to support the DK Hostmaster registry, with the following:

- Support for AuthInfo tokens and handling of these
- Support for handling of automatic renewal
- Support for implicit transfer at registration time

Automatic renewal is described in detail in a separate RFC: "[DKHM RFC for handling of Automatic Renewal][DKHMRFCAUTORENEW]", the aspects in regard to transfer will however be described here. Please see the mentioned RFC for all the details automatic renewal.

<a id="transfer"></a>
## Transfer

The transfer command, will adhere to the EPP standard as described in [RFC:5731][RFC5731].

The command will only be available to registrars and require the use of an AuthInfo token and domain name.

An example XML request could look as follows (example lifted from RFC:5731 and modified):

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
  <command>
    <transfer op="request">
      <domain:transfer xmlns:domain="urn:ietf:params:xml:ns:domain-1.0">
        <domain:name>eksempel.dk</domain:name>
        <domain:period unit="y">1</domain:period>
        <domain:authInfo>
          <domain:pw>2fooBAR</domain:pw>
        </domain:authInfo>
      </domain:transfer>
    </transfer>
    <clTRID>ABC-12345</clTRID>
  </command>
</epp>
```

And the response to the request (example lifted from RFC:5731 and modified):

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<epp xmlns="urn:ietf:params:xml:ns:epp-1.0">
  <response>
    <result code="1000">
      <msg>Command completed successfully</msg>
    </result>
    <resData>
      <domain:trnData xmlndomain="urn:ietf:params:xml:ns:domain-1.0">
        <domain:name>eksempel.dk</domain:name>
        <domain:trStatus>serverApproved</domain:trStatus>
        <domain:reID>ClientX</domain:reID>
        <domain:reDate>2000-06-08T22:00:00.0Z</domain:reDate>
        <domain:acID>ClientY</domain:acID>
        <domain:acDate>2000-06-13T22:00:00.0Z</domain:acDate>
        <domain:exDate>2002-09-08T22:00:00.0Z</domain:exDate>
      </domain:trnData>
    </resData>
    <trID>
      <clTRID>ABC-12345</clTRID>
      <svTRID>54322-XYZ</svTRID>
    </trID>
  </response>
</epp>
```

Since the transfer process differs from the one outlined in the [RFC:5731][RFC5731], the following status codes should only be the ones observed:

- `serverApproved`

The following should not be observed, since the process does not implement this transactional model:

- `clientApproved`
- `clientCancelled`
- `clientRejected`
- `pending`
- `serverCancelled`

Upon transfer, the contact objects related to the domain name being transferred are cloned to new similar objects under the administration of the receiving registrar.

The cloning is a best-effort cloning, since the ID-control status cannot be guaranteed to be consistent in the case where a contact object is locked to a register, but has limitations in access to data due to policies in regard to disclosure etc.

Host objects related to the domain name, where the domain name is superordinate and the host objects are subordinate are transferred to the registrar as specified in [RFC:5321][RFC5732]

Do note that a transfer will set the default auto-renewal/expiration for the transferred domain. Please see ["DKHM RFC for handling of Automatic Renewal or Expiration"][DKHMRFCAUTORENEW]

<a id="withdraw"></a>
## Withdraw

DK Hostmaster has decided to support the option for registrar of transferring out, so where the regular transfer command (described above) is a _pull_ operation. The registrar can _push_ a domain name from it's portfolio to DK Hostmaster, when and if a registrar requires so.

The process resembles the transfer, but with the receiving account being DK Hostmaster.

The implementation is based on the extension developed by Norid, the registry for the ccTLD for Norway (.no). The specification is listed in the references section below.

Since this extension is at a higher level than the other extensions defined by DK Hostmaster. The definition look as follows:

```xsd
<!-- dkhm-3.2.xsd -->
<element name="withdraw" type="dkhm:withdrawType"/>
<complexType name="withdrawType">
  <sequence>
    <element name="name" type="eppcom:labelType"/>
  </sequence>
</complexType>
```

Ref: [`dkhm-3.2.xsd`][DKHMXSD3.2]

:warning: The reference and file mentioned above is not released at this time, so this file might be re-versioned upon release.

```xsd
<?xml version="1.0" encoding="UTF-8"?>

<schema targetNamespace="urn:dkhm:params:xml:ns:dkhm-domain-3.2"
        xmlns:dkhm-domain="urn:dkhm:params:xml:ns:dkhm-domain-3.2"
        xmlns:epp="urn:ietf:params:xml:ns:epp-1.0"
        xmlns:eppcom="urn:ietf:params:xml:ns:eppcom-1.0"
        xmlns="http://www.w3.org/2001/XMLSchema"
        elementFormDefault="qualified">

  <import namespace="urn:ietf:params:xml:ns:eppcom-1.0" schemaLocation="eppcom-1.0.xsd"/>
  <import namespace="urn:ietf:params:xml:ns:epp-1.0" schemaLocation="epp-1.0.xsd"/>

  <annotation>
    <documentation>Extensible Provisioning Protocol v1.0 provisioning schema. DKHM extension v3.2 for domain</documentation>
  </annotation>

  <element name="withdraw" type="dkhm-domain:withdrawType"/>

  <complexType name="withdrawType">
    <sequence>
      <element name="name" type="eppcom:labelType"/>
    </sequence>
  </complexType>
</schema>
```

Ref: [`dkhm-domain-3.2.xsd`][DKHMDOMAINXSD3.2]

:warning: The reference and file mentioned above is not released at this time, so this file might be re-versioned upon release.

An example of a withdraw XML request would look as follows (example lifted from Norid specification and modified):

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<epp xmlns="urn:ietf:params:xml:ns:epp-1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="urn:ietf:params:xml:ns:epp-1.0 epp-1.0.xsd">
  <extension>
    <command xmlns="urn:dkhm:params:xml:ns:dkhm-3.2">
      <withdraw>
        <domain:withdraw xmlns:domain="urn:dkhm:params:xml:ns:dkhm-domain-3.2">
          <domain:name>eksempel.dk</domain:name>
        </domain:withdraw>
      </withdraw>
      <clTRID>ABC-12345</clTRID>
    </command>
  </extension>
</epp>
```

<a id="references"></a>
## References

- [DK Hostmaster EPP Service Specification][DKHMEPPSPEC]
- [DK Hostmaster EPP Service XSD Repository][DKHMXSDSPEC]
- [RFC:5730 "Extensible Provisioning Protocol (EPP)"][RFC5730]
- [RFC:5731 "Extensible Provisioning Protocol (EPP) Domain Name Mapping"][RFC5731]
- [RFC:5732 "Extensible Provisioning Protocol (EPP) Host Mapping"][RFC5732]
- ["DKHM RFC for handling of Automatic Renewal][DKHMRFCAUTORENEW]
- [Norid EPP Interface Specification, revision 1e1](https://teknisk.norid.no/uploads/2019/11/EPP_Interface_Specification.1e1.pdf) (PDF)

[RFC5730]: https://www.rfc-editor.org/rfc/rfc5730.html
[RFC5731]: https://www.rfc-editor.org/rfc/rfc5731.html
[RFC5732]: https://www.rfc-editor.org/rfc/rfc5732
[DKHMRFCAUTORENEW]: https://github.com/DK-Hostmaster/DKHM-RFC-AutoRenew
[DKHMEPPSPEC]: https://github.com/DK-Hostmaster/epp-service-specification
[DKHMXSDSPEC]: https://github.com/DK-Hostmaster/epp-xsd-files
[CONCEPT]: https://www.dk-hostmaster.dk/en/new-basis-collaboration-between-registrars-and-dk-hostmaster
[DKHMXSD3.2]: https://github.com/DK-Hostmaster/epp-xsd-files/blob/master/dkhm-3.2.xsd
[DKHMDOMAINXSD3.2]: https://github.com/DK-Hostmaster/epp-xsd-files/blob/master/dkhm-domain-3.2.xsd
