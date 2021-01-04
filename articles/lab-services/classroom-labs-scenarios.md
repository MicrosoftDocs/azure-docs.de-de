---
title: Verwenden von Labs für Schulungen – Azure Lab Services
description: In diesem Artikel wird beschrieben, wie Sie Azure DevTest Labs zum Erstellen von Labs in Azure für Schulungsszenarien verwenden.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f9eb3d44d470a978e676f6268cd693b283dd2703
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433559"
---
# <a name="use-labs-for-trainings"></a>Verwenden von Labs zu Schulungszwecken
Azure Lab Services ermöglicht es Lehrern/Dozenten (Lehrkräften, Professoren, Ausbildern oder Tutoren usw.), schnell und einfach ein Online-Lab zu erstellen, um vorkonfigurierte Lernumgebungen für die Lab-Benutzer bereitzustellen. Jeder Lab-Benutzer könnte identische und isolierte Umgebungen für die Schulung verwenden. Richtlinien können angewendet werden, um sicherzustellen, dass Schulungsumgebungen für jeden Lab-Benutzer nur bei Bedarf verfügbar sind und genügend Ressourcen – z. B. virtuelle Computer – enthalten, die zur Schulung erforderlich sind. 

![Classroom-Lab](./media/classroom-labs-scenarios/classroom.png)

Labs erfüllt die folgenden Anforderungen für das Durchführen von Trainings in virtuellen Umgebungen: 

- Lab-Benutzer können ihre Schulungsumgebungen schnell bereitstellen
- Jeder Schulungscomputer muss identisch sein
- Lab-Benutzer können die von anderen Benutzern erstellten virtuellen Computer nicht sehen
- Kostenkontrolle, indem sichergestellt wird, dass Lab-Benutzer nur die zur Schulung erforderliche Anzahl von virtuellen Computer erhalten und nicht verwendete virtuelle Computer heruntergefahren werden
- Einfache Freigabe des Schulungs-Labs für alle Benutzer
- Schulungs-Labs können beliebig oft wiederverwendet werden

In diesem Artikel erfahren Sie mehr über die verschiedenen Azure Lab Services-Features, mit denen die zuvor beschriebenen Schulungsanforderungen erfüllt werden können. Zudem enthält er detaillierte Schritte, mit denen Sie ein Lab zu Schulungszwecken einrichten können.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Erstellen des Lab-Kontos als Lab-Kontoadministrator
Bei der Verwendung von Azure Lab Services muss zuerst im Azure-Portal ein Lab-Konto erstellt werden. Nachdem der Lab-Kontoadministrator das Lab-Konto erstellt hat, fügt der Administrator die Benutzer hinzu, die in der Rolle **Lab-Ersteller** Labs erstellen möchten. Die Lehrer/Dozenten erstellen Labs mit virtuellen Computern, damit die Kursteilnehmer die Übungen in ihren Kursen ausführen können. Einzelheiten hierzu finden Sie unter [Erstellen und Verwalten von Lab-Konten](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-labs"></a>Erstellen und Verwalten von Labs
Die Lehrer/Dozenten, denen im Lab-Konto die Rolle „Lab-Ersteller“ zugewiesen wurde, können im Lab-Konto ein oder mehrere Labs erstellen. Sie erstellen und konfigurieren einen virtuellen Computer als Vorlage, der die gesamte erforderliche Software enthält, die für die Übungen in dem jeweiligen Kurs erforderlich sind. Sie wählen aus den verfügbaren Images zum Erstellen von Classroom-Labs ein vorgefertigtes Image aus und passen es durch die Installation der für das Lab erforderlichen Software an. Einzelheiten hierzu finden Sie unter [Erstellen und Verwalten von Labs](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurieren von Nutzungseinstellungen und Richtlinien
Ein Lab-Ersteller kann Benutzer hinzufügen oder entfernen, einen Registrierungslink abrufen und an Lab-Benutzer senden, Richtlinien (z. B. individuelle Kontingente pro Benutzer) einrichten, die Anzahl der im Lab verfügbaren Computer aktualisieren und vieles mehr. Einzelheiten hierzu finden Sie unter [Konfigurieren von Nutzungseinstellungen und Richtlinien](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Erstellen und Verwalten von Zeitplänen
Mithilfe von Zeitplänen können Sie ein Classroom-Lab so konfigurieren, dass VMs im Lab automatisch zu einem bestimmten Zeitpunkt gestartet und heruntergefahren werden. Sie können einen einmaligen Zeitplan oder einen sich wiederholenden Zeitplan definieren. Einzelheiten hierzu finden Sie unter [Erstellen und Verwalten von Zeitplänen für Labs](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Einrichten und Veröffentlichen einer Vorlage für virtuelle Computer
Eine Vorlage in einem Lab ist ein VM-Basisimage und dient zur Erstellung der virtuellen Computer aller Benutzer. Richten Sie die Vorlage für den virtuellen Computer so ein, dass sie genau das enthält, was Sie den Schulungsteilnehmern zur Verfügung stellen möchten. Sie können einen Namen und eine Beschreibung der Vorlage angeben, die den Lab-Benutzern angezeigt werden. Anschließend veröffentlichen Sie die Vorlage, um Instanzen der Vorlagen-VM für Ihre Lab-Benutzer zur Verfügung zu stellen. Wenn Sie eine Vorlage veröffentlichen, werden von Azure Lab Services im Lab mithilfe der Vorlage virtuelle Computer erstellt. Die Anzahl der in diesem Vorgang erstellten virtuellen Computer entspricht der maximalen Anzahl von Benutzern im Lab, die Sie in der Nutzungsrichtlinie des Labs festlegen können. Alle virtuellen Computer haben die gleiche Konfiguration wie die Vorlage. Einzelheiten hierzu finden Sie unter [Einrichten und Veröffentlichen einer Vorlage für einen virtuellen Computer](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Verwenden von virtuellen Computern in Classroom-Labs
Die Kursteilnehmer registrieren sich beim Lab und stellen eine Verbindung mit den virtuellen Computern her, damit sie die Übungen für den Kurs ausführen können. Einzelheiten hierzu finden Sie unter [Zugreifen auf ein Classroom-Lab](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Nächste Schritte
Beginnen Sie mit dem Erstellen eines Lab-Kontos in Labs mithilfe der Anweisungen in diesem Artikel: [Tutorial: Einrichten eines Lab-Kontos mit Azure Lab Services](tutorial-setup-lab-account.md).