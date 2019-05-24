---
title: 'Tutorial für Azure Security Center: Schützen Ihrer Ressourcen mit Azure Security Center | Microsoft-Dokumentation'
description: Dieses Tutorial zeigt, wie Sie eine Richtlinie für den Just-in-Time-VM-Zugriff und eine Anwendungssteuerungsrichtlinie konfigurieren.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: v-mohabe
ms.openlocfilehash: 8fc287826d1a98b5d61229979544fdb3780455d4
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967943"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Tutorial: Schützen Ihrer Ressourcen mit Azure Security Center
Security Center verringert Ihre Gefährdung durch Bedrohungen, indem mithilfe von Zugriffs- und Anwendungssteuerungen böswillige Aktivitäten blockiert werden. Durch einen Just-in-Time (JIT)-Zugriff auf einen virtuellen Computer wird die Anfälligkeit für Angriffe verringert, da Sie den dauerhaften Zugriff auf virtuelle Computer verweigern können. Stattdessen bieten Sie einen gesteuerten und überwachten Zugriff auf VMs nur bei Bedarf. Adaptive Anwendungssteuerungen helfen dabei, VMs gegen Schadsoftware abzusichern, indem sie steuern, welche Anwendungen auf Ihren VMs ausgeführt werden können. Security Center nutzt Machine Learning, um die auf dem virtuellen Computer ausgeführten Prozesse zu analysieren, und unterstützt Sie beim Anwenden von Whitelistregeln, die auf diesen Daten basieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren einer Richtlinie für den Just-in-Time-VM-Zugriff
> * Konfigurieren einer Anwendungssteuerungsrichtlinie

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durchlaufen der in diesem Tutorial behandelten Features müssen Sie den Tarif „Standard“ von Security Center verwenden. Sie können Security Center Standard kostenlos testen. Weitere Informationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/). Unter [Schnellstarthandbuch zu Azure Security Center](security-center-get-started.md) wird Schritt für Schritt beschrieben, wie Sie das Upgrade auf den Tarif „Standard“ durchführen.

## <a name="manage-vm-access"></a>Verwalten des VM-Zugriffs
Mit JIT Zugriff auf einen virtuellen Computer kann eingehender Datenverkehr auf den virtuellen Azure-Computern gesperrt werden, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einen einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen.

Verwaltungsports müssen nicht jederzeit geöffnet sein. Sie müssen nur geöffnet sein, während eine Verbindung mit dem virtuellen Computer besteht, z.B. zum Ausführen von Verwaltungs- oder Wartungsaufgaben. Wenn Just-In-Time aktiviert ist, verwendet das Security Center Netzwerksicherheitsgruppen-Regeln (NSG), die den Zugriff auf Verwaltungsports beschränken, um sie vor Angriffen zu schützen.

1. Wählen Sie im Hauptmenü von Security Center unter **ERWEITERTER CLOUDSCHUTZ** die Option **Just-in-Time-VM-Zugriff** aus.

   ![Just-in-Time-VM-Zugriff][1]

   **Just-In-Time-VM-Zugriff** enthält Informationen zum Status Ihrer virtuellen Computer:

   - **Konfiguriert** – Virtuelle Computer, die für die Unterstützung von Just-In-Time-Zugriff konfiguriert wurden.
   - **Empfohlen** – Virtuelle Computer, die Just-In-Time-VM-Zugriff unterstützen, jedoch nicht entsprechend konfiguriert wurden.
   - **Keine Empfehlung** – Mögliche Gründe, dass ein virtueller Computer nicht empfohlen wird:

     - Fehlende NSG – Die Just-In-Time-Lösung erfordert, dass eine NSG festgelegt ist.
     - Klassischer virtueller Computer – Der durch das Security Center gesteuerte Just-In-Time-VM-Zugriff unterstützt derzeit nur virtuelle Computer, die über Azure Resource Manager bereitgestellt wurden.
     - Andere – Ein virtueller Computer fällt in diese Kategorie, wenn die Just-In-Time-Lösung in der Sicherheitsrichtlinie des Abonnements oder der Ressourcengruppe deaktiviert ist oder wenn der virtuelle Computer über keine öffentliche IP-Adresse und über keine NSG verfügt.

2. Wählen Sie eine empfohlene VM aus, und klicken Sie auf **JIT auf 1 VM aktivieren**, um eine Just-in-Time-Richtlinie für diese VM zu konfigurieren:

   Sie können die von Security Center empfohlenen Standardports speichern oder einen neuen Port hinzufügen und konfigurieren, an dem Sie die Just-in-Time-Lösung aktivieren möchten. Fügen Sie in diesem Tutorial einen Port durch Klicken auf **Hinzufügen** hinzu.

   ![Hinzufügen einer Portkonfiguration][2]

3. Geben Sie unter **Portkonfiguration hinzufügen** Folgendes an:

   - Den Port
   - Den Protokolltyp
   - Zulässige Quell-IP-Adressen, d.h. IP-Adressbereiche, auf die der Zugriff zulässig ist, wenn die Anforderung genehmigt wurde
   - Die maximale Anforderungszeit, d.h. das maximale Zeitfenster, in dem ein bestimmter Port geöffnet sein kann

4. Klicken Sie zum Speichern auf **OK**.

## <a name="harden-vms-against-malware"></a>Absichern von VMs gegen Schadsoftware
Mit adaptiven Anwendungssteuerungen können Sie eine Gruppe von Anwendungen definieren, deren Ausführung in konfigurierten Ressourcengruppen zulässig ist. Dadurch können Sie Ihre VMs gegen Schadsoftware absichern. Security Center nutzt Machine Learning, um die auf dem virtuellen Computer ausgeführten Prozesse zu analysieren, und unterstützt Sie beim Anwenden von Whitelistregeln, die auf diesen Daten basieren.

Dieses Feature steht nur für Windows-Computer zur Verfügung.

1. Kehren Sie zum Hauptmenü von Security Center zurück. Klicken Sie unter **ERWEITERTER CLOUDSCHUTZ** auf **Adaptive Anwendungssteuerungen**.

   ![Adaptive Anwendungssteuerungen][3]

   Der Abschnitt **Ressourcengruppen** enthält drei Registerkarten:

   - **Konfiguriert**: Liste der Ressourcengruppen mit den virtuellen Computern, für die die Anwendungssteuerung bereits konfiguriert ist.
   - **Empfohlen**: Liste der Ressourcengruppen, für die Anwendungssteuerung empfohlen wird.
   - **Keine Empfehlung**: Liste der Ressourcengruppen mit virtuellen Computern, für die keine Anwendungssteuerungsempfehlungen vorliegen. Ein Beispiel wären etwa virtuelle Computer, auf denen sich die Anwendungen immer wieder ändern und die keinen konsistenten Zustand erreicht haben.

2. Klicken Sie auf die Registerkarte **Empfohlen**, um eine Liste mit Ressourcengruppen mit Anwendungssteuerungsempfehlungen anzuzeigen.

   ![Empfehlungen für die Anwendungssteuerung][4]

3. Wählen Sie eine Ressourcengruppe aus, um die Option **Regeln zur Anwendungssteuerung erstellen** zu öffnen. Sehen Sie sich unter **VMs auswählen** die Liste mit den empfohlenen virtuellen Computern an, und heben Sie die Auswahl aller virtuellen Computer auf, auf die Sie die Anwendungssteuerung nicht anwenden möchten. Sehen Sie sich unter **Prozesse für Whitelistregeln auswählen** die Liste mit empfohlenen Anwendungen an, und heben Sie die Auswahl aller Optionen auf, die Sie nicht anwenden möchten. Die Liste enthält Folgendes:

   - **NAME**: Der vollständige Anwendungspfad
   - **PROZESSE**: Die Anzahl von Anwendungen innerhalb des jeweiligen Pfads.
   - **ALLGEMEIN**: „Ja“ gibt an, dass diese Prozesse auf den meisten virtuellen Computern in dieser Ressourcengruppe ausgeführt wurden.
   - **SICHERHEITSLÜCKE**: Ein Warnsymbol gibt an, ob die Anwendungen von einem Angreifer zur Umgehung des Anwendungswhitelistings verwendet werden können. Es empfiehlt sich, diese Anwendungen vor der Genehmigung zu überprüfen.

4. Klicken Sie nach Abschluss Ihrer Auswahl auf **Erstellen**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Andere Schnellstartanleitungen und Tutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Wenn Sie planen, mit den nachfolgenden Schnellstartanleitungen und Tutorials fortzufahren, sollten Sie weiter den Tarif „Standard“ nutzen und die automatische Bereitstellung aktiviert lassen. Gehen Sie wie folgt vor, falls Sie nicht fortfahren oder zum Free-Tarif zurückkehren möchten:

1. Kehren Sie zum Hauptmenü von Security Center zurück, und wählen Sie die Option **Sicherheitsrichtlinie**.
2. Wählen Sie das Abonnement oder die Richtlinie aus, für das bzw. die Sie zu „Free“ zurückwechseln möchten. Der Bereich **Sicherheitsrichtlinie** wird geöffnet.
3. Wählen Sie unter **RICHTLINIENKOMPONENTEN** die Option **Tarif**.
4. Wählen Sie **Free**, um für das Abonnement vom Tarif „Standard“ zu „Free“ zu wechseln.
5. Wählen Sie **Speichern** aus.

Gehen Sie wie folgt vor, um die automatische Bereitstellung zu deaktivieren:

1. Kehren Sie zum Hauptmenü von Security Center zurück, und wählen Sie die Option **Sicherheitsrichtlinie**.
2. Wählen Sie das Abonnement aus, für das Sie die automatische Bereitstellung deaktivieren möchten.
3. Wählen Sie im Bereich **Sicherheitsrichtlinie – Datensammlung** unter **Onboarding** die Option **Aus**, um die automatische Bereitstellung zu deaktivieren.
4. Wählen Sie **Speichern** aus.

>[!NOTE]
> Wenn Sie die automatische Bereitstellung deaktivieren, wird Microsoft Monitoring Agent nicht von virtuellen Azure-Computern entfernt, auf denen der Agent bereitgestellt wurde. Wenn Sie die automatische Bereitstellung deaktivieren, schränkt dies die Sicherheitsüberwachung für Ihre Ressourcen ein.
>

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie erfahren, wie Sie Ihre Angriffsfläche für Bedrohungen wie folgt verringern:

> [!div class="checklist"]
> * Konfigurieren einer Richtlinie für den Just-in-Time VM-Zugriff, um einen gesteuerten und überwachten Zugriff auf VMs nur bei Bedarf zu ermöglichen
> * Konfigurieren einer adaptiven Anwendungssteuerung, um festzulegen, welche Anwendungen auf Ihren VMs ausgeführt werden können

Im nächsten Tutorial erfahren Sie, wie Sie auf Sicherheitsvorfälle reagieren.

> [!div class="nextstepaction"]
> [Tutorial: Reagieren auf Sicherheitsvorfälle](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
