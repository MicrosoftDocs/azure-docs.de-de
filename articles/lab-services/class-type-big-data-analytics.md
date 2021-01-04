---
title: Einrichten eines Labs zum Lehren von Big Data-Analysen mit Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe der Docker-Bereitstellung von Hortonworks Data Platform (HDP) ein Lab zum Lehren von Big Data-Analysen einrichten.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659895"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Einrichten eines Labs zum Lehren von Big Data-Analysen mithilfe der Docker-Bereitstellung von HortonWorks Data Platform

Erfahren Sie, wie Sie für einen Kurs ein Lab zum Lehren von Big Data-Analysen einrichten.  Bei dieser Art von Kurs lernen die Kursteilnehmer, mit großen Datenmengen umzugehen und maschinelle und statistische Lernalgorithmen anzuwenden, um Erkenntnisse aus Daten zu gewinnen.  Ein wichtiges Ziel für Kursteilnehmer ist es, den Umgang mit Datenanalysetools zu erlernen, wie z. B. dem [Open-Source-Softwarepaket von Apache Hadoop](https://hadoop.apache.org/), das Tools für die Speicherung, Verwaltung und Verarbeitung von Big Data bereitstellt.

In diesem Lab verwenden die Kursteilnehmer eine beliebte kommerzielle Version von Hadoop mit dem Namen [Hortonworks Data Platform (HDP)](https://www.cloudera.com/products/hdp.html), die von [Cloudera](https://www.cloudera.com/) angeboten wird.  Konkret nutzen die Kursteilnehmer [HDP Sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html), eine vereinfachte, leicht zu handhabende Version der Plattform, die kostenlos ist und zum Lernen und Experimentieren gedacht ist.  Wenngleich in diesem Kurs entweder Windows- oder Linux-VMs (virtuelle Computer) mit bereitgestellter HDP Sandbox verwendet werden können, wird in diesem Artikel gezeigt, wie Windows verwendet wird.

Ein weiterer interessanter Aspekt dieses Labs ist, dass wir HDP Sandbox auf den Lab-VMs unter Verwendung von [Docker](https://www.docker.com/)-Containern bereitstellen.  Jeder Docker-Container bietet eine eigene isolierte Umgebung, in der Softwareanwendungen ausgeführt werden können.  Vom Konzept her sind Docker-Container wie geschachtelte VMs und können zur einfachen Bereitstellung und Ausführung einer Vielzahl von Softwareanwendungen verwendet werden, die auf Containerimages basieren, die auf [Docker Hub](https://www.docker.com/products/docker-hub) angeboten werden.  Das Bereitstellungsskript von Cloudera für HDP Sandbox ruft automatisch per Pull das [Docker-Image von HDP Sandbox 3.0.1](https://hub.docker.com/r/hortonworks/sandbox-hdp) vom Docker Hub ab und führt zwei Docker-Container aus:
  - sandbox-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten dieses Labs benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie im [Tutorial zum Einrichten eines Lab-Kontos](tutorial-setup-lab-account.md).  Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen für das Lab-Konto, die in der nachfolgenden Tabelle beschrieben werden. Weitere Informationen zum Aktivieren von Marketplace-Images finden Sie unter [Angeben von für Lab-Ersteller verfügbaren Marketplace-Images](./specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| ------------------- | ------------ |
|Marketplace-Image| Aktivieren Sie das Windows 10 Pro-Image zur Verwendung in Ihrem Lab-Konto.|

### <a name="lab-settings"></a>Lab-Einstellungen

Verwenden Sie beim Einrichten eines Classroom-Labs die Einstellungen in der unten stehenden Tabelle.  Weitere Informationen zum Erstellen eines Classroom-Labs finden Sie im Tutorial [Einrichten eines Classroom-Labs](tutorial-setup-classroom-lab.md).

| Lab-Einstellungen | Wert/Anweisungen |
| ------------ | ------------------ |
|VM-Größe| Mittel (geschachtelte Virtualisierung). Diese VM-Größe eignet sich am besten für relationale Datenbanken, Zwischenspeicherung im Arbeitsspeicher und Analysen.  Sie unterstützt auch die geschachtelte Virtualisierung.|  
|VM-Image| Windows 10 Pro|

> [!NOTE] 
> Wir müssen „Mittel (geschachtelte Virtualisierung)“ verwenden, da die Bereitstellung von HDP Sandbox mithilfe von Docker Folgendes erfordert:
>   - Windows Hyper-V mit geschachtelter Virtualisierung
>   - Mindestens 10 GB RAM

## <a name="template-machine-configuration"></a>Konfiguration des Vorlagencomputers

Zum Einrichten der Vorlagen-VM erfolgen folgende Aktionen:
- Installieren von Docker
- Bereitstellen von HDP Sandbox
- Verwenden von PowerShell und Windows Taskplaner, um die Docker-Container automatisch zu starten

### <a name="install-docker"></a>Installieren von Docker

Die Schritte in diesem Abschnitt basieren auf den [Anweisungen von Cloudera für die Bereitstellung mit Docker-Containern](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Um Docker-Container verwenden zu können, müssen Sie Docker Desktop zunächst auf der Vorlagen-VM installieren:

1. Folgen Sie den Schritten im Abschnitt [Voraussetzungen](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites), um [Docker für Windows](https://docs.docker.com/docker-for-windows/install/) zu installieren. 

    > [!IMPORTANT] 
    > Achten Sie darauf, dass die Konfigurationsoption **Windows-Container statt Linux-Container verwenden** nicht aktiviert ist.

1. Stellen Sie sicher, dass **Windows-Container und Hyper-V-Features** aktiviert sind.
   ![Windows-Features aktivieren oder deaktivieren](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Folgen Sie den Schritten im Abschnitt [Arbeitsspeicher für Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows), um die Arbeitsspeicherkonfiguration von Docker zu konfigurieren.

    > [!WARNING]
    > Wenn Sie bei der Installation von Docker versehentlich die Option **Windows-Container statt Linux-Container verwenden** aktivieren, werden die Konfigurationseinstellungen für den Arbeitsspeicher nicht angezeigt.  Um dies zu beheben, können Sie auf Linux-Container umstellen, indem Sie [auf der Windows-Taskleiste auf das Docker-Symbol klicken](https://docs.docker.com/docker-for-windows/#docker-settings-dialog). Wenn sich das Menü „Docker Desktop“ öffnet, wählen Sie **Auf Linux-Container umstellen** aus.
 
### <a name="deploy-hdp-sandbox"></a>Bereitstellen von HDP Sandbox

In diesem Abschnitt stellen Sie HDP Sandbox bereit und greifen anschließend im Browser auf HDP Sandbox zu.

1. Vergewissern Sie sich, dass Sie [Git Bash](https://gitforwindows.org/) wie im Abschnitt [Voraussetzungen](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) der Anleitung aufgeführt installiert haben, da dies für die nächsten Schritte empfohlen wird.

1. Führen Sie unter Befolgung der [ Bereitstellungs- und Installationsanleitung von Cloudera für Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html) die Schritte in den folgenden Abschnitten aus:
   
   -    Bereitstellen von HDP Sandbox
   -    Überprüfen von HDP Sandbox

    > [!WARNING] 
    > Wenn Sie die neueste ZIP-Datei für HDP herunterladen, stellen Sie sicher, dass Sie *nicht* die ZIP-Datei in einem Verzeichnispfad mit Leerzeichen speichern.

    > [!NOTE] 
    > Wenn Sie während der Bereitstellung eine Ausnahme mit der Meldung **Laufwerk nicht freigegeben** erhalten, müssen Sie Ihr Laufwerk C für Docker freigeben, damit die Linux-Container von HDP auf lokale Windows-Dateien zugreifen können.  Um dieses Problem zu beheben, [klicken Sie auf der Windows-Taskleiste auf das Docker-Symbol](https://docs.docker.com/docker-for-windows/#docker-settings-dialog), um das Menü „Docker Desktop“ zu öffnen und **Einstellungen** auszuwählen.  Wählen Sie im geöffneten Dialogfeld **Docker-Einstellungen** die Option **Ressourcen > Dateifreigabe** aus, und aktivieren Sie Laufwerk **C**.  Sie können dann die Schritte wiederholen, um HDP Sandbox bereitzustellen.

1. Sobald die Docker-Container für HDP Sandbox bereitgestellt sind und ausgeführt werden, können Sie auf die Umgebung zugreifen. Starten Sie dazu Ihren Browser, und befolgen Sie die Anweisungen von Cloudera zum Öffnen der [Begrüßungsseite der Sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) und zum Starten des HDP-Dashboards.

    > [!NOTE] 
    > Diese Anweisungen gehen davon aus, dass Sie zuerst die lokale IP-Adresse der Sandboxumgebung zu sandbox-hdp.hortonworks.com in der Hostdatei auf Ihrer Vorlagen-VM zugeordnet haben.  Wenn Sie diese Zuordnung **nicht** vornehmen, können Sie auf die Sandbox-Begrüßungsseite zugreifen, indem Sie zu `http://localhost:8080` navigieren.

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Automatisches Starten von Docker-Containern, wenn sich Kursteilnehmer anmelden

Um Kursteilnehmern eine leicht zu bedienende Oberfläche zur Verfügung zu stellen, verwenden wir ein PowerShell-Skript, das die folgenden Schritte automatisch ausführt:
  - Die Docker-Container von HDP Sandbox starten, wenn ein Kursteilnehmer seine Lab-VM startet und eine Verbindung damit herstellt
  - Den Browser öffnen und zur Begrüßungsseite der Sandbox navigieren
Außerdem verwenden wir den Windows Taskplaner, um dieses Skript automatisch auszuführen, wenn sich ein Kursteilnehmer bei seiner VM anmeldet.
Führen Sie dazu die folgenden Schritte aus: [Schreiben eines Skripts für die Big Data-Analyse](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Kostenschätzung

Das folgende Beispiel dient der Einschätzung der Kosten des Labs.

Für einen Kurs mit 25 Teilnehmern, 20 planmäßigen Kursstunden und 10 Stunden Hausaufgaben bzw. Arbeitsaufträgen entstünden folgende Kosten für das Lab:
  - 25 Kursteilnehmer x (20 + 10) Stunden x 55 Lab-Einheiten x 0,01 USD pro Stunde = 412,50 USD

Weitere Informationen zu den Preisen finden Sie unter [Azure Lab Services-Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Zusammenfassung

In diesem Artikel wurden Sie durch die nötigen Schritte zum Erstellen eines Labs für einen Kurs für die Analyse von Big Data geführt, in dem mit Docker bereitgestellte Hortonworks Data Platform verwendet wird.  Die Einrichtung für diesen Kurstyp kann für ähnliche Datenanalysekurse verwendet werden.  Diese Einrichtung kann auch für andere Arten von Kursen gelten, die Docker für die Bereitstellung verwenden.

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Erstellen und Verwalten einer Vorlage](how-to-create-manage-template.md)
- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)