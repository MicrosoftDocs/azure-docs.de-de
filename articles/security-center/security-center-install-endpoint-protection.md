---
title: Behandeln von Endpoint Protection-Problemen mit Azure Security Center | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Endpoint Protection-Probleme in Azure Security Center behandeln.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: rkarlin
ms.openlocfilehash: 882d4e0592b74e8af30ff5bf110a41e403c3bf7d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60906474"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Behandeln von Endpoint Protection-Problemen mit Azure Security Center
Azure Security Center überwacht den Status des Antischadsoftware-Schutzes und meldet diesen auf dem Blatt „Endpoint protection issues“ (Endpoint Protection-Probleme). Security Center hebt Probleme wie erkannte Bedrohungen und unzureichenden Schutz hervor, die Ihre virtuellen Computer (VMs) und physischen Computer anfällig für Antischadsoftwarebedrohungen machen können. Mithilfe der Informationen unter **Endpoint protection issues** (Endpoint Protection-Probleme) können Sie die Behandlung der erkannten Probleme planen.

Security Center meldet folgende Endpoint Protection-Probleme:

- Endpoint protection not installed on Azure VMs (Endpoint Protection auf virtuellen Azure-Computern nicht installiert): Auf diesen virtuellen Azure-Computern ist keine unterstützte Antischadsoftware-Lösung installiert.
- Endpoint protection not installed on non-Azure computers (Endpoint Protection auf Azure-fremden Computern nicht installiert): Auf diesen Azure-fremden Computern ist keine unterstützte Antischadsoftware installiert.
- Endpoint Protection-Integrität:

  - Signatur veraltet: Auf diesen virtuellen und physischen Computern ist eine Antischadsoftware-Lösung installiert, diese verfügt jedoch nicht über die neuesten Antischadsoftware-Signaturen.
  - Kein Echtzeitschutz: Auf diesen virtuellen und physischen Computern ist eine Antischadsoftware-Lösung installiert, diese ist jedoch nicht für Echtzeitschutz konfiguriert.   Der Dienst wurde möglicherweise deaktiviert, oder Security Center kann den Status nicht abrufen, da die Lösung nicht unterstützt wird. Eine Liste mit unterstützten Lösungen finden Sie unter [Partnerintegration in Azure Security Center](security-center-os-coverage.md#supported-endpoint-protection-solutions).
  - Keine Berichterstattung: Von der installierten Antischadsoftware-Lösung werden keine Daten gemeldet.
  - Unbekannt: Eine Antischadsoftware-Lösung ist installiert, aber der Status ist unbekannt, oder es wird ein unbekannter Fehler gemeldet.

    > [!NOTE]
    > Eine Liste der Sicherheitslösungen für den Endpunktschutz, die in Security Center integriert sind, finden Sie unter [Integrieren von Sicherheitslösungen](security-center-os-coverage.md#supported-endpoint-protection-solutions).
    >
    >

## <a name="implement-the-recommendation"></a>Implementieren der Empfehlung
Endpoint Protection-Probleme werden in Security Center als Empfehlung präsentiert.  Wenn Ihre Umgebung für Antischadsoftwarebedrohungen anfällig ist, wird diese Empfehlung unter **Empfehlungen** und unter **Compute** angezeigt. Zum Anzeigen des Dashboards **Endpoint protection issues** (Endpoint Protection-Probleme) muss der Compute-Workflow verwendet werden.

In diesem Beispiel verwenden wir **Compute**.  Sie erfahren, wie Sie Antischadsoftware auf virtuellen Azure-Computern und auf Azure-fremden Computern installieren.

## <a name="install-antimalware-on-azure-vms"></a>Installieren von Antischadsoftware auf virtuellen Azure-Computern

1. Klicken Sie im Hauptmenü von Security Center oder unter **Übersicht** auf **Compute und Apps**.

   ![Auswählen von „Compute“][1]

2. Klicken Sie unter **Compute** auf **Endpoint protection issues** (Endpoint Protection-Probleme). Das Dashboard **Endpoint protection issues** (Endpoint Protection-Probleme) wird geöffnet.

   ![Auswählen von „Endpoint protection issues“ (Endpoint Protection-Probleme)][2]

   Im oberen Bereich des Dashboards befindet sich Folgendes:

   - Installed endpoint protection providers (Installierte Endpoint Protection-Anbieter): Eine Liste mit den verschiedenen Anbietern, die von Security Center ermittelt wurden.
   - Installed endpoint protection health state (Integritätszustand der installierten Endpoint Protection-Lösung): Zeigt den Integritätszustand der virtuellen und physischen Computer an, auf denen eine Endpoint Protection-Lösung installiert ist. Das Diagramm zeigt die Anzahl einwandfreier virtueller und physischer Computer und die Anzahl von Computern mit unzureichendem Schutz an.
   - Erkannte Schadsoftware: Zeigt die Anzahl virtueller und physischer Computer an, für die Security Center die Erkennung von Schadsoftware meldet.
   - Angegriffene Computer: Zeigt die Anzahl virtueller und physischer Computer an, für die Security Center Angriffe durch Schadsoftware meldet.

   Im unteren Bereich des Dashboards befindet sich eine Liste mit Endpoint Protection-Problemen und folgenden Informationen:  

   - **GESAMT**: Die Anzahl virtueller und physischer Computer, die von dem Problem betroffen sind.
   - Ein Balken, der die Anzahl virtueller und physischer Computer darstellt, die von dem Problem betroffen sind. Die Balkenfarbe gibt Aufschluss über die Priorität:

      - Rot: Hohe Priorität; sollte umgehend behoben werden.
      - Orange: Mittlere Priorität; sollte zeitnah behoben werden.

3. Klicken Sie auf **Endpoint protection not installed on Azure VMs** (Endpoint Protection auf virtuellen Azure-Computern nicht installiert).

   ![Klicken auf „Endpoint protection not installed on Azure VMs“ (Endpoint Protection auf virtuellen Azure-Computern nicht installiert)][3]

4. Unter **Endpoint protection not installed on Azure VMs** (Endpoint Protection auf virtuellen Azure-Computern nicht installiert) befindet sich eine Liste mit virtuellen Azure-Computern, auf denen keine Antischadsoftware installiert ist.  Sie können Antischadsoftware entweder auf allen virtuellen Computer in der Liste installieren oder einzelne virtuelle Computer auswählen, auf denen Antischadsoftware installiert werden soll, indem Sie auf den jeweiligen virtuellen Computer klicken.
5. Wählen Sie unter **Endpoint Protection auswählen** die gewünschte Endpoint Protection-Lösung aus. Wählen Sie in diesem Beispiel **Microsoft Antimalware** aus.
6. Weitere Informationen zur Endpoint Protection-Lösung werden angezeigt. Klicken Sie auf **Erstellen**.

## <a name="install-antimalware-on-non-azure-computers"></a>Installieren von Antischadsoftware auf Azure-fremden Computern

1. Kehren Sie zu **Endpoint protection issues** (Endpoint Protection-Probleme) zurück, und klicken Sie auf **Endpoint protection not installed on non-Azure computers** (Endpoint Protection auf Azure-fremden Computern nicht installiert).

   ![Klicken auf „Endpoint protection not installed on non-Azure computers“ (Endpoint Protection auf Azure-fremden Computern nicht installiert)][4]

2. Wählen Sie unter **Endpoint protection not installed on non-Azure computers** (Endpoint Protection auf Azure-fremden Computern nicht installiert) einen Arbeitsbereich aus. Eine für den Arbeitsbereich gefilterte Azure Monitor-Protokollsuchabfrage wird geöffnet und zeigt eine Liste mit Computern ohne Antischadsoftware an. Klicken Sie auf einen Computer in der Liste, um weitere Informationen zu erhalten.

   ![Azure Monitor-Protokollsuche][5]

Ein weiteres Suchergebnis wird geöffnet, das gefilterte Informationen für diesen speziellen Computer enthält.

  ![Azure Monitor-Protokollsuche][6]

> [!NOTE]
> Wir empfehlen, Endpoint Protection für alle virtuellen und physischen Computer bereitzustellen, um Viren, Spyware und andere Schadsoftware zu erkennen und zu entfernen.
>
>

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde gezeigt, wie Sie die Security Center-Empfehlung „Endpoint Protection installieren“ implementieren. Weitere Informationen zum Aktivieren von Antischadsoftware von Microsoft finden Sie in den folgenden Dokumenten:

* [Microsoft Antimalware for Cloud Services and Virtual Machine (Antischadsoftware von Microsoft für Cloud Services und virtuelle Computer)](../security/azure-security-antimalware.md): Erfahren Sie mehr zur Bereitstellung von Antischadsoftware von Microsoft.

Weitere Informationen zu Security Center finden Sie in den folgenden Dokumenten:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md) Hier erfahren Sie, wie Sie Sicherheitsrichtlinien konfigurieren.
* [Verwalten von Sicherheitsempfehlungen in Azure Security Center:](security-center-recommendations.md) Hier erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
* [Überwachen der Sicherheitsintegrität in Azure Security Center:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) : Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center:](security-center-partner-solutions.md) Erfahren Sie, wie der Integritätsstatus Ihrer Partnerlösungen überwacht wird.
* [Azure Security Center – Häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
