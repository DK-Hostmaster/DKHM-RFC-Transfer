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
- [XSD Definition](#xsd-definition)
- [References](#references)

<!-- /MarkdownTOC -->

<a id="introduction"></a>
## Introduction

This is a draft and proposal for implementation of the process for domain name transfer via the DK Hostmaster EPP portal/service.

<a id="about-this-document"></a>
### About this Document

We have adopted the term RFC (_Request For Comments_), due to the recognition in the term and concept, so this document is a process supporting document, aiming to serve the purpose of obtaining a common understanding of the proposed implementation and to foster discussion on the details of the implementation. The final specification will be lifted into the [DK Hostmaster EPP Service Specification](https://github.com/DK-Hostmaster/epp-service-specification) implementation and this document will be closed for comments and the document no longer be updated.

<a id="xml-and-xsd-examples"></a>
### XML and XSD Examples

All example XML files are available in the [DK Hostmaster EPP XSD repository](https://github.com/DK-Hostmaster/epp-xsd-files).

<a id="description"></a>
## Description

DK Hostmaster are extending the registry capabilities to support _one-stop-shop_ so registrars and registrants will have the option to pick and model for domain name administration with suit their requirements.

This paves the way for support of an implementation of the EPP transfer command.

The command will be extended to support the DK Hostmaster registry, with the following:

- Support for AuthInfo tokens and handling of these
- Support for handling of automatic renewal
- Support for implicit transfer at registration time

Automatic renewal is described in detail in a separate RFC: "[DKHM RFC for handling of Automatic Renewal][DKHMRFCAUTORENEW]", the aspects in regard to transfer will however be described here. Please see the mentioned RFC for all the details automatic renewal.

<a id="references"></a>
## References

- [DK Hostmaster EPP Service Specification](https://github.com/DK-Hostmaster/epp-service-specification)
- [DK Hostmaster EPP Service XSD Repository](https://github.com/DK-Hostmaster/epp-xsd-files)
- [RFC:5730 "Extensible Provisioning Protocol (EPP)"][RFC5730]
- [RFC:5731 "Extensible Provisioning Protocol (EPP) Domain Name Mapping"][RFC5731]
- ["DKHM RFC for handling of Automatic Renewal][DKHMRFCAUTORENEW]

[RFC5730]: https://www.rfc-editor.org/rfc/rfc5730.html
[RFC5731]: https://www.rfc-editor.org/rfc/rfc5731.html
[DKHMRFCAUTORENEW]: https://github.com/DK-Hostmaster/DKHM-RFC-AutoRenew
