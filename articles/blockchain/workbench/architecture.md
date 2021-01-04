---
title: Azure Blockchain Workbench-Architektur
description: Übersicht über die Architektur von Azure Blockchain Workbench (Vorschau) und die zugehörigen Komponenten.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 1fff70ef2eeb1dc27d33769fd48fe5923f56717b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021604"
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain Workbench-Architektur

Azure Blockchain Workbench (Vorschau) vereinfacht die Entwicklung von Blockchainanwendungen, indem eine Lösung bereitgestellt wird, die mehrere Azure-Komponenten nutzt. Azure Blockchain Workbench wird mithilfe einer Lösungsvorlage im Azure Marketplace bereitgestellt. Über die Vorlage können Sie die Module und Komponenten auswählen, die bereitgestellt werden sollen, wie z.B. Blockchainstapel, Typ der Clientanwendung und Unterstützung für die IoT-Integration. Nach der Bereitstellung bietet Blockchain Workbench Zugriff auf eine Webanwendung, eine iOS-Anwendung und eine Android-Anwendung.

![Blockchain Workbench-Architektur](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identität und Authentifizierung

Mit Blockchain Workbench kann ein Konsortium seine Unternehmensidentitäten mit Azure Active Directory (Azure AD) zusammenführen. Workbench generiert mit den in Azure AD gespeicherten Unternehmensidentitäten neue Benutzerkonten für On-Chain-Identitäten. Die Identitätszuordnung erleichtert die authentifizierte Anmeldung bei Client-APIs und -anwendungen und verwendet die Authentifizierungsrichtlinien von Unternehmen. Workbench bietet auch die Möglichkeit, Unternehmensidentitäten bestimmten Rollen innerhalb eines festgelegten intelligenten Vertrags zuzuordnen. Darüber hinaus bietet Workbench einen Mechanismus, um die Aktionen, die diese Rollen ausführen können, sowie deren Ausführungszeitpunkt zu identifizieren.

Nach der Bereitstellung von Blockchain Workbench interagieren Benutzer mit dieser Lösung entweder über die Clientanwendungen, die REST-basierte Client-API oder die Messaging-API. In allen Fällen müssen Interaktionen authentifiziert werden – entweder über Azure Active Directory (Azure AD)- oder gerätespezifische Anmeldeinformationen.

Benutzer geben ihre Identitäten an ein Konsortium-Azure AD weiter, indem sie eine Einladung an die E-Mail-Adresse von Teilnehmern senden. Beim Anmelden werden diese Benutzer mit Name, Kennwort und Richtlinien authentifiziert. Ein Beispiel hierfür wäre die zweistufige Authentifizierung ihrer Organisation.

Azure AD wird verwendet, um alle Benutzer zu verwalten, die Zugriff auf Blockchain Workbench haben. Jedes Gerät, das mit einem intelligenten Vertrag verbunden wird, ist ebenfalls mit Azure AD verknüpft.

Azure AD wird auch verwendet, um Benutzer einer speziellen Administratorgruppe zuzuordnen. Der Administratorengruppe zugeordnete Benutzer verfügen über Zugriff auf Rechte und Aktionen in Blockchain Workbench – hierzu gehört z.B. die Bereitstellung von Verträgen und die Vergabe von Zugriffsberechtigungen für einen Vertrag an einen Benutzer. Benutzer außerhalb dieser Gruppe haben keinen Zugriff auf Administratoraktionen.

## <a name="client-applications"></a>Clientanwendungen

Workbench bietet automatisch generierte Clientanwendungen für Web und mobile Geräte (iOS, Android), mit denen Blockchainanwendungen überprüft, getestet und angezeigt werden können. Die Anwendungsschnittstelle wird dynamisch auf Grundlage von intelligenten Vertragsmetadaten generiert und kann jeden Anwendungsfall berücksichtigen. Die Clientanwendungen liefern ein benutzerorientiertes Front-End für die von Blockchain Workbench generierten vollständigen Blockchainanwendungen. Clientanwendungen authentifizieren Benutzer über Azure Active Directory (Azure AD) und präsentieren dann eine auf den Geschäftskontext des intelligenten Vertrags zugeschnittene Benutzeroberfläche. Die Benutzeroberfläche ermöglicht die Erstellung neuer intelligenter Vertragsinstanzen durch autorisierte Personen. Anschließend können bestimmte Arten von Transaktionen zu geeigneten Zeitpunkten im Geschäftsprozess des intelligenten Vertrags ausgeführt werden.

In der Webanwendung haben autorisierte Benutzer die Möglichkeit, auf die Administratorkonsole zuzugreifen. Die Konsole ist für Benutzer der Administratorgruppe in Azure AD verfügbar und bietet Zugriff auf die folgenden Funktionen:

* Bereitstellen von durch Microsoft angebotenen intelligenten Verträgen für gängige Szenarien. Zum Beispiel ein Szenario zum Übertragen von Ressourcen.
* Hochladen und Bereitstellen eigener intelligenter Verträge.
* Zuordnen eines Benutzerzugriffs zu dem intelligenten Vertrag im Rahmen einer bestimmten Rolle.

Weitere Informationen finden Sie im Artikel über [Azure Blockchain Workbench-Beispielclientanwendungen auf GitHub](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>Gatewaydienst-API

Blockchain Workbench umfasst eine REST-basierte Gatewaydienst-API. Während des Schreibvorgangs in eine Blockchain generiert die API Nachrichten und sendet diese an einen Ereignisbroker. Fordert die API Daten an, werden Abfragen an die Off-Chain-Datenbank gesendet. Die Datenbank enthält ein Replikat der On-Chain-Daten und Metadaten, die Kontext- und Konfigurationsinformationen für unterstützte intelligente Verträge liefern. Abfragen geben die benötigten Daten aus dem Off-Chain-Replikat in einem Format zurück, das durch die Metadaten des Vertrags informiert wird.

Entwickler können auf die Gatewaydienst-API zugreifen, um Blockchainlösungen zu erstellen oder zu integrieren, ohne sich auf die Clientanwendungen von Blockchain Workbench verlassen zu müssen.

> [!NOTE]
> Um einen authentifizierten Zugriff auf die API zu ermöglichen, werden zwei Clientanwendungen im Azure Active Directory registriert. Azure Active Directory erfordert für jeden Anwendungstyp (nativ und Web) unterschiedliche Anwendungsregistrierungen.

## <a name="message-broker-for-incoming-messages"></a>Nachrichtenbroker für eingehende Nachrichten

Entwickler, die Nachrichten direkt an Blockchain Workbench senden möchten, können diese direkt an Service Bus senden. Beispielsweise kann die Nachrichten-API für die Integration in Systemen oder für IoT-Geräte verwendet werden.

## <a name="message-broker-for-downstream-consumers"></a>Nachrichtenbroker für Downstreamconsumer

Während des Lebenszyklus der Anwendung treten Ereignisse auf. Ereignisse können durch die Gateway-API oder auf dem Ledger ausgelöst werden. Ereignisbenachrichtigungen können basierend auf Ereignissen einen Downstreamcode initiieren.

Blockchain Workbench stellt automatisch zwei Typen von Ereignisconsumern bereit. Der eine Consumer wird durch Blockchainereignisse ausgelöst, die den Off-Chain-SQL-Speicher auffüllen sollen. Der andere Consumer wird zur Erfassung von Metadaten für Ereignisse benötigt, die von der API im Zusammenhang mit dem Hochladen und Speichern von Dokumenten erzeugt werden.

## <a name="message-consumers"></a>Nachrichtenconsumer

 Nachrichtenconsumer erhalten Nachrichten von Service Bus. Das zugrundeliegende Ereignismodell für Nachrichtenconsumer ermöglicht die Erweiterung durch zusätzliche Dienste und Systeme. Diese können Sie beispielsweise zum Füllen von CosmosDB oder Auswerten von Nachrichten mit Azure Streaming Analytics verwenden. Die folgenden Abschnitte beschreiben die in Blockchain Workbench enthaltenen Nachrichtenconsumer.

### <a name="distributed-ledger-consumer"></a>Distributed Ledger-Consumer

Nachrichten der Distributed Ledger-Technologie (DLT) enthalten die Metadaten für Transaktionen, die in die Blockchain geschrieben werden sollen. Der Consumer ruft die Nachrichten ab und pusht die Daten zu einem Transaktionsgenerator, -signaturgeber und -router.

### <a name="database-consumer"></a>Datenbankconsumer

Der Datenbankconsumer nimmt Nachrichten von Service Bus entgegen und pusht die Daten zu einer angehängten Datenbank, z. B. einer Datenbank in Azure SQL-Datenbank.

### <a name="storage-consumer"></a>Speicherconsumer

Der Speicherconsumer empfängt Nachrichten von Service Bus und pusht Daten zu einem angeschlossenen Speicher. Ein Beispiel hierfür wäre das Speichern von Hashdokumenten in Azure Storage.

## <a name="transaction-builder-and-signer"></a>Transaktionsgenerator und -signaturgeber

Wenn eine Nachricht auf dem Broker für eingehende Nachrichten an die Blockchain geschrieben werden muss, wird sie vom DLT-Consumer verarbeitet. Der DLT-Consumer ist ein Dienst, der die Nachricht mit den Metadaten zur Ausführung einer gewünschten Transaktion abruft und diese dann an den *Transaktionsgenerator und -signaturgeber* sendet. Der *Transaktionsgenerator und -signaturgeber* stellt eine Blockchaintransaktion anhand der Daten und des gewünschten Blockchainziels zusammen. Anschließend wird die Transaktion signiert. Private Schlüssel werden in Azure Key Vault gespeichert.

 Blockchain Workbench holt den entsprechenden privaten Schlüssel aus Key Vault und signiert die Transaktion außerhalb von Key Vault. Nach dem Signieren wird die Transaktion an Transaktionsrouter und Ledger gesendet.

## <a name="transaction-routers-and-ledgers"></a>Transaktionsrouter und Ledger

Transaktionsrouter und Ledger nehmen signierte Transaktionen auf und leiten sie an die entsprechende Blockchain weiter. Blockchain Workbench unterstützt derzeit Ethereum als Zielblockchain.

## <a name="dlt-watcher"></a>DLT-Watcher

Ein DLT-Watcher (Distributed Ledger-Technologie) überwacht Ereignisse bei Blockchains, die mit Blockchain Workbench verbunden sind.
Ereignisse geben personen- und systemrelevante Informationen wider. Beispiele hierfür sind das Erstellen neuer Vertragsinstanzen, das Ausführen von Transaktionen oder das Ändern von Zuständen. Die Ereignisse werden erfasst und an den Broker für ausgehende Nachrichten gesendet, sodass sie von Downstreamconsumern genutzt werden können.

Beispielsweise überwacht der SQL-Consumer Ereignisse und nutzt diese, um die Datenbank mit den enthaltenen Werten zu füllen. Mithilfe der Kopie ist die Wiederherstellung eines Replikats von On-Chain-Daten in einem Off-Chain-Speicher möglich.

## <a name="azure-sql-database"></a>Azure SQL-Datenbank

Die mit Blockchain Workbench verbundene Datenbank speichert Vertragsdefinitionen, Konfigurationsmetadaten und ein für SQL zugängliches Replikat der in der Blockchain gespeicherten Daten. Diese Daten können einfach abgefragt, visualisiert oder analysiert werden, indem direkt auf die Datenbank zugegriffen wird. Entwickler und andere Benutzer können die Datenbank für Berichte, Analysen oder andere datenorientierte Integrationen nutzen. Beispielsweise können Benutzer Transaktionsdaten mit Power BI visualisieren.

Dieser Off-Chain-Speicher bietet Unternehmen die Möglichkeit, Daten in SQL statt in einem Blockchainledger abzufragen. Durch die Vereinheitlichung auf ein Standardschema, das unabhängig von Stapeln der Blockchaintechnologie ist, ermöglicht der Off-Chain-Speicher die Wiederverwendung von Berichten und anderen Artefakten in Projekten, Szenarien und Organisationen.

## <a name="azure-storage"></a>Azure Storage

Azure Storage wird verwendet, um Verträge und mit Verträgen verbundene Metadaten zu speichern.

Von Bestellungen und Frachtbriefen, über in den Nachrichten verwendete und medizinische Bilder, bis hin zu Videos, die aus einem Kontinuum wie Polizeikörperkameras und großen Filmen stammen, spielen Dokumente in vielen Blockchain-orientierten Szenarien eine Rolle. Dokumente sind nicht geeignet, um sie direkt in der Blockchain zu platzieren.

Blockchain Workbench bietet die Möglichkeit, Dokumente oder andere Medieninhalte mit Blockchaingeschäftslogik hinzuzufügen. Ein Hash des Dokuments oder Medieninhalts wird in der Blockchain und das eigentliche Dokument bzw. der Medieninhalt in Azure Storage gespeichert. Die zugehörigen Transaktionsinformationen werden an den Broker für eingehende Nachrichten gesendet, verpackt, signiert und an die Blockchain weitergeleitet. Dieser Prozess löst Ereignisse aus, die über den Broker für ausgehende Nachrichten freigegeben werden. Die SQL-Datenbank nutzt diese Informationen auf und sendet sie zur späteren Abfrage an die Datenbank. Downstreamsysteme könnten diese Ereignisse ebenfalls nutzen, um ggf. zu agieren.

## <a name="monitoring"></a>Überwachung

Workbench ermöglicht die Anwendungsprotokollierung mit Application Insights und Azure Monitor. Application Insights wird verwendet, um alle protokollierten Informationen aus Blockchain Workbench zu speichern, einschließlich Fehler, Warnungen und erfolgreiche Vorgänge. Application Insights kann von Entwicklern eingesetzt werden, um Probleme mit Blockchain Workbench zu beheben. 

Azure Monitor liefert Informationen über die Integrität des Blockchainnetzwerks. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen von Azure Blockchain Workbench](./deploy.md)
