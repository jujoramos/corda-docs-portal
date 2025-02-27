---
date: '2023-11-01'
description: Describes how to set up a CorDapp development environment, including writing, compiling, and running a first basic CorDapp using the CorDapp Standard Development Environment (CSDE).
title: "CSDE"
project: corda5-tools
section_menu: corda5-tools
version: tools
menu:
  corda5-tools:
    weight: 1000
    identifier: corda5-develop-get-started
aliases: ../platform/corda/5.0/developing-applications/getting-started.html
---
# CSDE

This section guides Developers who are new to Corda 5 from setting up their development environment through to writing, compiling, and running their first basic {{< tooltip >}}CorDapp{{< /tooltip >}}.

The CorDapp Standard Development Environment (CSDE) makes the process of prototyping CorDapps more straight-forward.
The CSDE is obtained by cloning our `CSDE-cordapp-template-kotlin` or `CSDE-cordapp-template-java` repository to your local machine. The CSDE provides:

* a prepared CorDapp project that you can use as a starting point to develop your own prototypes.
* a base Gradle configuration that brings in the dependencies you need to write and test a Corda 5 CorDapp.
* a set of Gradle helper tasks which speed up and simplify the development and deployment process; these are effectively wrappers over the {{< tooltip >}}Corda CLI{{< /tooltip >}}.
* debug configuration for debugging a local Corda cluster.
* the `MyFirstFlow` code which forms the basis of the Getting Started documentation.
* the `utxoexample` Chat CorDapp, which provides a basic, working {{< tooltip >}}UTXO{{< /tooltip >}} Ledger CorDapp.
* the ability to configure the members of the local Corda network.
