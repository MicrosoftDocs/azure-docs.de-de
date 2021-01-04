---
title: Konzepte für Labs – Azure Lab Services | Microsoft-Dokumentation
description: Lernen Sie die grundlegenden Konzepte von Lab Services kennen, und erfahren Sie, wie der Dienst das Erstellen und Verwalten von Labs vereinfacht.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa3a8dad195b4b3cbf0786c8923c8b330d148898
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435517"
---
# <a name="labs-concepts"></a>Konzepte für Labs

Die folgende Liste enthält wichtige Lab Services-Konzepte und -Definitionen:

## <a name="quota"></a>Kontingent

Das Kontingent ist das Zeitlimit (in Stunden), das ein Lehrer/Dozent für die Verwendung einer Lab-VM durch einen Kursteilnehmer festlegen kann. Es kann auf 0 (null) oder eine bestimmte Anzahl von Stunden festgelegt werden. Wenn das Kontingent auf 0 festgelegt wird, kann der Kursteilnehmer den virtuellen Computer nur verwenden, wenn ein Zeitplan ausgeführt wird oder wenn ein Lehrer/Dozent den virtuellen Computer manuell für den Kursteilnehmer einschaltet.  

Die Kontingentstunden werden gezählt, wenn Kursteilnehmer den virtuellen Computer in einem Lab selbst starten.  Wenn ein Lehrer/Dozent den virtuellen Computer in einem Lab für einen Kursteilnehmer manuell startet, werden für den Kursteilnehmer keine Kontingentstunden verwendet.

## <a name="schedules"></a>Zeitpläne

Zeitpläne sind die Zeitfenster, die ein Lehrer/Dozent für die Klasse erstellen kann, damit die virtuellen Computer der Kursteilnehmer für den Unterricht zur Verfügung stehen.  Zeitpläne können einmalig oder wiederkehrend sein.  Kontingentstunden werden nicht verbraucht, wenn ein Zeitplan ausgeführt wird.

Es stehen drei Arten von Zeitplänen zur Verfügung: „Standard“, „Start only“ (Nur starten) und „Stop only“ (Nur beenden).

- **Standard**: Die Dienstebene Standard bietet eine verbesserte Leistungsvorhersagbarkeit und ist für Datenbanken mit mehreren gleichzeitigen Anforderungen konzipiert, z.B.  Dieser Zeitplan startet alle virtuellen Computer der Kursteilnehmer zur angegebenen Startzeit und schaltet alle virtuellen Computer der Kursteilnehmer zur angegebenen Endzeit ab.
- **Start only** (Nur starten).   Dieser Zeitplan startet alle virtuellen Computer der Kursteilnehmer zum angegebenen Zeitpunkt.  Virtuelle Computer der Kursteilnehmer werden erst dann angehalten, wenn ein Kursteilnehmer seinen virtuellen Computer über das Azure Lab Services-Portal beendet oder wenn nur ein Zeitplan für „Stop only“ (Nur beenden) vorliegt.
- **Stop only** (Nur beenden).  Dieser Zeitplan beendet alle virtuellen Computer der Kursteilnehmer zum angegebenen Zeitpunkt.  

## <a name="template-virtual-machine"></a>Vorlage für virtuelle Maschinen

Eine Vorlage für virtuelle Maschinen in einem Lab ist ein VM-Basisimage, mit dem die virtuellen Computer aller Benutzer erstellt werden. Kursleiter und Lab-Ersteller können die Vorlage für virtuelle Maschinen einrichten und mit der Software, die sie den Teilnehmern für die Labs bereitstellen möchten, konfigurieren. Wenn Sie eine VM-Vorlage veröffentlichen, erstellt oder aktualisiert Azure Lab Services die Lab-VMs basierend auf der VM-Vorlage.

## <a name="user-profiles"></a>Benutzerprofile

In diesem Artikel werden verschiedene Benutzerprofile in Azure Lab Services beschrieben.

### <a name="lab-account-owner"></a>Lab-Kontobesitzer

Typischerweise fungiert ein IT-Administrator der Cloudressourcen eines Unternehmens, der das Azure-Abonnement besitzt, als Besitzer eines Lab-Kontos und erledigt die folgenden Aufgaben:

- Einrichten eines Lab-Dienstkontos für Ihre Organisation
- Verwalten und Konfigurieren von Richtlinien in allen Labs
- Erteilen von Berechtigungen an Personen in der Organisation zum Erstellen eines Lab unter dem Lab-Konto

### <a name="educator"></a>Lehrer/Dozent

Üblicherweise erstellen Benutzer (etwa eine Lehrkraft oder ein Onlinetrainer) Labs unter einem Labkonto. Ein Lehrer/Dozent führt die folgenden Aufgaben aus:

- Erstellen eines Classroom-Labs
- Erstellen virtueller Computer im Lab
- Installieren der auf virtuellen Computern benötigten Software
- Angeben, wer auf das Lab zugreifen darf
- Bereitstellen eines Registrierungslinks zum Lab für Schüler/Studenten

### <a name="student"></a>Student

Ein Schüler/Student führt die folgenden Aufgaben aus:

- Klicken auf den Registrierungslink, den der Lab-Benutzer vom Lab-Ersteller erhält, um sich beim Lab zu registrieren
- Herstellen einer Verbindung mit einem virtuellen Computer im Lab zur Verwendung für Klassentätigkeiten, Aufgaben und Projekte

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit dem Einrichten eines Labkontos vertraut, das benötigt wird, um ein Classroom-Lab unter Verwendung von Azure Lab Services zu erstellen:

- [Einrichten eines Lab-Kontos](tutorial-setup-lab-account.md)
