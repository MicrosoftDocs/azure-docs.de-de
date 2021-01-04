---
title: 'Tutorial: Verwenden von Azure Security Center für virtuelle Computer in Azure'
description: In diesem Tutorial erfahren Sie mehr über die Funktionen von Azure Security Center zum Schutz und zur Absicherung Ihrer virtuellen Computer in Azure.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/11/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aec223ceca242e37e25f9e7288c8e0c0e2ea7535
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594793"
---
# <a name="tutorial-use-azure-security-center-to-monitor-virtual-machines"></a>Tutorial: Verwenden von Azure Security Center zum Überwachen virtueller Computer

Azure Security Center kann Ihnen dabei helfen, Einsicht in die Sicherheitsmaßnahmen für Ihre Azure-Ressourcen zu erhalten. Security Center bietet eine integrierte Sicherheitsüberwachung. Sie können so Bedrohungen erkennen, die andernfalls möglicherweise unbemerkt bleiben. In diesem Tutorial erfahren Sie etwas über Azure Security Center und folgende Maßnahmen:
 
> [!div class="checklist"]
> * Einrichten der Datensammlung
> * Einrichten von Sicherheitsrichtlinien
> * Anzeigen und Beheben von Integritätsproblemen bei der Konfiguration
> * Überprüfen erkannter Bedrohungen

## <a name="security-center-overview"></a>Übersicht über das Security Center

Mit dem Security Center können Sie potenzielle Probleme in Bezug auf die VM-Konfiguration und gezielte Sicherheitsbedrohungen feststellen. Dies kann virtuelle Computer ohne Netzwerksicherheitsgruppen, unverschlüsselte Datenträger und Brute-Force-RDP-Angriffe (Remote Desktop Protocol) einschließen. Diese Informationen werden auf dem Security Center-Dashboard in übersichtlichen Diagrammen angezeigt.

Wählen Sie für den Zugriff auf das Security Center-Dashboard im Azure-Portal auf der Menüleiste **Security Center** aus. Auf dem Dashboard können Sie die Sicherheitsintegrität Ihrer Azure-Umgebung sehen, nach einer bestimmten Anzahl von aktuellen Empfehlungen suchen und den aktuellen Status von Bedrohungswarnungen anzeigen. Sie können jedes Übersichtsdiagramm erweitern, um weitere Details anzuzeigen.

![Security Center-Dashboard](./media/tutorial-azure-security/asc-dash.png)

Security Center bietet über die reine Datenermittlung hinaus auch Empfehlungen für erkannte Probleme. Wenn etwa ein virtueller Computer ohne eine angefügte Netzwerksicherheitsgruppe bereitgestellt wurde, zeigt Security Center beispielsweise eine Empfehlung für mögliche Maßnahmen an. Sie erhalten automatisierte Korrekturmaßnahmen, ohne das Umfeld von Security Center verlassen zu müssen.  

![Screenshot: Seite „Empfehlungen“ mit einer Beschreibung, einer Ressource, dem Zustand und dem Schweregrad von Empfehlungen](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Einrichten der Datensammlung

Bevor Sie Einblick in VM-Sicherheitskonfigurationen erhalten können, muss die Security Center-Datensammlung konfiguriert werden. Dies umfasst das Aktivieren der Datensammlung, wodurch automatisch der Microsoft Monitoring Agent auf allen virtuellen Computern in Ihrem Abonnement installiert wird.

1. Klicken Sie auf dem Security Center-Dashboard auf **Sicherheitsrichtlinie**, und wählen Sie dann Ihr Abonnement aus. 
2. Für die **Datensammlung** wählen Sie unter **Automatische Bereitstellung** die Option **Ein**.
3. Behalten Sie unter **Standardmäßige Arbeitsbereichskonfiguration** die Option **Von Security Center erstellte Arbeitsbereiche verwenden (Standard)** bei.
4. Lassen Sie unter **Sicherheitsereignisse** die Standardoption **Allgemein** aktiviert.
4. Klicken Sie oben auf der Seite auf **Speichern**. 

Der Datensammlungs-Agent von Security Center wird daraufhin auf allen virtuellen Computern installiert, und die Datensammlung beginnt. 

## <a name="set-up-a-security-policy"></a>Einrichten einer Sicherheitsrichtlinie

Sicherheitsrichtlinien werden verwendet, um die Elemente zu definieren, für die Security Center Daten sammelt und Empfehlungen gibt. Sie können unterschiedliche Sicherheitsrichtlinien auf verschiedene Gruppen von Azure-Ressourcen anwenden. Obwohl Azure-Ressourcen standardmäßig für sämtliche Richtlinienelemente ausgewertet werden, können Sie einzelne Richtlinienelemente für alle Azure-Ressourcen oder für eine Ressourcengruppe deaktivieren. Ausführliche Informationen zu Security Center-Sicherheitsrichtlinien finden Sie unter [Festlegen von Sicherheitsrichtlinien in Azure Security Center](../security-center/tutorial-security-policy.md). 

So richten Sie eine Sicherheitsrichtlinie für ein gesamtes Abonnement ein:

1. Wählen Sie im Security Center-Dashboard **Sicherheitsrichtlinie** und dann Ihr Abonnement aus.
2. Wählen Sie auf dem Blatt **Sicherheitsrichtlinie** die Option **Sicherheitsrichtlinie** aus. 
3. Aktivieren oder deaktivieren Sie auf dem Blatt **Sicherheitsrichtlinie – Sicherheitsrichtlinie** die Richtlinienelemente, die Sie auf das Abonnement anwenden möchten.
4. Wenn Sie Ihre Einstellungen festgelegt haben, wählen Sie im oberen Bereich des Blatts **Speichern** aus. 

![Eindeutige Richtlinie](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Anzeigen der Integrität der VM-Konfiguration

Nachdem Sie die Datensammlung aktiviert und eine Sicherheitsrichtlinie festgelegt haben, beginnt Security Center damit, Warnungen und Empfehlungen anzuzeigen. Wenn weitere VMs bereitgestellt werden, wird auf diesen der Datensammlungs-Agent installiert. Security Center sammelt dann auch Daten für die neuen virtuellen Computer. Ausführliche Informationen zur Integrität der VM-Konfiguration finden Sie unter [Schützen von virtuellen Computern in Security Center](../security-center/asset-inventory.md). 

Bei der Datensammlung wird die Ressourcenintegrität für jede VM und die zugehörige Azure-Ressource aggregiert. Die Informationen werden in einem übersichtlichen Diagramm dargestellt. 

So zeigen Sie die Ressourcenintegrität an:

1.  Wählen Sie im Security Center-Dashboard unter **Prävention** die Option **Compute** aus. 
2.  Wählen Sie auf dem Blatt **Compute** die Option **VMs und Computer** aus. Diese Ansicht enthält eine Zusammenfassung des Konfigurationsstatus aller VMs.

![Computeintegrität](./media/tutorial-azure-security/compute-health.png)

Um alle Empfehlungen für einen virtuellen Computer anzuzeigen, wählen Sie den virtuellen Computer aus. 

## <a name="remediate-configuration-issues"></a>Beheben von Konfigurationsproblemen

Sobald Security Center mit dem Auffüllen der Konfigurationsdaten beginnt, werden Empfehlungen für die eingerichtete Sicherheitsrichtlinie bereitgestellt. Wenn eine VM beispielsweise ohne eine zugeordnete Netzwerksicherheitsgruppe eingerichtet wurde, wird eine Empfehlung zur Erstellung einer solchen Gruppe bereitgestellt. 

So zeigen Sie eine Liste aller Empfehlungen an 

1. Wählen Sie auf dem Security Center-Dashboard **Empfehlungen** aus.
2. Wählen Sie eine bestimmte Empfehlung aus. Es wird eine Liste aller Ressourcen angezeigt, für die diese Empfehlung gilt.
3. Um eine Empfehlung anzuwenden, wählen Sie die Ressource aus. 
4. Führen Sie die Anweisungen zur Wartung aus. 

In vielen Fällen stellt Security Center Schritte zur Umsetzung der Empfehlung vor, die Sie ausführen können, ohne Security Center verlassen zu müssen. Im folgenden Beispiel erkennt Security Center eine Netzwerksicherheitsgruppe, die eine uneingeschränkte Eingangsregel enthält. Sie können auf der Empfehlungsseite die Schaltfläche **Eingangsregeln bearbeiten** auswählen. Die für das Ändern der Regel erforderliche Benutzeroberfläche wird angezeigt. 

![Empfehlungen](./media/tutorial-azure-security/remediation.png)

Nachdem Empfehlungen umgesetzt wurden, werden sie als gelöst markiert. 

## <a name="view-detected-threats"></a>Anzeigen erkannter Bedrohungen

Neben den Empfehlungen zur Ressourcenkonfiguration bietet Security Center auch Warnungen zur Bedrohungserkennung. Das Sicherheitswarnfeature aggregiert die von den einzelnen VMs gesammelten Daten, Azure-Netzwerkprotokolle und verbundenen Partnerlösungen, um Sicherheitsrisiken für Azure-Ressourcen zu erkennen. Ausführliche Informationen zu Funktionen der Security Center-Bedrohungserkennung finden Sie unter [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md#detect-threats).

Für das Sicherheitswarnfeature muss der Security Center-Tarif von *Free* auf *Standard* erhöht werden. Sie können eine **kostenlose Testversion** nutzen, wenn Sie auf diesen höheren Tarif umstellen. 

So ändern Sie den Tarif  

1. Klicken Sie auf dem Security Center-Dashboard auf **Sicherheitsrichtlinie**, und wählen Sie dann Ihr Abonnement aus.
2. Wählen Sie **Tarif** aus.
3. Wählen Sie **Standard** aus, und klicken Sie dann im oberen Bereich des Blatts auf **Speichern**.


Nach dem Ändern des Tarifs wird das Diagramm zu den Sicherheitswarnungen aufgefüllt, sobald Sicherheitsbedrohungen erkannt werden.

![Sicherheitswarnungen](./media/tutorial-azure-security/security-alerts.png)

Wählen Sie eine Warnung aus, um weitere Informationen anzuzeigen. Sie können beispielsweise eine Beschreibung der Bedrohung, den Zeitpunkt der Erkennung, alle Versuche und die empfohlenen Korrekturmaßnahmen anzeigen. Im folgenden Beispiel wurde ein Brute-Force-RDP-Angriff mit 294 RDP-Fehlversuchen erkannt. Eine empfohlene Lösung wird bereitgestellt.

![RDP-Angriff](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial richten Sie Azure Security Center ein und überprüfen dann VMs in Security Center. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Einrichten der Datensammlung
> * Einrichten von Sicherheitsrichtlinien
> * Anzeigen und Beheben von Integritätsproblemen bei der Konfiguration
> * Überprüfen erkannter Bedrohungen

Wechseln Sie zum nächsten Tutorial, um mehr über das Erstellen einer CI/CD-Pipeline mit Jenkins, GitHub und Docker zu erfahren.

> [!div class="nextstepaction"]
> [Erstellen einer CI/CD-Infrastruktur mit Jenkins, GitHub und Docker](/azure/developer/jenkins/pipeline-with-github-and-docker)