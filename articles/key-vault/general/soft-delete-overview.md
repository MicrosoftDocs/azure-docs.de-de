---
title: Vorläufiges Löschen in Azure Key Vault | Microsoft-Dokumentation
description: Die Funktion für vorläufiges Löschen in Azure Key Vault ermöglicht Ihnen die Wiederherstellung gelöschter Schlüsseltresore und Schlüsseltresorobjekte, z. B. Schlüssel, Geheimnisse und Zertifikate.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: 184003e42a14e786cc52c4f585c6781f7b9fe0e7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452185"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen

> [!IMPORTANT]
> Sie müssen vorläufiges Löschen für Ihre Schlüsseltresore sofort aktivieren. Die Möglichkeit, das vorläufige Löschen zu deaktivieren, wird am Ende des Jahres eingestellt, und der vorläufige Löschschutz wird für alle Schlüsseltresore automatisch aktiviert.  Ausführliche Informationen finden Sie [hier](soft-delete-change.md).

Das Key Vault-Feature für vorläufiges Löschen ermöglicht die Wiederherstellung der gelöschten Tresore und Tresorobjekte, z. B. Schlüssel, Geheimnisse und Zertifikate. Insbesondere werden die folgenden Szenarien behandelt:  Dieser Schutz bietet die folgenden Schutzmaßnahmen:

- Sobald Geheimnisse, Schlüssel, Zertifikate oder Schlüsseltresore gelöscht wurden, können sie wiederhergestellt werden. Dies ist je nach Ihrer Konfiguration für einen Zeitraum von sieben bis 90 Kalendertagen möglich. Wenn keine Konfiguration angegeben wird, wird der Standardwiederherstellungszeitraum von 90 Tagen festgelegt. So haben Benutzer ausreichend Zeit, ein versehentliches Löschen eines Geheimnisses zu erkennen und darauf zu reagieren.
- Zwei Vorgänge müssen durchgeführt werden, um ein Geheimnis dauerhaft zu löschen. Zuerst muss ein Benutzer das Objekt löschen. Dadurch weist es den Zustand einer vorläufigen Löschung auf. Dann muss ein Benutzer das Objekt im Zustand der vorläufigen Löschung endgültig löschen. Der Vorgang des endgültigen Löschens erfordert zusätzliche Berechtigungen für Zugriffsrichtlinien. Diese zusätzlichen Schutzmaßnahmen verringern das Risiko, dass ein Benutzer versehentlich oder böswillig ein Geheimnis oder einen Schlüsseltresor löscht.  
- Wenn Sie ein vorläufig gelöschtes Geheimnis endgültig löschen möchten, muss einem Dienstprinzipal eine zusätzliche Berechtigung für Zugriffsrichtlinien zum endgültigen Löschen gewährt werden. Diese Berechtigung für Zugriffsrichtlinien für endgültiges Löschen wird Dienstprinzipalen einschließlich Schlüsseltresoren und Besitzern von Abonnements nicht standardmäßig gewährt, sondern muss explizit festgelegt werden. Indem erhöhte Berechtigungen für Zugriffsrichtlinien für das endgültige Löschen eines vorläufig gelöschten Geheimnisses erzwungen werden, kann die Wahrscheinlichkeit einer versehentlichen Löschung eines Geheimnisses reduziert werden.

## <a name="supporting-interfaces"></a>Unterstützende Schnittstellen

Das Feature für vorläufiges Löschen ist über die [REST-API](/rest/api/keyvault/), die [Azure CLI](./key-vault-recovery.md), über [Azure PowerShell](./key-vault-recovery.md) und über die [.NET/C#](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)-Schnittstellen sowie über die [ARM-Vorlagen](/azure/templates/microsoft.keyvault/2019-09-01/vaults) verfügbar.

## <a name="scenarios"></a>Szenarien

Azure-Schlüsseltresore sind nachverfolgte Ressourcen, die von Azure Resource Manager verwaltet werden. Azure Resource Manager gibt auch ein klar definiertes Verhalten für Löschvorgänge an, das erfordert, dass ein erfolgreicher DELETE-Vorgang dazu führt, dass auf die jeweilige Ressource nicht mehr zugegriffen werden kann. Bei der Funktion für vorläufiges Löschen geht es um die Wiederherstellung des gelöschten Objekts, unabhängig davon, ob der Löschvorgang versehentlich oder absichtlich erfolgte.

1. In einem typischen Szenario hat ein Benutzer einen Schlüsseltresor oder ein Key Vault-Objekt versehentlich gelöscht. Wenn der Schlüsseltresor oder das Key Vault-Objekt für einen zuvor festgelegten Zeitraum wiederherstellbar ist, kann der Benutzer den Löschvorgang möglicherweise rückgängig machen und die Daten wiederherstellen.

2. In einem anderen Szenario versucht ein nicht autorisierter Benutzer, einen Schlüsseltresor oder ein Key Vault-Objekt (z. B. einen Schlüssel in einem Tresor) zu löschen, um eine Unterbrechung des Geschäftsbetriebs zu verursachen. Die Trennung der Löschung des Schlüsseltresors oder Key Vault-Objekts vom tatsächlichen Löschen der zugrunde liegenden Daten kann als Sicherheitsmaßnahme verwendet werden, indem z. B. die Berechtigungen für das Löschen von Daten auf eine andere vertrauenswürdige Rolle beschränkt werden. Dieser Ansatz erfordert effektiv ein Quorum für einen Vorgang, der andernfalls zu einem sofortigen Datenverlust führen könnte.

### <a name="soft-delete-behavior"></a>Verhalten des vorläufigen Löschens

Wenn vorläufiges Löschen aktiviert ist, werden Ressourcen, die als gelöscht gekennzeichnet wurden, für einen bestimmten Zeitraum (standardmäßig 90 Tage) aufbewahrt. Der Dienst bietet darüber hinaus einen Mechanismus zur Wiederherstellung des gelöschten Objekts, bei dem der Löschvorgang im Wesentlichen rückgängig gemacht wird.

Beim Erstellen eines neuen Schlüsseltresors ist vorläufiges Löschen standardmäßig aktiviert. Sie können über die [Azure-Befehlszeilenschnittstelle](./key-vault-recovery.md) oder [Azure PowerShell](./key-vault-recovery.md) aber auch einen Schlüsseltresor ohne vorläufiges Löschen erstellen. Wenn vorläufiges Löschen in einem Schlüsseltresor aktiviert ist, kann es nicht mehr deaktiviert werden.

Der Standardaufbewahrungszeitraum beträgt 90 Tage. Über das Azure-Portal kann jedoch während der Erstellung des Schlüsseltresors das Intervall der Aufbewahrungsrichtlinie auf einen Wert zwischen 7 und 90 Tagen festgelegt werden. Die Aufbewahrungsrichtlinie des Bereinigungsschutzes verwendet das gleiche Intervall. Nach der Festlegung kann das Intervall für die Aufbewahrungsrichtlinie nicht mehr geändert werden.

Der Name eines vorläufig gelöschten Schlüsseltresors kann erst nach Ablauf des Aufbewahrungszeitraums erneut verwendet werden.

### <a name="purge-protection"></a>Bereinigungsschutz

Der Bereinigungsschutz ist ein optionales Key Vault-Verhalten und ist standardmäßig **nicht aktiviert**. Der Schutz vor dem endgültigen Löschen kann nur aktiviert werden, wenn das vorläufige Löschen aktiviert ist.  Es kann über die [Befehlszeilenschnittstelle](./key-vault-recovery.md?tabs=azure-cli) oder [PowerShell](./key-vault-recovery.md?tabs=azure-powershell) aktiviert werden.

Bei aktiviertem Bereinigungsschutz kann ein Tresor oder ein Objekt im gelöschten Zustand erst nach Ablauf der Aufbewahrungsdauer endgültig gelöscht werden. Vorläufig gelöschte Tresore und Objekte können unter Einhaltung der Aufbewahrungsrichtlinie weiterhin wiederhergestellt werden.

Der Standardaufbewahrungszeitraum beträgt 90 Tage. Über das Azure-Portal kann das Intervall der Aufbewahrungsrichtlinie jedoch auf einen Wert zwischen sieben und 90 Tagen festgelegt werden. Nachdem das Intervall für die Aufbewahrungsrichtlinie festgelegt und gespeichert wurde, kann es für diesen Tresor nicht mehr geändert werden.

### <a name="permitted-purge"></a>Zulässige Löschung

Das endgültige Löschen eines Schlüsseltresors kann über einen POST-Vorgang für die Proxyressource erfolgen und erfordert spezielle Berechtigungen. Im Allgemeinen kann nur der Besitzer des Abonnements einen Schlüsseltresor endgültig löschen. Der POST-Vorgang löst die sofortige Löschung dieses Tresors aus, die nicht rückgängig gemacht werden kann. 

Es gelten folgende Ausnahmen:
- Wenn das Azure-Abonnement als *nicht löschbar* markiert wurde. In diesem Fall kann der eigentliche Löschvorgang nur vom Dienst ausgeführt werden, und dies erfolgt als geplanter Prozess. 
- Wenn das `--enable-purge-protection flag` im Tresor selbst aktiviert wurde. In diesem Fall wartet Key Vault 90 Tage ab der Markierung des ursprünglichen geheimen Objekts zum Löschen, bevor das Objekt vollständig gelöscht wird.

Informationen zu den erforderlichen Schritten finden Sie unter [Verwenden des vorläufigen Löschens in Key Vault mit der CLI: Bereinigen eines Schlüsseltresors](./key-vault-recovery.md?tabs=azure-cli#key-vault-cli) oder [Verwenden des vorläufigen Löschens in Key Vault mit PowerShell: Bereinigen eines Schlüsseltresors](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell).

### <a name="key-vault-recovery"></a>Wiederherstellung eines Schlüsseltresors

Beim Löschen eines Schlüsseltresors erstellt der Dienst eine Proxyressource unter dem Abonnement, wobei genügend Metadaten für die Wiederherstellung hinzugefügt werden. Die Proxyressource ist ein gespeichertes Objekt, das am gleichen Speicherort wie der gelöschte Schlüsseltresor verfügbar ist. 

### <a name="key-vault-object-recovery"></a>Wiederherstellung eines Key Vault-Objekts

Beim Löschen eines Key Vault-Objekts, beispielsweise eines Schlüssels, versetzt der Dienst das Objekt in einen gelöschten Zustand, sodass keine Abrufvorgänge mehr darauf zugreifen können. Während sich das Key Vault-Objekt in diesem Zustand befindet, kann es nur aufgelistet, wiederhergestellt oder zwangsweise/endgültig gelöscht werden. Verwenden Sie zur Anzeige der Objekte den Azure CLI-Befehl `az keyvault key list-deleted` (weitere Informationen hierzu finden Sie unter [Verwenden des vorläufigen Löschens in Key Vault mit der CLI](./key-vault-recovery.md)) oder den Azure PowerShell-Parameter `-InRemovedState` (weitere Informationen hierzu finden Sie unter [Geheimnisse](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)).  

Gleichzeitig plant Key Vault die Ausführung des Löschens der zugrunde liegenden Daten, die dem gelöschten Schlüsseltresor oder Key Vault-Objekt entsprechen, nach einem zuvor festgelegten Aufbewahrungsintervall. Der dem Tresor entsprechende DNS-Eintrag wird ebenfalls für die Dauer des Aufbewahrungsintervalls beibehalten.

### <a name="soft-delete-retention-period"></a>Aufbewahrungszeitraum für vorläufig gelöschte Ressourcen

Vorläufig gelöschte Ressourcen werden für einen festgelegten Zeitraum von 90 Tagen beibehalten. Während des Aufbewahrungsintervalls für vorläufig gelöschte Ressourcen gilt Folgendes:

- Sie können alle im vorläufig gelöschten Zustand befindlichen Schlüsseltresore und Key Vault-Objekte für Ihr Abonnement auflisten sowie auf die entsprechenden Löschungs- und Wiederherstellungsinformationen zugreifen.
  - Nur Benutzer mit speziellen Berechtigungen können gelöschte Tresore auflisten. Wir empfehlen unseren Benutzern das Erstellen einer benutzerdefinierten Rolle mit diesen speziellen Berechtigungen für den Umgang mit gelöschten Tresoren.
- Es kann kein Schlüsseltresor mit demselben Namen am gleichen Speicherort erstellt werden. Dementsprechend kann auch kein Key Vault-Objekt in einem bestimmten Tresor erstellt werden, wenn dieser Schlüsseltresor ein Objekt mit demselben Namen enthält, das sich in einem gelöschten Zustand befindet.
- Nur ein Benutzer mit speziellen Berechtigungen kann einen Schlüsseltresor oder ein Key Vault-Objekt durch Ausführen eines Wiederherstellungsbefehls für die entsprechende Proxyressource wiederherstellen.
  - Der Benutzer, ein Mitglied der benutzerdefinierten Rolle, der über die Berechtigung zum Erstellen eines Schlüsseltresors unter der Ressourcengruppe verfügt, kann den Tresor wiederherstellen.
- Nur ein Benutzer mit speziellen Berechtigungen kann das Löschen eines Schlüsseltresors oder Key Vault-Objekts durch Ausführen eines Löschbefehls für die entsprechende Proxyressource erzwingen.

Sofern ein Schlüsseltresor oder Key Vault-Objekt nicht wiederhergestellt wird, löscht der Dienst am Ende des Aufbewahrungsintervalls den vorläufig gelöschten Schlüsseltresor oder das vorläufig gelöschte Key Vault-Objekt und dessen Inhalt endgültig. Das Löschen von Ressourcen kann nicht neu geplant werden.

### <a name="billing-implications"></a>Hinweise zur Gebührenberechnung

Im Allgemeinen gilt: Wenn sich ein Objekt (ein Schlüsseltresor oder ein Schlüssel/Geheimnis) im gelöschten Zustand befindet, sind nur zwei Vorgänge möglich: Bereinigen und Wiederherstellen. Alle anderen Vorgänge sind nicht erfolgreich. Das Objekt ist zwar vorhanden, es können jedoch keine Vorgänge dafür ausgeführt werden. Dadurch findet keine Nutzung statt, und es erfolgt auch keine Abrechnung. Es gelten allerdings folgende Ausnahmen:

- Bereinigungs- und Wiederherstellungsaktionen gelten als normale Schlüsseltresorvorgänge und werden entsprechend in Rechnung gestellt.
- Wenn es sich bei dem Objekt um einen HSM-Schlüssel handelt, fällt pro Schlüsselversion und Monat die Gebühr für durch den HSM geschützte Schlüssel an, sofern in den letzten 30 Tagen eine Schlüsselversion verwendet wurde. Danach gilt: Da sich das Objekt im gelöschten Zustand befindet, können keine Vorgänge dafür ausgeführt werden, und es fallen somit auch keine Gebühren an.

## <a name="next-steps"></a>Nächste Schritte

Die folgenden zwei Handbücher stellen die primären Verwendungsszenarien für vorläufiges Löschen vor.

- [Verwenden des vorläufigen Löschens in Key Vault mit PowerShell](./key-vault-recovery.md) 
- [Verwenden des vorläufigen Löschens in Key Vault mit CLI](./key-vault-recovery.md)