---
title: 'Häufig gestellte Fragen zu Azure Security Center: Fragen zu virtuellen Computern'
description: Häufig gestellte Fragen zu virtuellen Computern in Azure Security Center, einem Produkt, das Ihnen hilft, Bedrohungen zu verhindern, zu erkennen und darauf zu reagieren
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 32023fca15de9d3f9258bc05166c3cff4184ea15
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499848"
---
# <a name="faq---questions-about-virtual-machines"></a>FAQ: Fragen zu virtuellen Computern


## <a name="what-types-of-virtual-machines-are-supported"></a>Welche Typen von virtuellen Maschinen werden unterstützt?

Die Überwachung und entsprechende Empfehlungen sind für virtuelle Computer (VMs) verfügbar, die mit dem [klassischen Bereitstellungsmodell oder mit dem Resource Manager-Bereitstellungsmodell](../azure-resource-manager/management/deployment-models.md)erstellt wurden.

Eine Liste mit unterstützten Plattformen finden Sie unter [Supported platforms in Azure Security Center (Unterstützte Plattformen in Azure Security Center)](security-center-os-coverage.md).


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Warum erkennt Azure Security Center die Antischadsoftware nicht, die auf meiner Azure-VM ausgeführt wird?

Azure Security Center erkennt Antischadsoftware, die über Azure-Erweiterungen installiert wurde. Security Center kann z.B. Antischadsoftware nicht erkennen, die auf einem Image vorinstalliert war, das Sie bereitgestellt haben, oder Antischadsoftware, die Sie auf Ihren virtuellen Computern über Ihre eigenen Prozesse installiert haben (z.B. Systeme für die Konfigurationsverwaltung).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Warum erhalte ich die Meldung „Fehlende Überprüfungsdaten“ für meinen virtuellen Computer?

Diese Meldung wird angezeigt, wenn keine Überprüfungsdaten für einen virtuellen Computer vorhanden sind. Es kann einige Zeit dauern (weniger als eine Stunde), um die Überprüfungsdaten aufzufüllen, nachdem die Datensammlung in Azure Security Center aktiviert wurde. Nach dem ersten Auffüllen der Überprüfungsdaten erhalten Sie diese Meldung möglicherweise, weil überhaupt keine oder keine aktuellen Überprüfungsdaten vorliegen. Überprüfungen werden für beendete virtuelle Computer nicht aufgefüllt. Diese Meldung kann auch angezeigt werden, wenn die Überprüfungsdaten (gemäß der Aufbewahrungsrichtlinie für den Windows-Agenten, die einen Standardwert von 30 Tagen vorgibt) nicht vor kurzem aufgefüllt wurden.


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Wie häufig sucht Security Center nach Sicherheitsrisiken des Betriebssystems, Systemupdates und Endpoint Protection-Problemen?

Unten sehen Sie Wartezeiten für Security Center-Scans auf Sicherheitsrisiken, Updates und Probleme:

- Betriebssystem-Sicherheitskonfigurationen – Daten werden innerhalb von 48 Stunden aktualisiert.
- Systemupdates – Daten werden innerhalb von 24 Stunden aktualisiert.
- Endpoint Protection-Probleme – Daten werden innerhalb von 8 Stunden aktualisiert.

Das Security Center scannt in der Regel jede Stunde nach neuen Daten und aktualisiert die Empfehlungen entsprechend. 

> [!NOTE]
> Security Center verwendet den Log Analytics-Agent zum Erfassen und Speichern von Daten. Weitere Informationen finden Sie unter [Azure Security Center – Plattformmigration](./security-center-enable-data-collection.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Warum erhalte ich die Meldung „VM-Agent fehlt“?

Der VM-Agent muss auf virtuellen Computern installiert sein, um die Datensammlung zu ermöglichen. Der VM-Agent wird standardmäßig für virtuelle Maschinen installiert, die vom Azure Marketplace bereitgestellt werden. Informationen zum Installieren des VM-Agents auf anderen virtuellen Computern finden Sie im Blogbeitrag [VM Agent and Extensions](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)(VM-Agent und Erweiterungen).