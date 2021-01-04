---
title: Ermitteln von Apps auf lokalen Servern mit Azure Migrate
description: Erfahren Sie, wie Sie Apps, Rollen und Funktionen auf lokalen Servern mithilfe der Azure Migrate-Serverbewertung ermitteln.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: eb589c08122cd47747c005c13d12b336319fd558
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752004"
---
# <a name="discover-machine-apps-roles-and-features"></a>Ermitteln von Apps, Rollen und Features eines Computers

In diesem Abschnitt wird beschrieben, wie Anwendungen, Rollen und Features, die auf lokalen Servern installiert sind, mithilfe der Azure Migrate-Serverbewertung.

Die Ermittlung des Bestands an Apps, Rollen und Features, die auf lokalen Computern ausgeführt werden, hilft bei der Identifizierung und Anpassung eines Migrationspfads zu Azure für Ihre Workloads. Die App-Ermittlung verwendet die Azure Migrate-Appliance zur Durchführung von Ermittlungen unter Verwendung von VM-Gastanmeldeinformationen. Die App-Ermittlung erfolgt ohne Agent. Auf den virtuellen Computern wird nichts installiert.

> [!NOTE]
> Die App-Ermittlung befindet sich derzeit nur für VMware-VMs in der Vorschau und ist ausschließlich auf die Ermittlung beschränkt. Eine app-basierte Bewertung ist noch nicht verfügbar. 


## <a name="before-you-start"></a>Vorbereitung

- Stellen Sie Folgendes sicher:
    - Sie haben ein Azure Migrate-Projekt [erstellt](./create-manage-projects.md).
    - Sie haben das Azure Migrate-Serverbewertungstool zu einem Projekt [hinzugefügt](how-to-assess.md).
- Sie haben die Unterstützung und die Anforderungen für die App-Ermittlung [überprüft](migrate-support-matrix-vmware.md#vmware-requirements).
- Stellen Sie sicher, dass auf den virtuellen Computern, auf denen Sie die App-Ermittlung ausführen, PowerShell Version 2.0 oder höher sowie die VMware Tools (höher als 10.2.0) installiert sind.
- Überprüfen Sie die [Anforderungen](migrate-appliance.md) für die Bereitstellung der Azure Migrate-Appliance.


## <a name="deploy-the-azure-migrate-appliance"></a>Bereitstellen der Azure Migrate-Appliance

1. [Überprüfen Sie die Anforderungen](migrate-appliance.md#appliance---vmware) für die Bereitstellung der Azure Migrate-Appliance.
2. Überprüfen Sie die Azure-URLs, die die Appliance für den Zugriff in [öffentlichen Clouds](migrate-appliance.md#public-cloud-urls) und [Government-Clouds](migrate-appliance.md#government-cloud-urls) benötigt.
3. [Überprüfen Sie die Daten](migrate-appliance.md#collected-data---vmware), die die Appliance während der Ermittlung und Bewertung sammelt.
4. [Beachten](migrate-support-matrix-vmware.md#port-access-requirements) Sie die Portzugriffsanforderungen für die Appliance.
5. [Stellen Sie die Azure Migrate-Appliance bereit](how-to-set-up-appliance-vmware.md), um die Ermittlung zu starten. Zum Bereitstellen der Appliance laden Sie eine OVA-Vorlage herunter und importieren Sie diese in VMware, um die Appliance als VMware-VM zu erstellen. Sie konfigurieren die Appliance und registrieren sie dann bei Azure Migrate.
6. Um eine kontinuierliche Ermittlung zu starten, geben Sie bei der Bereitstellung der Appliance folgende Informationen an:
    - Name des vCenter-Servers, zu dem eine Verbindung hergestellt werden soll.
    - Anmeldeinformationen, die Sie für die Appliance erstellt haben, um eine Verbindung zum vCenter-Server herzustellen.
    - Die Kontoanmeldeinformationen, die Sie für die Appliance für die Verbindung zu Windows-/Linux-VMs erstellt haben.

Nachdem die Appliance bereitgestellt wurde und Sie die Anmeldeinformationen angegeben haben, startet das Gerät die kontinuierliche Ermittlung von VM-Metadaten und Leistungsdaten sowie die Ermittlung von Apps, Features und Rollen.  Die Dauer der App-Ermittlung hängt von der Anzahl der vorhandenen VMs ab. Die App-Ermittlung für 500 VMs dauert ca. eine Stunde.

## <a name="verify-permissions"></a>Überprüfen von Berechtigungen

Sie [haben ein schreibgeschütztes vCenter Server-Konto](./tutorial-discover-vmware.md#prepare-vmware) zur Ermittlung und Bewertung erstellt. Das schreibgeschützte Konto benötigt für **Virtuelle Computer** > **Gastvorgänge** aktivierte Berechtigungen, um mit der VM für die App-Ermittlung interagieren zu können.

### <a name="add-the-user-account-to-the-appliance"></a>Hinzufügen des Benutzerkontos zur Appliance

Fügen Sie das Benutzerkonto wie folgt hinzu:

1. Öffnen Sie die Verwaltungs-App für die Appliance. 
2. Navigieren Sie zum Bereich **vCenter-Details angeben**.
3. Klicken Sie unter **Anwendungen und Abhängigkeiten auf VMs ermitteln** auf **Anmeldeinformationen hinzufügen**.
3. Wählen Sie das **Betriebssystem** aus, geben Sie einen benutzerfreundlichen Namen für das Konto an, und geben Sie dann **Benutzername**/**Kennwort** an.
6. Klicken Sie auf **Speichern**.
7. Klicken Sie auf **Speichern und Ermittlung starten**.

    ![VM-Benutzerkonto hinzufügen](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>Überprüfen und Exportieren des Bestands

Wenn Sie Anmeldeinformationen für die App-Ermittlung eingegeben haben, können Sie den App-Bestand nach Abschluss der Ermittlung im Azure-Portal überprüfen und exportieren.

1. Klicken Sie unter **Azure Migrate – Server** > **Azure Migrate: Serverbewertung** auf die angezeigte Anzahl, um die Seite **Ermittelte Server** zu öffnen.

    > [!NOTE]
    > In dieser Phase können Sie optional auch die Abhängigkeitsanalyse für ermittelte Computer einrichten, sodass Sie Abhängigkeiten zwischen den Computern visualisieren können, die Sie bewerten möchten. [Weitere Informationen](concepts-dependency-visualization.md) zur Abhängigkeitsanalyse.

2. Klicken Sie in **Ermittelte Anwendungen** auf die angezeigte Anzahl.
3. In **Anwendungsbestand** können Sie die ermittelten Apps, Rollen und Features überprüfen.
4. Klicken Sie zum Exportieren des Bestands in **Ermittelte Server** auf **App-Bestand exportieren**.

Der App-Bestand wird im Excel-Format exportiert und heruntergeladen. Im Arbeitsblatt für den **Anwendungsbestand** werden alle Apps angezeigt, die auf allen Computern ermittelt wurden.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie eine Bewertung](how-to-create-assessment.md) für ermittelte Server.
- Bewerten Sie SQL Server-Datenbanken mit [Azure Migrate-Datenbankbewertung](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).