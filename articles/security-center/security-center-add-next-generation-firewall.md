---
title: Hinzufügen einer Firewall der nächsten Generation in Azure Security Center | Microsoft Docs
description: In diesem Dokument wird erläutert, wie Sie die Azure Security Center-Empfehlungen **Firewall der nächsten Generation hinzufügen** und **Datenverkehr nur über NGFW routen** implementieren.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 731102037b596091b80fbdfa02a8ff3c111b556e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60706985"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Hinzufügen einer Firewall der nächsten Generation in Azure Security Center
In Azure Security Center wird unter Umständen empfohlen, dass Sie zum Verbessern Ihrer Sicherheitsmaßnahmen eine Firewall der nächsten Generation (Next Generation Firewall, NGFW) eines Microsoft-Partners hinzufügen. In diesem Dokument sehen wir uns ein Beispiel zu dieser Vorgehensweise an.

> [!NOTE]
> Der Dienst wird anhand einer Beispielbereitstellung vorgestellt.  Es ist keine schrittweise Anleitung.
>
>

## <a name="implement-the-recommendation"></a>Implementieren der Empfehlung
1. Wählen Sie auf dem Blatt **Empfehlungen** die Option **Firewall der nächsten Generation hinzufügen** aus.
   ![Firewall der nächsten Generation hinzufügen][1]
2. Wählen Sie auf dem Blatt **Firewall der nächsten Generation hinzufügen** einen Endpunkt aus.
   ![Auswählen eines Endpunkts][2]
3. Ein zweites Blatt **Firewall der nächsten Generation hinzufügen** wird geöffnet. Sie können eine vorhandene Lösung verwenden, sofern verfügbar, oder eine neue erstellen. In diesem Beispiel sind keine vorhandenen Lösungen verfügbar. Daher erstellen wir eine Firewall der nächsten Generation.
   ![Erstellen einer Firewall der nächsten Generation][3]
4. Wählen Sie zum Erstellen einer Firewall der nächsten Generation eine Lösung aus der Liste der integrierten Partner aus. In diesem Beispiel wählen wir **Check Point**.
   ![Auswählen der Firewalllösung der nächsten Generation][4]
5. Auf dem eingeblendeten Blatt **Check Point** werden Informationen zu dieser Partnerlösung angezeigt. Klicken Sie auf dem Informationsblatt auf **Erstellen** .
   ![Blatt mit Firewallinformationen][5]
6. Das Blatt **Virtuellen Computer erstellen** wird geöffnet. Hier können Sie Informationen eingeben, die zum Einrichten des virtuellen Computers erforderlich sind, auf dem die Firewall der nächsten Generation ausgeführt wird. Führen Sie die Schritte aus, und geben Sie die für die Firewall der nächsten Generation erforderlichen Informationen ein. Klicken Sie zum Übernehmen auf „OK“.
   ![Erstellen eines virtuellen Computers zum Ausführen der Firewall der nächsten Generation][6]

## <a name="route-traffic-through-ngfw-only"></a>Datenverkehr nur durch Firewall der nächsten Generation leiten
Kehren Sie zum Blatt **Empfehlungen** zurück. Nachdem Sie über Security Center eine Firewall der nächsten Generation hinzugefügt haben, wurde ein neuer Eintrag namens **Datenverkehr nur über NGFW routen**generiert. Diese Empfehlung wird nur erstellt, wenn Sie die Firewall der nächsten Generation über Security Center installiert haben. Wenn Sie über Endpunkte mit Internetzugriff verfügen, empfiehlt Security Center die Konfiguration von Regeln für Netzwerksicherheitsgruppen, die erzwingen, dass eingehender Datenverkehr durch Ihre Firewall der nächsten Generation an Ihren virtuellen Computer geleitet wird.

1. Wählen Sie auf dem Blatt **Empfehlungen** die Option **Datenverkehr nur über NGFW routen** aus.
   ![Datenverkehr nur durch Firewall der nächsten Generation leiten][7]
2. Daraufhin wird das Blatt **Datenverkehr nur über NGFW routen** geöffnet, das die virtuellen Computer enthält, an die Sie Datenverkehr weiterleiten können. Wählen Sie einen virtuellen Computer aus der Liste aus.
   ![Auswählen eines virtuellen Computers][8]
3. Ein Blatt für den ausgewählten virtuellen Computer wird geöffnet, auf dem dazugehörige Eingangsregeln angezeigt werden. In einer Beschreibung erhalten Sie weitere Informationen zu den möglichen nächsten Schritten. Wählen Sie **Eingangsregeln bearbeiten** aus, um mit dem Bearbeiten einer Eingangsregel fortzufahren. Es wird erwartet, dass **Quelle** für die Endpunkte mit Internetzugriff, die mit der Firewall der nächsten Generation verknüpft sind, nicht auf **Beliebig** festgelegt wird. Weitere Informationen zu den Eigenschaften von Eingangsregeln finden Sie unter [Sicherheitsregeln](../virtual-network/security-overview.md#security-rules).
   ![Konfigurieren von Regeln zum Begrenzen des Zugriffs][9]
   ![Eingangsregel bearbeiten][10]

## <a name="see-also"></a>Weitere Informationen
In diesem Dokument wurde gezeigt, wie Sie die Security Center-Empfehlung „Firewall der nächsten Generation hinzufügen“ umsetzen. Weitere Informationen zu Firewalls der nächsten Generation und der Partnerlösung von Check Point finden Sie in den folgenden Artikeln:

* [Firewall der nächsten Generation](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [Check Point vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md) Hier erfahren Sie, wie Sie Sicherheitsrichtlinien konfigurieren.
* [Verwalten von Sicherheitsempfehlungen in Azure Security Center:](security-center-recommendations.md) Hier erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen.
* [Überwachen der Sicherheitsintegrität in Azure Security Center:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) : Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf diese reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center:](security-center-partner-solutions.md) Erfahren Sie, wie der Integritätsstatus Ihrer Partnerlösungen überwacht wird.
* [Azure Security Center – Häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
