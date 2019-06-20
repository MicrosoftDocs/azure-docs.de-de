---
title: Übersicht über die Entwicklung mit dem Azure Blockchain-Dienst
description: Einführung in die Entwicklung von Lösungen im Azure Blockchain-Dienst
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027616"
---
# <a name="azure-blockchain-service-development-overview"></a>Übersicht über die Entwicklung mit dem Azure Blockchain-Dienst

Mit dem Azure Blockchain-Dienst können Sie Konsortiumsblockchainnetzwerke erstellen, um unter anderem Unternehmensszenarios für die Assetnachverfolgung, digitale Token, Treue und Belohnung, die Supply-Chain-Finanzierung und die Herkunft zu ermöglichen. Dieser Artikel dient als Einführung der Entwicklung mit dem Azure Blockchain-Dienst sowie wichtige Themen für die Implementierung von Blockchains in Unternehmen.

## <a name="client-connection-to-azure-blockchain-service"></a>Verbindung des Clients mit dem Azure Blockchain-Dienst

Es gibt verschiedene Arten von Clients für Blockchainnetzwerke, einschließlich vollständiger Knoten, einfacher Knoten und Remoteclients. Der Azure Blockchain-Dienst erstellt ein Blockchainnetzwerk, das Knoten enthält. Sie können verschiedene Clients als Einstiegspunkt für die Blockchainentwicklung mit dem Azure Blockchain-Dienst verwenden. Der Azure Blockchain-Dienst stellt die Standardauthentifizierung oder den Zugriffsschlüssel als Entwicklungsendpunkt bereit. Im Folgenden werden beliebte Clients zum Herstellen der Verbindung aufgeführt.

### <a name="metamask"></a>MetaMask

MetaMask ist ein browserbasierter Wallet (Remoteclient), RPC-Client und einfacher Vertrags-Explorer. Im Gegensatz zu anderen browserbasierten Wallets injiziert MetaMask eine Web3-Instanz in den JavaScript-Kontext des Browsers und dient als RPC-Client, der eine Verbindung mit mehreren Ethereum-Blockchains (*mainnet*, *Ropsten testnet*, *Kovan testnet*, lokaler RPC-Knoten usw.) herstellt. Sie können einen benutzerdefinierten RPC einrichten, um mithilfe von Remix mühelos eine Verbindung mit den Azure Blockchain-Dienst herzustellen und mit der Blockchainentwicklung zu beginnen.

### <a name="geth"></a>Geth

Geth ist die Befehlszeilenschnittstelle zum Ausführen eines vollständigen Ethereum-Knotens, der in Go implementiert ist. Sie müssen keinen vollständigen Knoten ausführen, jedoch können Sie dessen interaktive Konsole starten, die eine JavaScript-Laufzeitumgebung bereitstellt, die wiederum eine JavaScript-API für die Interaktion mit Azure Blockchain zur Verfügung stellt.

## <a name="development-framework-configuration"></a>Konfiguration des Entwicklungsframeworks

Für die Entwicklung von professionellen Blockchainlösungen für Unternehmen ist ein Entwicklungsframework erforderlich, um Verbindungen mit verschiedenen Blockchainnetzwerken herzustellen, den Lebenszyklus von Smart Contracts zu verwalten, Smart Contracts mit Skripts bereitzustellen und eine interaktive Konsole auszustatten.

Truffle ist ein beliebtes Blockchainentwicklungsframework zum Schreiben, Kompilieren, Bereitstellen und Testen von dezentralisierten Anwendungen für Ethereum-Blockchains. Sie können sich Truffle als Framework vorstellen, das versucht, die Smart Contract-Entwicklung und die traditionelle Webentwicklung nahtlos zu integrieren.

Selbst die kleinsten Projekte interagieren mit mindestens zwei Blockchainknoten: Einer befindet sich auf dem Computer des Entwicklers, und der andere stellt das Netzwerk dar, auf dem der Entwickler die Anwendung bereitstellt. Beispiele hierfür sind das öffentliche Ethereum-Hauptnetzwerk oder der Azure Blockchain-Dienst. Truffle stellt ein System für die Verwaltung der Kompilierungs- und Bereitstellungsartefakte für jedes Netzwerk auf eine gewisse Art und Weise bereit, die die Bereitstellung der endgültigen Anwendung vereinfacht. Weitere Informationen finden Sie unter [Quickstart: Use Truffle to connect to a an Azure Blockchain Service network (Schnellstart: Verwenden von Truffle zum Herstellen einer Verbindung mit einem Azure Blockchain-Netzwerk)](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Private Ethereum Quorum-Transaktion

Quorum ist ein auf Ethereum basierendes verteiltes Ledgerprotokoll mit Datenschutz für Transaktionen und Verträge sowie neuen Konsensmechanismen. Die folgenden wichtigen Verbesserungen gegenüber Go-Ethereum sind enthalten:

* Datenschutz: Quorum unterstützt private Transaktionen und Verträge über die Aufteilung privater Zustände und nutzt den Peer-to-Peer-Austausch verschlüsselter Nachrichten für die direkte Übertragung privater Daten zwischen Netzwerkteilnehmern.
* Alternative Konsensmechanismen: ohne erforderlichen Konsens für Proof-of-Work oder Proof-of-Stake in einem berechtigten Netzwerk. Quorum stellt mehrere Konsensmechanismen bereit, die für Konsortiumblockchains wie RAFT und IBFT entwickelt wurden.  Azure Blockchain nutzt den IBFT-Konsensmechanismus.

* Peer-Berechtigungsvergabe: Die Berechtigung von Knoten und Peers erfolgt mithilfe von Smart Contracts, wodurch sichergestellt wird, dass nur bekannte Parteien dem Netzwerk beitreten können.
* Bessere Leistung: Quorum bietet bessere Leistung als die öffentliche Geth-Schnittstelle.

Eine Schritt-für-Schritt-Anleitung finden Sie im [Tutorial: Send a transaction using Azure Blockchain Service (Tutorial: Senden von Transaktionen mithilfe des Azure Blockchain-Diensts)](send-transaction.md). Außerdem finden Sie in diesem Artikel ein Beispiel für private Transaktionen.

## <a name="block-explorers"></a>Block-Explorer

Block-Explorer sind Blockchainbrowser, die individuelle Blockinhalte sowie Adress- und Verlaufsdaten zu Transaktionen anzeigen. Grundlegende Blockinformationen stehen über Azure Monitor im Azure Blockchain-Dienst zur Verfügung. Wenn Sie jedoch ausführlichere Informationen während der Entwicklung benötigen, können sich Block-Explorer als nützlich erweisen.  Es gibt einige beliebte Open-Source-Block-Explorer, die Sie verwenden können. In der folgenden Liste werden Block-Explorer aufgeführt, die mit dem Azure Blockchain-Dienst funktionieren:

* [Azure Blockchain Service Explorer](https://web3labs.com/azure-offer) von Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>TPS-Messung

Mit der steigenden Verwendung von Blockchains in Unternehmensszenarios spielt die Geschwindigkeit der Transaktionen pro Sekunde (TPS) eine größere Rolle bei der Vermeidung von Leistungsengpässen und Ineffizienzen im System. Die Beibehaltung hoher Transaktionsraten kann innerhalb einer dezentralisierten Blockchain schwierig sein. Eine genaue TPS-Messung kann von verschiedenen Faktoren beeinträchtigt werden, z. B. vom Serverthread, von der Größe der Transaktionswarteschlange und von der Sicherheit. Wenn Sie die TPS-Geschwindigkeit während der Entwicklung messen müssen, empfiehlt sich das beliebte Open-Source-Tool [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Use Truffle to connect to a an Azure Blockchain Service network (Schnellstart: Verwenden von Truffle zum Herstellen einer Verbindung mit einem Azure Blockchain-Netzwerk)](connect-truffle.md)
