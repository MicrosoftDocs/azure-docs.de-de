---
title: 'Neuigkeiten Versionshinweise: Azure Blockchain Service'
description: Erfahren Sie, was es Neues im Azure Blockchain Service gibt, wie die aktuellsten Versionshinweise, Versionen, bekannten Probleme und bevorstehenden Änderungen.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 6a3113a2d28e704b188d701da13493ecd8263cab
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335025"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Neues im Azure Blockchain Service

> Lassen Sie sich über Updates auf dieser Seite benachrichtigen, indem Sie die URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` kopieren und in Ihren RSS-Feedreader einfügen [![Symbol des RSS-Feedreaders](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522).

Es werden fortlaufend Verbesserungen am Azure Blockchain Service vorgenommen. Damit Sie bezüglich der neuesten Entwicklungen auf dem neuesten Stand bleiben, bietet dieser Artikel Informationen zu Folgendem:

- Neue Funktionen
- Versionsupgrades
- Bekannte Probleme

---

## <a name="june-2020"></a>Juni 2020

### <a name="version-upgrades"></a>Versionsupgrades

- Upgrade der Quorum-Version auf 2.6.0. In Version 2.6.0 können Sie signierte private Transaktionen senden. Weitere Informationen zum Senden privater Transaktionen finden Sie in der [Quorum-API-Dokumentation](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis).
- Upgrade der Tessera-Version auf 0.10.5.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>Vertragsgröße und Transaktionsgröße wurden auf 128 KB angehoben

Typ: Konfigurationsänderung

Die Vertragsgröße (MaxCodeSize) wurde auf 128 KB angehoben, sodass Sie größere Smart Contracts bereitstellen können. Außerdem wurde die Transaktionsgröße auf 128 KB erhöht. Konfigurationsänderungen gelten für neue Konsortien, die nach dem 19. Juni 2020 im Azure Blockchain Service erstellt wurden.

### <a name="trietimeout-value-reduced"></a>Trietimeout-Wert reduziert

Typ: Konfigurationsänderung

Der TrieTimeout-Wert wurde herabgesetzt, sodass der speicherinterne Zustand häufiger auf den Datenträger geschrieben wird. Mit dem niedrigeren Wert wird die schnellere Wiederherstellung eines Knotens im seltenen Fall eines Knotenabsturzes sichergestellt.

## <a name="may-2020"></a>Mai 2020

### <a name="version-upgrades"></a>Versionsupgrades

- Upgrade der Ubuntu-Version auf 18.04
- Upgrade der Quorum-Version auf 2.5.0
- Upgrade der Tessera-Version auf 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Der Azure Blockchain Service unterstützt das Senden von rawPrivate-Transaktionen

Typ: Funktion

Kunden können private Transaktionen außerhalb des Kontos auf dem Knoten signieren.

### <a name="two-phase-member-provisioning"></a>Zweistufige Bereitstellung von Mitgliedern

Typ: Erweiterung

Die zweistufige Bereitstellung erleichtert die Optimierung von Szenarien, in denen ein Mitglied in einem lange Zeit bestehenden Konsortium erstellt wird. Die Mitgliedsinfrastruktur wird in der ersten Stufe bereitgestellt. In der zweiten Stufe wird das Mitglied mit der Blockchain synchronisiert. Durch die zweistufige Bereitstellung werden Fehler bei der Mitgliedserstellung aufgrund von Zeitüberschreitungen vermieden.

## <a name="known-issues"></a>Bekannte Probleme

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>eth.estimateGas-Funktion löst Ausnahme in Quorum v2.6.0 aus

In Quorum v2.6.0 führen Aufrufe der Funktion *eth.estimateGas* ohne Bereitstellung des zusätzlichen Parameters *value* zu einer Ausnahme vom Typ *Absturz des Methodenhandlers*. Das Quorum-Team wurde benachrichtigt, und eine Problembehebung wird zu Ende Juli 2020 erwartet. Bis eine Korrektur verfügbar ist, können Sie die folgenden Problemumgehungen verwenden:

- Vermeiden Sie die Verwendung von *eth.estimateGas* , da sie die Leistung beeinträchtigen kann. Weitere Informationen zu den Leistungsproblemen bei eth.estimateGas finden Sie unter [Aufrufen der eth.estimateGas-Funktion führt zu verringerter Leistung](#calling-ethestimategas-function-reduces-performance). Schließen Sie in jede Transaktion einen Wert für gas ein. Die meisten Bibliotheken rufen eth.estimateGas auf, wenn kein Wert für gas angegeben wird, was zu einem Absturz von Quorum v2.6.0 führen würde.
- Wenn Sie *eth.estimateGas* aufrufen müssen, schlägt das Quorum-Team als Problemumgehung vor, dass Sie den zusätzlichen Parameter *value* als *0* übergeben.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>Mining wird bei weniger als vier Validierungsknoten beendet

Produktionsnetzwerke sollten mindestens vier Validierungsknoten aufweisen. Für Quorum werden mindestens vier Validierungsknoten benötigt, um den IBFT-Regeln für Absturzfehlertoleranz (3F+1) zu genügen. Sie sollten über mindestens zwei Azure Blockchain Service-Knoten im Tarif *Standard* verfügen, um vier Validierungsknoten zu erhalten. Ein Standardknoten wird mit vier Validierungsknoten bereitgestellt.  

Wenn das Blockchain-Netzwerk im Azure Blockchain Service nicht vier Validierungsknoten aufweist, wird das Mining im Netzwerk möglicherweise beendet. Sie können erkennen, dass das Mining beendet wurde, indem Sie eine Benachrichtigung für verarbeitete Blöcke festlegen. In einem fehlerfreien Netzwerk werden pro Knoten innerhalb von 5 Minuten 60 Blöcke verarbeitet.

Als Abhilfemaßnahme muss das Azure Blockchain Service-Team den Knoten neu starten. Kunden müssen eine Supportanfrage einreichen, um den Knoten neu zu starten. Das Azure Blockchain Service-Team arbeitet daran, Miningprobleme automatisch zu erkennen und zu beheben.

Verwenden Sie *Standard* für Bereitstellungen für die Produktion. Verwenden Sie *Basic* für die Entwicklung, das Testen und Proof of Concept-Vorgänge. Das Wechseln zwischen den Tarifen „Basic“ und „Standard“ nach der Erstellung eines Mitglieds wird nicht unterstützt.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Für Blockchain Data Manager ist ein Knoten im Standard-Tarif erforderlich

Verwenden Sie den *Standard* -Tarif, wenn Sie Blockchain Data Manager verwenden. Der *Basic* -Tarif verfügt nur über 4 GB Arbeitsspeicher. Daher kann er nicht bis zu der Auslastung skaliert werden, die für Blockchain Data Manager und andere auf ihm ausgeführte Dienste erforderlich ist.

Verwenden Sie *Basic* für die Entwicklung, das Testen und Proof of Concept-Vorgänge. Das Wechseln zwischen den Tarifen „Basic“ und „Standard“ nach der Erstellung eines Mitglieds wird nicht unterstützt.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Eine große Menge an Aufrufen zur Entsperrung von Konten führt zu einem Absturz von geth

Eine große Menge an Aufrufen zur Entsperrung von Konten während der Übermittlung einer Transaktion kann zu einem Absturz von geth auf einem Transaktionsknoten führen. Das hat zur Folge, dass Sie das Erfassen von Transaktionen beenden müssen. Andernfalls wächst die Warteschlange der ausstehenden Transaktionen an.

Geth wird in weniger als einer Minute neu gestartet. Abhängig vom Knoten kann die Synchronisierung viel Zeit in Anspruch nehmen. Das Azure Blockchain Service-Team aktiviert eine Archivierungsfunktion, die eine Verkürzung der erforderlichen Synchronisierungszeit bewirkt.

Um geth-Abstürze zu identifizieren, können Sie die Protokolle auf jede Fehlermeldung in Blockchainnachrichten in Anwendungsprotokollen überprüfen. Sie können ferner überprüfen, ob die Anzahl der verarbeiteten Blöcke abnimmt, während die ausstehenden Transaktionen zunehmen.

Um das Problem zu entschärfen, senden Sie mit einem Befehl zum Entsperren des Kontos signierte Transaktionen anstelle von nicht signierten Transaktionen. Für bereits extern signierte Transaktionen besteht keine Notwendigkeit, das Konto zu entsperren.

Wenn Sie nicht signierte Transaktionen senden möchten, entsperren Sie das Konto für unbegrenzte Zeit, indem Sie im Entsperrbefehl „0“ als Zeitparameter übergeben. Sie können das Konto nach dem Übermitteln aller Transaktionen wieder sperren.  

Nachfolgend sind die geth-Parameter aufgeführt, die vom Azure Blockchain Service verwendet werden. Diese Parameter können nicht anpasst werden.

- Istanbul-Blockzeitraum: 5 s
- Unterer Gas-Grenzwert: 700000000
- Oberer Gas-Grenzwert: 800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Große Mengen privater Transaktionen führen zu verringerter Leistung

Wenn Sie Azure Blockchain Service im Basic-Tarif zusammen mit privaten Transaktionen verwenden, stürzt Tessera möglicherweise ab.

Sie können den Tessera-Absturz erkennen, indem Sie die Blockchain-Anwendungsprotokolle überprüfen und nach der Nachricht `Tessera crashed. Restarting Tessera...` suchen.

Azure Blockchain Service löst nach Abstürzen einen Neustart von Tessera aus. Der Neustart nimmt etwa eine Minute in Anspruch.

Verwenden Sie den *Standard* Tarif, wenn Sie eine große Menge privater Transaktionen senden. Verwenden Sie *Basic* für die Entwicklung, das Testen und Proof of Concept-Vorgänge. Das Wechseln zwischen den Tarifen „Basic“ und „Standard“ nach der Erstellung eines Mitglieds wird nicht unterstützt.

### <a name="calling-ethestimategas-function-reduces-performance"></a>Das Aufrufen der eth.estimateGas-Funktion führt zu verringerter Leistung

Das mehrmalige Aufrufen der Funktion *eth.estimateGas* verringert die Transaktionen pro Sekunde drastisch. Verwenden Sie die Funktion *eth.estimateGas* nicht für jede Transaktionsübermittlung. Die Funktion *eth.estimateGas* ist sehr arbeitsspeicherintensiv.

Verwenden Sie nach Möglichkeit einen konservativen gas-Wert für die Übermittlung von Transaktionen, und minimieren Sie die Verwendung von *eth.estimateGas*.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Unbegrenzte Schleifen in Smart Contracts führen zu verringerter Leistung

Vermeiden Sie unbegrenzte Schleifen in Smart Contracts, da sie die Leistung beeinträchtigen können. Weitere Informationen finden Sie in den folgenden Ressourcen:

- [Vermeiden von unbegrenzten Schleifen](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Bewährte Methoden für die Sicherheit von Smart Contracts](https://github.com/ConsenSys/smart-contract-best-practices)
- [Von Quorum bereitgestellte Smart Contract-Richtlinien](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Von Solidity bereitgestellte Richtlinien für Gas-Grenzwerte und Schleifen](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)