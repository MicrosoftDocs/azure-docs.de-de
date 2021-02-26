---
title: Automatische Azure-Verwaltung für virtuelle Computer
description: Erfahren Sie mehr über die automatische Azure-Verwaltung für virtuelle Computer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 7772d57937393da1c48fa2658818d8a1a2b28a1f
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550783"
---
# <a name="azure-automanage-for-virtual-machines"></a>Automatische Azure-Verwaltung für virtuelle Computer

Dieser Artikel enthält Informationen zur automatischen Azure-Verwaltung für virtuelle Computer, die folgende Vorteile bieten:

- Das Onboarding virtueller Computer zur Auswahl bewährter Azure-Dienste wird intelligent durchgeführt
- Jeder Dienst wird automatisch nach den bewährten Methoden von Azure konfiguriert
- Es erfolgt eine Überwachung auf Datendrift, der behoben wird, wenn er erkannt wird
- Bietet eine einfache Benutzererfahrung (zeigen, klicken, festlegen, vergessen)


## <a name="overview"></a>Übersicht

Die automatische Azure-Verwaltung für virtuelle Computer ist ein Dienst, der die Anforderung beseitigt, bestimmte Dienste in Azure zu entdecken, zu integrieren und zu konfigurieren, die Ihrem virtuellen Computer zugute kommen würden. Diese Dienste tragen zur Verbesserung der Zuverlässigkeit, Sicherheit und Verwaltung virtueller Computer bei und gelten als bewährte Azure-Dienste, z. B. [Azure-Updateverwaltung](../automation/update-management/overview.md) und [Azure Backup](../backup/backup-overview.md), um nur einige zu nennen.

Nachdem Sie das Onboarding Ihrer virtuellen Computer für die automatische Azure-Verwaltung durchgeführt haben, konfiguriert diese automatisch jeden bewährten Dienst mit den empfohlenen Einstellungen. Die bewährten Methoden sind für jeden Dienst unterschiedlich. Ein Beispiel hierfür könnte Azure Backup sein, bei dem die bewährte Methode darin bestehen könnte, den virtuellen Computer einmal täglich zu sichern und einen Aufbewahrungszeitraum von sechs Monaten einzuhalten.

Die automatische Azure-Verwaltung überwacht auch automatisch den Datendrift und korrigiert ihn, wenn er erkannt wird. Das bedeutet, wenn für Ihren virtuellen Computer das Onboarding in die automatische Azure-Verwaltung durchgeführt wird, konfigurieren wir ihn nicht nur nach den bewährten Azure-Methoden, sondern wir überwachen Ihren Computer, um sicherzustellen, dass er diese bewährten Methoden während seines gesamten Lebenszyklus einhält. Wenn Ihr virtueller Computer von diesen Methoden abweicht oder ein Datendrift auftritt, korrigieren wir dies und bringen Ihren Computer wieder in den gewünschten Zustand.

Und schließlich ist die Benutzererfahrung unglaublich einfach.


## <a name="prerequisites"></a>Voraussetzungen

Es gibt mehrere Voraussetzungen zu beachten, bevor Sie versuchen, die automatische Azure-Verwaltung auf Ihren virtuellen Computern zu aktivieren.

- Nur Windows Server-VMs
- VMs müssen sich in einer unterstützten Region befinden (siehe folgender Absatz)
- Benutzer muss über die richtigen (im folgenden Absatz beschriebenen) Berechtigungen verfügen.
- Automatische Verwaltung unterstützt zurzeit keine Sandboxabonnements.

Es ist auch wichtig zu beachten, dass die automatische Verwaltung nur Windows-VMs unterstützt, die sich in den folgenden Regionen befinden: „Europa, Westen“, „USA, Osten“, „USA, Westen 2“, „Kanada, Mitte“, „USA, Westen-Mitte“, „Japan, Osten“.

Sie müssen über die Rolle **Mitwirkender** für die Ressourcengruppe verfügen, die Ihre VMs enthält, um Automanage auf virtuellen Computern mit einem vorhandenen Automanage-Konto zu aktivieren. Wenn Sie Automanage mit einem neuen Automanage-Konto aktivieren, benötigen Sie die folgenden Berechtigungen in Ihrem Abonnement: Rolle **Besitzer** oder **Mitwirkender** sowie Rollen vom Typ **Benutzerzugriffsadministrator**

> [!NOTE]
> Wenn Sie Automanage auf einem virtuellen Computer verwenden möchten, der mit einem Arbeitsbereich in einem anderen Abonnement verbunden ist, müssen Sie in jedem Abonnement über die oben beschriebenen Berechtigungen verfügen.

## <a name="participating-services"></a>Beteiligte Dienste

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Intelligentes Durchführen des Onboardings von Diensten.":::

Die vollständige Liste der teilnehmenden Azure-Dienste sowie die unterstützten Konfigurationsprofile finden Sie unter [Bewährte Methoden für die Automatische Azure-Verwaltung von virtuellen Computern](virtual-machines-best-practices.md).

 Wir werden mit Ihnen automatisch das Onboarding für diese teilnehmenden Dienste durchführen. Sie sind für unser Whitepaper über bewährte Methoden unerlässlich, das Sie in unserem [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management) finden.

Bei all diesen Diensten werden wir das Onboarding automatisch durchführen, sie automatisch konfigurieren, auf Datendrift überwachen und schlichten, wenn ein Datendrift festgestellt wird.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Aktivieren der automatischen Verwaltung für VMs im Azure-Portal

Im Azure-Portal können Sie die automatische Verwaltung auf einem vorhandenen virtuellen Computer oder beim Erstellen eines neuen virtuellen Computers aktivieren. Eine kurze Anleitung zu diesem Prozess finden Sie unter [Schnellstart zur automatischen Verwaltung für virtuelle Computer](quick-create-virtual-machines-portal.md).

Wenn Sie zum ersten Mal die automatische Verwaltung für Ihre VM aktivieren, können Sie im Azure-Portal nach **Automatische Verwaltung – Bewährte Methoden für Azure-VMs** suchen. Klicken Sie auf **Für vorhandene VM aktivieren**, wählen Sie die VMs aus, für die Sie das Onboarding durchführen möchten, klicken Sie auf **Auswählen** und dann auf **Aktivieren**, dann sind Sie fertig.

Die einzige Zeit, in der Sie möglicherweise mit dieser VM interagieren müssen, um diese Dienste zu verwalten, ist für den Fall, dass wir versucht haben, Ihre VM zu reparieren, dies jedoch nicht gelungen ist. Wenn wir Ihre VM erfolgreich korrigieren, stellen wir die Compliance wieder her, ohne Sie überhaupt zu warnen.


## <a name="configuration-profiles"></a>Konfigurationsprofile

Wenn Sie die automatische Verwaltung für Ihren virtuellen Computer aktivieren, ist ein Konfigurationsprofil erforderlich. Konfigurationsprofile sind die Grundlage dieses Diensts. Sie legen genau fest, für welche Dienste wir das Onboarding für Ihre Computer durchführen. Sie legen zu einem gewissen Grad ebenfalls fest, wie diese Dienste konfiguriert werden sollen.

### <a name="default-configuration-profiles"></a>Standardkonfigurationsprofile

Es sind derzeit zwei Konfigurationsprofile verfügbar.

- **Bewährte Methoden für virtuelle Azure-Computer – Dev/Test**: Das Konfigurationsprofil ist für Dev/Test-Computer konzipiert.
- **Bewährte Methoden für virtuelle Azure-Computer – Produktion**: Das Konfigurationsprofil ist für die Produktion konzipiert.

Der Grund für diese Differenzierung liegt darin, dass bestimmte Dienste auf der Grundlage der aktiven Workload empfohlen werden. Auf einem Computer in der Produktion werden wir für Sie z. B. automatisch das Onboarding für Azure Backup durchführen. Für einen Dev/Test-Computer wäre ein Sicherungsdienst jedoch ein unnötiger Kostenfaktor, da Dev/Test-Computer in der Regel geringere geschäftliche Auswirkungen haben.

### <a name="customizing-a-configuration-profile-using-preferences"></a>Anpassen eines Konfigurationsprofils mithilfe von Voreinstellungen

Zusätzlich zu den Standarddiensten, für die wir das Onboarding für Sie durchführen, ermöglichen wir Ihnen die Konfiguration einer bestimmten Teilmenge von Voreinstellungen. Diese Voreinstellungen sind innerhalb einer Reihe von Konfigurationsoptionen zulässig, die nicht gegen unsere bewährten Methoden verstoßen. Im Falle von Azure Backup gestatten wir Ihnen z. B., die Häufigkeit der Sicherung zu definieren und festzulegen, an welchem Wochentag sie erfolgt. Wir werden Ihnen jedoch *nicht* gestatten, Azure Backup vollständig zu deaktivieren

> [!NOTE]
> Im Dev/Test-Konfigurationsprofil werden wir die VM überhaupt nicht sichern.

Sie können die Einstellungen eines Standardkonfigurationsprofils über Voreinstellungen anpassen. Erfahren Sie [hier](virtual-machines-custom-preferences.md), wie Sie eine Voreinstellung erstellen.

> [!NOTE]
> Sie können das Konfigurationsprofil auf Ihrer VM nicht ändern, solange die automatische Verwaltung aktiviert ist. Sie müssen die automatische Verwaltung für diese VM deaktivieren und dann die automatische Verwaltung mit dem gewünschten Konfigurationsprofil und den gewünschten Voreinstellungen erneut aktivieren.


## <a name="automanage-account"></a>Konto für die automatische Verwaltung

Das Konto für die automatische Verwaltung ist der Sicherheitskontext oder die Identität, unter der die automatisierten Vorgänge auftreten. Normalerweise müssen Sie die Automanage-Kontooption nicht auswählen, aber wenn Sie in einem Delegierungsszenario die automatische Verwaltung Ihrer Ressourcen aufteilen möchten (vielleicht zwischen zwei Systemadministratoren), können Sie mit dieser Option für jeden dieser Administratoren eine Azure-Identität definieren.

Wenn Sie in der Azure-Portalerfahrung die automatische Verwaltung auf Ihren VMs aktivieren, gibt es eine Dropdownliste „Erweitert“ auf dem Blatt **Bewährte Methode zum Aktivieren einer Azure-VM**, mit der Sie das Konto für die automatische Verwaltung zuweisen oder manuell erstellen können.

Dem Automanage-Konto wird sowohl die Rolle **Mitwirkender** als auch **Mitwirkender bei Ressourcenrichtlinien** zu den Abonnements gewährt, die die Computer enthalten, die Sie in Automanage integrieren. Sie können das gleiche Automanage-Konto über mehrere Abonnements hinweg auf Computern verwenden. Dadurch werden diesem Automanage-Konto die Rollen **Mitwirkender** und **Mitwirkender bei Ressourcenrichtlinien** für alle Abonnements erteilt.

Wenn Ihr virtueller Computer mit einem Log Analytics-Arbeitsbereich in einem anderen Abonnement verbunden ist, wird dem Automanage-Konto sowohl die Rolle **Mitwirkender** als auch **Mitwirkender bei Ressourcenrichtlinien** in diesem anderen Abonnement gewährt.

Wenn Sie Automanage mit einem neuen Automanage-Konto aktivieren, benötigen Sie die folgenden Berechtigungen in Ihrem Abonnement: Rolle **Besitzer** oder **Mitwirkender** sowie Rollen vom Typ **Benutzerzugriffsadministrator**

Wenn Sie Automanage mit einem vorhandenen Automanage-Konto aktivieren, müssen Sie über die Rolle **Mitwirkender** für die Ressourcengruppe verfügen, die Ihre virtuellen Computer enthält.

> [!NOTE]
> Wenn Sie bewährte Methoden für Automanage deaktivieren, bleiben die Berechtigungen des Automanage-Kontos für alle zugeordneten Abonnements erhalten. Entfernen Sie die Berechtigungen manuell, indem Sie die IAM-Seite des Abonnements aufrufen oder das Automanage-Konto löschen. Das Automanage-Konto kann nicht gelöscht werden, wenn es noch Computer verwaltet.


## <a name="status-of-vms"></a>Status von VMs

Navigieren Sie im Azure-Portal zur Seite **Automatische Verwaltung – Bewährte Methoden für Azure-VMs**, auf der alle automatisch verwalteten virtuellen Computer aufgeführt sind. Hier wird der Gesamtstatus der einzelnen virtuellen Computer angezeigt.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Liste der konfigurierten virtuellen Computer":::

Für jede aufgelistete VM werden die folgenden Details angezeigt: Name, Konfigurationsprofil, Konfigurationsvoreinstellung, Status, Konto, Abonnement und Ressourcengruppe.

Die Spalte **Status** kann die folgenden Zustände anzeigen:
- *In Bearbeitung* – Die VM wurde gerade aktiviert und wird konfiguriert
- *Konfiguriert* – Die VM ist konfiguriert und es wurde kein Datendrift erkannt
- *Fehler* – Die VM weist einen Datendrift auf und wir konnten das Problem nicht beheben
- *Ausstehend*: Der virtuelle Computer wird zurzeit nicht ausgeführt, und bei der nächsten Ausführung des virtuellen Computers wird Automanage versuchen, die VM zu integrieren oder zu korrigieren

Wenn der **Status** *Fehler* angezeigt wird, können Sie die Bereitstellung über die Ressourcengruppe beheben, in der sich Ihre VM befindet. Wechseln Sie zu **Ressourcengruppen**, wählen Sie Ihre Ressourcengruppe aus, klicken Sie auf **Bereitstellungen**, und beachten Sie dort den Status *Fehler* zusammen mit Fehlerdetails.


## <a name="disabling-automanage-for-vms"></a>Deaktivieren der automatischen Verwaltung für VMs

Möglicherweise entscheiden Sie sich eines Tages dafür, die automatische Verwaltung für bestimmte VMs zu deaktivieren. Auf Ihrem Computer wird z. B. eine hochgradig vertrauliche, sichere Workload ausgeführt, und Sie müssen sie noch weiter sperren, als es Azure von sich aus vorgenommen hätte, sodass Sie den Computer außerhalb der bewährten Methoden von Azure konfigurieren müssen.

Navigieren Sie dazu im Azure-Portal zur Seite **Automatische Verwaltung – Bewährte Methoden für Azure-VMs**, auf der alle automatisch verwalteten virtuellen Computer aufgeführt sind. Aktivieren Sie das Kontrollkästchen neben dem virtuellen Computer, den Sie über die automatische Verwaltung deaktivieren möchten, und klicken Sie dann auf die Schaltfläche zum **Deaktivieren der automatischen Verwaltung**.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Deaktivieren der automatischen Verwaltung auf einem virtuellen Computer":::

Lesen Sie die Meldung im angezeigten Popupelement sorgfältig durch, bevor Sie dem **Deaktivieren** zustimmen.

> [!NOTE]
> Das Deaktivieren der automatischen Verwaltung für eine VM führt zu folgendem Verhalten:
>
> - Die Konfiguration der VM und der Dienste, für die Onboarding durchgeführt wird, ändert sich nicht.
> - Sämtliche Gebühren, die durch diese Dienste anfallen, bleiben abrechenbar und fallen weiterhin an.
> - Das gesamte Verhalten der automatischen Verwaltung wird sofort beendet.


In erster Linie werden wir den virtuellen Computer nicht von einem der Dienste trennen, auf dem wir das Onboarding für ihn durchgeführt und ihn konfiguriert haben. Somit bleiben alle durch diese Dienste entstandenen Kosten weiterhin abrechenbar. Sie müssen ggf. das Onboarding aufheben. Jegliches Verhalten der automatischen Verwaltung wird sofort beendet. Wir werden z. B. die VM nicht mehr auf Datendrift überwachen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, dass die automatische Verwaltung für virtuelle Computer ein Mittel darstellt, mit dem Sie die Kenntnis, das Onboarding und die Konfiguration bewährter Methoden von Azure-Diensten beseitigen können. Wenn ein Computer, für den Sie bei der automatischen Verwaltung für virtuelle Computer Onboarding durchgeführt haben, von den eingerichteten Konfigurationsprofilen abweicht, sorgen wir automatisch wieder für seine Compliance.

Versuchen Sie, die automatische Verwaltung für virtuelle Computer im Azure-Portal zu aktivieren.

> [!div class="nextstepaction"]
> [Aktivieren der automatischen Verwaltung für virtuelle Computer im Azure-Portal](quick-create-virtual-machines-portal.md)