---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 4f59f68c1598f737ea7cb3a0e8046fc0779ed9d3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113805"
---
Der Azure-Dateisynchronisierungs-Agent wird regelmäßig aktualisiert, um neue Funktionalität hinzuzufügen und Probleme zu beheben. Es wird empfohlen, Microsoft Update zu konfigurieren, um Updates für den Azure-Dateisynchronisierungs-Agent zu erhalten, wenn diese verfügbar sind.

#### <a name="major-vs-minor-agent-versions"></a>Unterschiede zwischen Haupt- und Nebenversionen des Agents
* Hauptversionen des Agents enthalten oft neue Features und haben eine aufsteigende Zahl als ersten Teil der Versionsnummer. Beispiel:  \*2.\*.\*\*
* Nebenversionen des Agents werden auch als „Patches“ bezeichnet und werden häufiger veröffentlicht als Hauptversionen. Sie enthalten oft Fehlerbehebungen und kleinere Verbesserungen, aber keine neuen Features. Beispiel: \*\*.3.\*\*

#### <a name="upgrade-paths"></a>Upgradepfade
Es gibt vier bewährte und getestete Wege, um die Updates des Azure-Dateisynchronisierungs-Agents zu installieren. 
1. **(Bevorzugt) Konfigurieren Sie Microsoft Update zum automatischen Herunterladen und Installieren von Agent-Updates.**  
    Es wird stets empfohlen, jedes Azure-Dateisynchronisierungsupdate auszuführen, um sicherzustellen, dass Sie Zugriff auf die neuesten Fehlerbehebungen für den Server-Agent haben. Mit Microsoft Update ist dies ein nahtloser Prozess, da Updates automatisch für Sie heruntergeladen und installiert werden.
2. **Verwenden von AfsUpdater.exe zum Herunterladen und Installieren von Agent-Updates.**  
    Die AfsUpdater.exe befindet sich im Installationsverzeichnis für den Agent. Doppelklicken Sie auf die ausführbare Datei, um Agent-Updates herunterzuladen und zu installieren. 
3. **Patchen Sie einen vorhandenen Azure-Dateisynchronisierungs-Agent mithilfe einer Microsoft Update-Patchdatei oder einer ausführbaren MSP-Datei. Das neueste Updatepaket für die Azure-Dateisynchronisierung kann aus dem [Microsoft Update-Katalog](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync) heruntergeladen werden.**  
    Wenn Sie eine ausführbare MSP-Datei verwenden, wird Ihre Installation der Azure-Dateisynchronisierung mit der gleichen Methode aktualisiert, die auch von Microsoft Update automatisch im vorherigen Upgradepfad verwendet wird. Das Anwenden eines Microsoft Update-Patches führt ein direktes Upgrade einer Azure-Dateisynchronisierungsinstallation aus.
4. **Laden Sie den neuesten Azure-Dateisynchronisierungs-Agent aus dem [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) herunter.**  
    Um eine bestehende Installation des Azure-Dateisynchronisierungs-Agents zu aktualisieren, deinstallieren Sie die ältere Version und installieren Sie dann die neueste Version mit dem heruntergeladenen Installationsprogramm. Die Serverregistrierung, Synchronisierungsgruppen und alle anderen Einstellungen werden vom Installationsprogramm für die Azure-Dateisynchronisierung beibehalten.

#### <a name="automatic-agent-lifecycle-management"></a>Automatische Agent-Lebenszyklusverwaltung
Mit Agent-Version 6 wurde vom Team für die Dateisynchronisierung eine Funktion für automatische Upgrades für den Agent eingeführt. Sie können einen von zwei Modi auswählen und ein Wartungsfenster angeben, in dem das Upgrade auf dem Server durchgeführt werden soll. Diese Funktion soll Sie bei der Agent-Lebenszyklusverwaltung unterstützen, indem entweder verhindert wird, dass der Agent abläuft, oder eine problemlose aktuelle Einstellung ermöglicht wird.
1. Mit der **Standardeinstellung** wird versucht zu verhindern, dass der Agent abläuft. Innerhalb von 21 Tagen vor dem angegebenen Ablaufdatum des Agents versucht der Agent, ein Selbstupgrade durchzuführen. Er versucht, das Upgrade einmal pro Woche innerhalb von 21 Tagen vor dem Ablauf und im ausgewählten Wartungsfenster durchzuführen. **Unabhängig von dieser Option müssen weiterhin reguläre Microsoft Update-Patches durchgeführt werden.**
2. Optional können Sie auswählen, dass der Agent automatisch ein Selbstupgrade durchführt, wenn eine neue Agent-Version verfügbar ist. Dies erfolgt auch während des ausgewählten Wartungsfensters, sodass neue Funktionen und Verbesserungen auf dem Server genutzt werden können, sobald sie allgemein verfügbar sind. Dies ist die empfohlene Einstellung, über die Agent-Hauptversionen sowie reguläre Updatepatches auf dem Server bereitgestellt werden.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Garantien zu Agent-Lebenszyklus und Änderungsverwaltung
Die Azure-Dateisynchronisierung ist ein Clouddienst, über den neue Funktionen und Verbesserungen kontinuierlich eingeführt werden. Dies bedeutet, dass eine bestimmte Version des Azure-Dateisynchronisierungs-Agents nur für eine begrenzte Zeit unterstützt werden kann. Für eine einfachere Bereitstellung gelten die folgenden Regeln, damit sichergestellt ist, dass Sie genügend Zeit haben und benachrichtigt werden, um Updates und Upgrades von Agents in Ihrem Änderungsverwaltungsprozess zu berücksichtigen:

- Die Hauptversionen des Agents werden für mindestens sechs Monate ab dem Datum der Erstveröffentlichung unterstützt.
- Eine Überlappung von mindestens drei Monaten bei der Unterstützung der Agent-Hauptversionen wird garantiert. 
- Warnungen werden für registrierte Server ausgegeben, die einen bald ablaufenden Agent mindestens drei Monate vor Ablauf verwenden. Ob ein registrierter Server eine ältere Version des Agents verwendet, können Sie im Abschnitt „Registrierte Server“ eines Speichersynchronisierungsdiensts überprüfen.
- Die Lebensdauer einer Nebenversion des Agents hängt von der zugehörigen Hauptversion ab. Wenn beispielsweise die Agent-Version 3.0 freigegeben wird, werden alle Agent-Versionen 2.\* gleichzeitig ablaufen.

> [!Note]
> Bei der Installation einer Agent-Version mit einer Ablaufmeldung wird zwar eine Warnung angezeigt, sie ist aber dennoch erfolgreich. Der Versuch einer Installation oder Verbindung mit einer abgelaufenen Agent-Version wird nicht unterstützt und wird daher blockiert.
