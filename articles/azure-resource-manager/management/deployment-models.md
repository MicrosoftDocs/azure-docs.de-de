---
title: Resource Manager-Bereitstellung und klassische Bereitstellung
description: Beschreibt die Unterschiede zwischen dem Ressourcen-Manager-Bereitstellungsmodell und dem klassischen Bereitstellungsmodell (auch als "Dienstverwaltungsmodell" bekannt).
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: da260db84de0cc8bd092eb95491b7cc38df93a12
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185843"
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Azure Resource Manager und klassische Bereitstellung: Grundlegendes zu Bereitstellungsmodellen und zum Status von Ressourcen

> [!NOTE]
> Die in diesem Artikel bereitgestellten Informationen gelten nur, wenn Sie von der klassischen Bereitstellung zur Azure Resource Manager-Bereitstellung migrieren.

In diesem Artikel wird sowohl das Azure Resource Manager-Bereitstellungsmodell als auch das klassische Bereitstellungsmodell erläutert. Das Resource Manager-Bereitstellungsmodell und das klassische Bereitstellungsmodell sind zwei unterschiedliche Methoden zum Bereitstellen und Verwalten Ihrer Azure-Lösungen. Sie nutzen sie unter Verwendung von zwei verschiedenen API-Sätzen, und die bereitgestellten Ressourcen weisen unter Umständen erhebliche Unterschiede auf. Die beiden Modelle sind nicht miteinander kompatibel. In diesem Artikel werden diese Unterschiede beschrieben.

Um die Bereitstellung und Verwaltung der Ressourcen zu vereinfachen, empfiehlt Microsoft die Verwendung von Resource Manager für alle neuen Ressourcen. Darüber hinaus empfiehlt Microsoft nach Möglichkeit die erneute Bereitstellung vorhandener Ressourcen über den Resource Manager.

Falls der Resource Manager neu für Sie ist, ist es ratsam, zuerst die Terminologiedefinitionen unter [Übersicht über Azure Resource Manager](overview.md)durchzulesen.

## <a name="history-of-the-deployment-models"></a>Verlauf der Bereitstellungsmodelle

In Azure war ursprünglich nur das klassische Bereitstellungsmodell verfügbar. In diesem Modell war jede Ressource unabhängig von den anderen vorhanden. Zusammengehörige Ressourcen konnten nicht gruppiert werden. Stattdessen mussten Sie manuell nachverfolgen, aus welchen Ressourcen die Lösung oder Anwendung bestand, und daran denken, sie mit einem koordinierten Ansatz zu verwalten. Zum Bereitstellen einer Lösung mussten Sie entweder jede Ressource einzeln über das Portal oder ein Skript erstellen, mit dem alle Ressourcen in der richtigen Reihenfolge bereitgestellt wurden. Beim Löschen einer Lösung mussten Sie jede Ressource einzeln löschen. Es war nicht einfach möglich, Richtlinien für die Zugriffssteuerung für zusammengehörige Ressourcen anzuwenden und zu aktualisieren. Außerdem gab es keinen Weg, Tags auf Ressourcen anzuwenden, um diese zur Unterstützung der Überwachung Ihrer Ressourcen und der Abrechnungsverwaltung mit Begriffen zu versehen.

Im Jahr 2014 wurden Resource Manager und das Konzept der Ressourcengruppen von Azure eingeführt. Eine Ressourcengruppe ist ein Container für Ressourcen, die über einen gemeinsamen Lebenszyklus verfügen. Das Resource Manager-Bereitstellungsmodell bietet verschiedene Vorteile:

* Sie müssen die Dienste für Ihre Lösung nicht mehr einzeln bearbeiten, sondern können sie als Gruppe bereitstellen, verwalten und überwachen.
* Sie können die Lösung während ihres Lebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden.
* Sie können die Zugriffssteuerung auf alle Ressourcen der Ressourcengruppe anwenden. Diese Richtlinien werden automatisch angewendet, wenn der Ressourcengruppe neue Ressourcen hinzugefügt werden.
* Sie können Tags auf Ressourcen anwenden, um alle Ressourcen in Ihrem Abonnement logisch zu organisieren.
* Sie können JavaScript Object Notation (JSON) verwenden, um die Infrastruktur für Ihre Lösung zu definieren. Die JSON-Datei wird als Resource Manager-Vorlage bezeichnet.
* Sie können die Abhängigkeiten zwischen Ressourcen definieren, sodass diese in der richtigen Reihenfolge bereitgestellt werden.

Nach Einführung des Ressourcen-Managers wurden alle Ressourcen nachträglich zu Standardressourcengruppen hinzugefügt. Wenn Sie jetzt eine Ressource über die klassische Bereitstellung erstellen, wird diese automatisch in einer standardmäßigen Ressourcengruppe für diesen Dienst erstellt, auch wenn Sie während der Bereitstellung diese Ressourcengruppe nicht angegeben haben. Jedoch bedeutet die bloße Tatsache, dass eine Ressource sich in einer Ressourcengruppe befindet, noch nicht, dass diese Ressource in das Resource Manager-Modell konvertiert wurde.

## <a name="understand-support-for-the-models"></a>Grundlagen der Unterstützung von Modellen

Es sind drei Szenarien möglich:

1. Cloud Services bietet keine Unterstützung für das Resource Manager-Bereitstellungsmodell.
2. Virtuelle Computer, Speicherkonten und virtuelle Netzwerke unterstützen sowohl das Resource Manager-Bereitstellungsmodell als auch das klassische Bereitstellungsmodell.
3. Alle anderen Azure-Dienste unterstützen Resource Manager.

Für virtuelle Computer, Speicherkonten und virtuelle Netzwerke gilt: Wenn die Ressource über die klassische Bereitstellung erstellt wurde, müssen Sie diese weiterhin mit klassischen Vorgängen ausführen. Falls der virtuelle Computer, das Speicherkonto oder das virtuelle Netzwerk per Resource Manager-Bereitstellung erstellt wurde, müssen Sie weiterhin Resource Manager-Vorgänge verwenden. Diese Unterscheidung kann verwirrend sein, wenn Ihr Abonnement eine Mischung aus Ressourcen enthält, die mit Resource Manager und mit der klassischen Bereitstellung erstellt wurden. Diese Ressourcenkombination kann zu unerwarteten Ergebnissen führen, da die Ressourcen nicht die gleichen Vorgänge unterstützen.

In einigen Fällen können Sie mithilfe eines Resource Manager-Befehls Informationen zu einer mit der klassischen Bereitstellung erstellten Ressource abrufen oder eine administrative Aufgabe ausführen, z.B. eine klassische Ressource in eine andere Ressourcengruppe verschieben. Dies sollte jedoch nicht zu der Annahme verleiten, dass der entsprechende Ressourcentyp Resource Manager-Vorgänge unterstützt. Nehmen Sie beispielsweise an, Sie verfügen über eine Ressourcengruppe, die einen mit dem klassischen Bereitstellungsmodell erstellten virtuellen Computer umfasst. Bei Ausführung des folgenden Resource Manager-PowerShell-Befehls:

```powershell
Get-AzResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

Der virtuelle Computer wird zurückgegeben:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

Das Resource Manager-Cmdlet **Get-AzVM** gibt aber nur per Resource Manager bereitgestellte virtuelle Computer zurück. Mit dem folgenden Befehl wird der über das klassische Bereitstellungsmodell erstellte virtuelle Computer nicht zurückgegeben.

```powershell
Get-AzVM -ResourceGroupName ExampleGroup
```

Nur Ressourcen, die mit dem Ressourcen-Manager erstellt wurden, unterstützen Tags. Sie können keine Tags auf klassische Ressourcen anwenden.

## <a name="changes-for-compute-network-and-storage"></a>Änderungen für Compute-, Netzwerk- und Speicherressourcen

Das folgende Diagramm zeigt die Compute-, Netzwerk- und Speicherressourcen, die über Resource Manager bereitgestellt werden.

![Resource Manager – Architektur](./media/deployment-models/arm_arch3.png)

SRP: Speicherressourcenanbieter, CRP: Anbieter von Computerressourcen, NRP: Anbieter von Netzwerkressourcen

Beachten Sie die folgenden Beziehungen zwischen den Ressourcen:

* Alle Ressourcen sind innerhalb einer Ressourcengruppe vorhanden.
* Der virtuelle Computer hängt von einem bestimmten Speicherkonto ab, das im Speicherressourcenanbieter definiert ist, um die Datenträger im Blobspeicher zu speichern (erforderlich).
* Der virtuelle Computer verweist auf eine bestimmte Netzwerkschnittstellenkarte, die im Netzwerkressourcenanbieter definiert ist (erforderlich), und eine Verfügbarkeitsgruppe, die im Compute-Ressourcenanbieter definiert ist (optional).
* Die Netzwerkschnittstellenkarte verweist auf die dem virtuellen Computer zugewiesene IP-Adresse (erforderlich), das Subnetz des virtuellen Netzwerks für den virtuellen Computer (erforderlich) und eine Netzwerksicherheitsgruppe (optional).
* Das Subnetz innerhalb eines virtuellen Netzwerks verweist auf eine Netzwerksicherheitsgruppe (optional).
* Die Lastenausgleichsinstanz verweist auf den Back-End-Pool von IP-Adressen, die die Netzwerkschnittstellenkarte eines virtuellen Computers enthalten (optional) und auf eine öffentliche oder private IP-Adresse für den Lastenausgleich (optional).

Im Folgenden sind die Komponenten und ihre Beziehungen für die klassische Bereitstellung dargestellt:

![Klassische Architektur](./media/deployment-models/arm_arch1.png)

Komponenten der klassischen Lösung zum Hosten eines virtuellen Computers:

* Ein erforderlicher Clouddienst, der als Container für das Hosten virtueller Computer (Compute) fungiert. Virtuelle Computer werden automatisch mit einer Netzwerkschnittstellenkarte bereitgestellt, und ihnen wird von Azure eine IP-Adresse zugewiesen. Darüber hinaus enthält der Clouddienst eine externe Lastenausgleichsinstanz, eine öffentliche IP-Adresse und Standardendpunkte, um Remotedesktop- und Remote-PowerShell-Datenverkehr für die Windows-basierten virtuellen Computer und Secure Shell-Datenverkehr (SSH) für Linux-basierte virtuelle Computer zu ermöglichen.
* Ein erforderliches Speicherkonto, in dem die virtuellen Festplatten für einen virtuellen Computer gespeichert werden, einschließlich des Betriebssystems sowie temporärer und zusätzlicher Datenträger (Speicher).
* Ein optionales virtuelles Netzwerk, das als zusätzlicher Container fungiert, in dem Sie eine Subnetzstruktur erstellen und das Subnetz auswählen können, in dem sich der virtuelle Computer befindet (Netzwerk).

In der folgenden Tabelle werden die Änderungen in der Interaktion von Compute-, Netzwerk- und Speicherressourcenanbietern beschrieben:

| Element | Klassisch | Ressourcen-Manager |
| --- | --- | --- |
| Clouddienst für virtuelle Computer |Beim Clouddienst handelte es sich um einen Container für die virtuellen Computer, für den Plattformverfügbarkeit sowie Lastenausgleich erforderlich waren. |Der Clouddienst ist kein erforderliches Objekt zum Erstellen eines virtuellen Computers mithilfe des neuen Modells mehr. |
| Virtuelle Netzwerke |Ein virtuelles Netzwerk ist optional für den virtuellen Computer. Ist eines vorhanden, kann das virtuelle Netzwerk nicht mit Resource Manager bereitgestellt werden. |Der virtuelle Computer benötigt ein virtuelles Netzwerk, das mit Resource Manager bereitgestellt wurde. |
| Speicherkonten |Der virtuelle Computer erfordert ein Speicherkonto, in dem die virtuellen Festplatten für das Betriebssystem sowie temporäre und zusätzliche Datenträger gespeichert werden. |Der virtuelle Computer benötigt ein Speicherkonto zum Speichern der Datenträger im Blobspeicher. |
| Verfügbarkeitsgruppen |Die Plattformverfügbarkeit wurde durch das Konfigurieren des gleichen „AvailabilitySetName“ auf den virtuellen Computern angezeigt. Die maximale Anzahl von Fehlerdomänen betrug 2. |Verfügbarkeitsgruppen sind Ressourcen, die vom Microsoft.Compute-Anbieter bereitgestellt werden. Virtuelle Computer, für die Hochverfügbarkeit erforderlich ist, müssen in der Verfügbarkeitsgruppe enthalten sein. Die maximale Anzahl von Fehlerdomänen beträgt nun 3. |
| Affinitätsgruppen |Zum Erstellen von virtuellen Netzwerken waren Affinitätsgruppen erforderlich. Dies ist jedoch seit der Einführung regionaler virtueller Netzwerke nicht mehr erforderlich. |Zur Vereinfachung ist das Affinitätsgruppenkonzept in über den Azure-Ressourcen-Manager verfügbaren APIs nicht vorhanden. |
| Lastenausgleich |Durch das Erstellen von Clouddiensten ist ein impliziter Lastenausgleich für die bereitgestellten virtuellen Computer verfügbar. |Der Lastenausgleich ist eine Ressource, die vom Microsoft.Network-Anbieter bereitgestellt wird. Die primäre Netzwerkschnittstelle der virtuellen Computer, die mit einem Lastenausgleich versehen werden soll, muss auf das Lastenausgleichsmodul verweisen. Lastenausgleichsmodule können intern oder extern sein. Eine Lastenausgleichsinstanz verweist auf den Back-End-Pool von IP-Adressen, die die NIC eines virtuellen Computers enthalten (optional) und auf eine öffentliche oder private IP-Adresse für den Lastenausgleich (optional). |
| Virtuelle IP-Adresse |Cloud Services erhält eine Standard-VIP (virtuelle IP-Adresse), wenn einem Clouddienst ein virtueller Computer hinzugefügt wird. Die virtuelle IP-Adresse ist dem impliziten Lastenausgleich zugeordnet. |Die öffentliche IP-Adresse ist eine Ressource, die vom Microsoft.Network-Anbieter bereitgestellt wird. Die öffentliche IP-Adresse kann statisch (reserviert) oder dynamisch sein. Dynamische öffentliche IP-Adressen können einem Lastenausgleich zugewiesen werden. Öffentliche IP-Adressen können mithilfe von Sicherheitsgruppen gesichert werden. |
| Reservierte IP-Adresse |Sie können eine IP-Adresse in Azure reservieren und einem Clouddienst zuordnen, um die Persistenz der IP-Adresse sicherzustellen. |Die öffentliche IP-Adresse kann im statischen Modus erstellt werden. Sie bietet die gleiche Funktionalität wie eine reservierte IP-Adresse. |
| Öffentliche IP-Adresse (PIP) pro virtuellem Computer |Öffentliche IP-Adressen können virtuellen Computern auch direkt zugeordnet werden. |Die öffentliche IP-Adresse ist eine Ressource, die vom Microsoft.Network-Anbieter bereitgestellt wird. Die öffentliche IP-Adresse kann statisch (reserviert) oder dynamisch sein. |
| Endpunkte |Eingabeendpunkte mussten auf virtuellen Computern konfiguriert werden, um Konnektivität für bestimmte Ports zu ermöglichen. Zu den häufigen Verbindungsmodi mit virtuellen Computern gehört das Einrichten von Eingabeendpunkten. |Es können auch eingehende NAT-Regeln auf Lastenausgleichsmodulen konfiguriert werden, um Endpunkte auf bestimmten Ports zum Herstellen einer Verbindung mit den virtuellen Computern zu aktivieren. |
| DNS-Name |Clouddienste erhielten implizite global eindeutige DNS-Namen. Beispiel: `mycoffeeshop.cloudapp.net`. |DNS-Namen sind optionale Parameter, die auf öffentlichen IP-Adressressourcen angegeben werden können. Der FQDN weist das folgende Format auf: `<domainlabel>.<region>.cloudapp.azure.com`. |
| Netzwerkschnittstellen |Primäre und sekundäre Netzwerkschnittstellen und deren Eigenschaften wurden als Netzwerkkonfiguration eines virtuellen Computers definiert. |Die Netzwerkschnittstelle ist eine Ressource, die vom Microsoft.Network-Anbieter bereitgestellt wird. Der Lebenszyklus von Netzwerkschnittstellen ist nicht an virtuelle Computer gebunden. Die Netzwerkschnittstelle verweist auf die dem virtuellen Computer zugewiesene IP-Adresse (erforderlich), das Subnetz des virtuellen Netzwerks für den virtuellen Computer (erforderlich) und eine Netzwerksicherheitsgruppe (optional). |

Informationen zum Verbinden virtueller Netzwerke aus verschiedenen Bereitstellungsmodellen finden Sie unter [Verbinden von virtuellen Netzwerken aus unterschiedlichen Bereitstellungsmodellen im Portal](../../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrieren von klassischen Bereitstellungen zu Resource Manager-Bereitstellungen

Wenn Sie bereit zum Migrieren Ihrer Ressourcen von der klassischen Bereitstellung zur Resource Manager-Bereitstellung sind, helfen Ihnen die folgenden Informationen weiter:

1. [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-deep-dive.md)
2. [Platform supported migration of IaaS resources from Classic to Azure Resource Manager (Plattformgestützte Migration von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager)](../../virtual-machines/migration-classic-resource-manager-overview.md)
3. [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von Azure PowerShell](../../virtual-machines/migration-classic-resource-manager-ps.md)
4. [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe der Azure-Befehlszeilenschnittstelle](../../virtual-machines/migration-classic-resource-manager-cli.md)

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Kann ich mit Resource Manager einen virtuellen Computer erstellen, der in einem virtuellen Netzwerk bereitgestellt wird, das mithilfe der klassischen Bereitstellung erstellt wurde?**

Diese Konfiguration wird nicht unterstützt. Sie können Resource Manager nicht zum Bereitstellen eines virtuellen Computers in einem virtuellen Netzwerk verwenden, das mithilfe des klassischen Bereitstellungsmodells erstellt wurde.

**Kann ich mit Resource Manager einen virtuellen Computer aus einem Benutzerimage erstellen, das mithilfe des klassischen Bereitstellungsmodells erstellt wurde?**

Diese Konfiguration wird nicht unterstützt. Sie können jedoch die virtuellen Festplattendateien aus einem Speicherkonto kopieren, das mithilfe des klassischen Bereitstellungsmodells erstellt wurde, und einem neuen Konto hinzufügen, das mit Resource Manager erstellt wurde.

**Welche Auswirkungen gibt es auf das Kontingent meines Abonnements?**

Die Kontingente für virtuelle Computer, virtuelle Netzwerke und Speicherkonten, die mit Azure Resource Manager erstellt wurden, und Ihre anderen Kontingente sind separat. Jedes Abonnement erhält Kontingente, um die Ressourcen mit den neuen APIs zu erstellen. Weitere Informationen zu den zusätzlichen Kontingenten finden Sie [hier](../../azure-resource-manager/management/azure-subscription-service-limits.md).

**Kann ich meine automatisierten Skripts zur Bereitstellung virtueller Computer, virtueller Netzwerke und Speicherkonten über die Resource Manager-APIs weiterhin verwenden?**

Alle Automatisierungen und Skripts, die Sie erstellt haben, bleiben für die vorhandenen virtuellen Computer und virtuellen Netzwerke funktionsfähig, die im Azure-Dienstverwaltungsmodus erstellt wurden. Die Skripts müssen jedoch aktualisiert werden, damit das neue Schema zum Erstellen der gleichen Ressourcen über den Resource Manager-Modus verwendet werden kann.

**Wo finde ich Beispiele für Vorlagen für den Azure-Ressourcen-Manager?**

Einen umfassenden Satz von Startervorlagen finden Sie unter [Schnellstartvorlagen für den Azure Resource-Manager](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Nächste Schritte

* Die Befehle zum Bereitstellen einer Vorlage finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](../templates/deploy-powershell.md).