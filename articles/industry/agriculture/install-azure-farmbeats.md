---
title: Installieren von Azure FarmBeats
description: In diesem Artikel wird beschrieben, wie Sie Azure FarmBeats in Ihrem Azure-Abonnement installieren.
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 5863dcc20fb13f1bb203c68ad168655371130601
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674559"
---
# <a name="install-azure-farmbeats"></a>Installieren von Azure FarmBeats

In diesem Artikel wird beschrieben, wie Sie Azure FarmBeats in Ihrem Azure-Abonnement installieren.

Azure FarmBeats ist ein Business-to-Business-Angebot, das in Azure Marketplace verfügbar ist. Es ermöglicht die Aggregation von landwirtschaftlichen Datasets über mehrere Anbieter hinweg sowie die Generierung von verwertbaren Erkenntnissen. Azure FarmBeats erreicht dies, indem es Ihnen ermöglicht, Modelle der künstlichen Intelligenz (KI) oder des maschinellen Lernens (ML) auf der Basis von zusammengeführten Datasets zu erstellen. Die zwei Hauptkomponenten von Azure FarmBeats sind:

- **Datenhub:** Eine API-Ebene, die eine anbieterübergreifende Aggregation, Normalisierung und Kontextualisierung verschiedener Datasets ermöglicht, die landwirtschaftliche Daten enthalten.

- **Accelerator:** Eine Webanwendung, die auf dem Datenhub aufsetzt. Es unterstützt Ihre Modellentwicklung und -visualisierung. Der Accelerator nutzt Azure FarmBeats-APIs, um die Visualisierung von erfassten Sensordaten in Form von Diagrammen und die Visualisierung der Modellausgabe als Karten zu veranschaulichen.

## <a name="general-information"></a>Allgemeine Informationen

### <a name="components-installed"></a>Installierte Komponenten

Wenn Sie Azure FarmBeats installieren, werden die folgenden Ressourcen in Ihrem Azure-Abonnement zur Verfügung gestellt:

| Installierte Azure-Ressourcen  | Azure FarmBeats-Komponente  |
|---------|---------|
| Application Insights   |      Datenhub und Accelerator      |
| App Service     |     Datenhub und Accelerator     |
| App Service-Plan   | Datenhub und Accelerator  |
| APIConnection    |  Datenhub       |
| Azure Cache for Redis       | Datenhub      |
| Azure Cosmos DB   |  Datenhub       |
| Azure Data Factory V2       |     Datenhub und Accelerator      |
| Azure Batch-Konto    | Datenhub   |
| Azure-Schlüsseltresor |  Datenhub und Accelerator        |
| Azure Maps-Konto       |     Accelerator    |
| Event Hub-Namespace    |     Datenhub      |
| Logik-App      |  Datenhub       |
| Speicherkonto      |     Datenhub und Accelerator      |
| Time Series Insights     |    Datenhub    |

### <a name="costs-incurred"></a>Angefallene Kosten

Die Kosten für Azure FarmBeats stellen eine Aggregation der Kosten der zugrunde liegenden Azure-Dienste dar. Preise zu Azure-Diensten können mit dem [Preisrechner](https://azure.microsoft.com/pricing/calculator) berechnet werden. Die tatsächlichen Kosten für die gesamte Installation variieren je nach Nutzung. Die stationären Kosten für die beiden Komponenten sind:

- Datenhub – weniger als 10 USD pro Tag
- Accelerator – weniger als 2 USD pro Tag

### <a name="regions-supported"></a>Unterstützte Regionen

Derzeit wird Azure FarmBeats in öffentlichen Cloudumgebungen in den folgenden Regionen unterstützt:

- Australien (Osten)
- USA (Mitte)
- East US
- USA (Ost) 2
- USA (Westen)
- USA, Westen 2
- Nordeuropa
- Europa, Westen
- Asien, Osten
- Asien, Südosten

### <a name="time-taken"></a>Benötigte Zeit

Das gesamte Setup von Azure FarmBeats, einschließlich der Vorbereitung und Installation, wird weniger als eine Stunde dauern.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit der eigentlichen Installation von Azure FarmBeats beginnen, müssen Sie die folgenden Schritte durchführen:

### <a name="verify-permissions"></a>Überprüfen von Berechtigungen

Sie benötigen die folgenden Berechtigungen im Azure-Mandanten, um Azure FarmBeats zu installieren:

- Mandant – Ersteller der AAD-App
- Abonnement – Besitzer
- Ressourcengruppe, in der FarmBeats installiert wird – Besitzer

Die ersten beiden Berechtigungen sind zum [Erstellen der AAD-Anwendung](#create-an-aad-application) erforderlich. Falls erforderlich, können Sie jemanden mit den entsprechenden Berechtigungen zum Erstellen der AAD-Anwendung beauftragen.

Die Person, die die FarmBeats-Installation vom Marketplace aus ausführt, muss ein Besitzer der Ressourcengruppe sein, in der FarmBeats installiert wird. Bei Abonnementbesitzern erfolgt dies automatisch, wenn die Ressourcengruppe erstellt wird. Erstellen Sie die Ressourcengruppe vorab für andere, und bitten Sie den Besitzer des Abonnements, Sie zu einem Besitzer der Ressourcengruppe zu machen.

Sie können Ihre Zugriffsberechtigungen im Azure-Portal überprüfen, indem Sie die Anweisungen zur [rollenbasierten Zugriffssteuerung von Azure](../../role-based-access-control/check-access.md) befolgen.

### <a name="decide-subscription-and-region"></a>Auswählen des Abonnements und der Region

Sie benötigen die Azure-Abonnement-ID und die Region, in der Sie Azure FarmBeats installieren möchten. Wählen Sie eine der Regionen aus, die im Abschnitt [Unterstützte Regionen](#regions-supported) aufgelistet sind.

Notieren Sie sich die **Azure-Abonnement-ID** und die **Azure-Region** .

### <a name="create-an-aad-application"></a>Erstellen einer AAD-Anwendung

Azure FarmBeats erfordert die Erstellung und Registrierung der Anwendung Azure Active Directory. Um das AAD-Erstellungsskript erfolgreich auszuführen, sind folgende Berechtigungen erforderlich:

- Mandant – Ersteller der AAD-App
- Abonnement – Besitzer

Führen Sie die folgenden Schritte in einer Cloud Shell-Instanz unter Verwendung der PowerShell-Umgebung aus. Benutzer, die sich erstmalig anmelden, werden aufgefordert, ein Abonnement auszuwählen und ein Speicherkonto zu erstellen. Schließen Sie das Setup wie vorgegeben ab.

1. Laden Sie das [AAD-App-Generatorskript](https://aka.ms/FarmBeatsAADScript) herunter.

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Standardmäßig wird die Datei in Ihr Basisverzeichnis heruntergeladen. Navigieren Sie zum Verzeichnis.

    ```azurepowershell-interactive
        cd
    ```

3. Starten Sie das AAD-Skript.

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Das Skript fragt nach den folgenden drei Eingaben:

    - **Name der FarmBeats-Website** : Das eindeutige URL-Präfix für Ihre FarmBeats-Webanwendung. Falls das Präfix bereits verwendet wird, endet das Skript mit einem Fehler. Nach der Installation kann über „https://\<FarmBeats-website-name>.azurewebsites.net“ auf Ihre FarmBeats-Bereitstellung zugegriffen werden. Die Swagger-APIs befinden sich unter „https://\<FarmBeats-website-name>-api.azurewebsites.net“.

    - **Azure-Anmelde-ID** : Geben Sie die Azure-Anmelde-ID für den Benutzer an, den Sie als Administrator von FarmBeats hinzufügen möchten. Dieser Benutzer kann dann anderen Benutzern Zugriff auf FarmBeats-Webanwendungen gewähren. Die Anmelde-ID hat im Allgemeinen die Form john.doe@domain.com. Der Azure-UPN wird ebenfalls unterstützt.

    - **Abonnement-ID** : Dies ist die ID des Abonnements, in dem Sie Azure-FarmBeats installieren möchten.

5. Die Ausführung des AAD-Skripts dauert etwa 2 Minuten und gibt die Werte sowohl auf dem Bildschirm als auch in eine JSON-Datei im gleichen Verzeichnis aus. Wenn Sie das Skript von jemand anderem haben ausführen lassen, bitten Sie diese Person, die Ausgabe mit Ihnen zu teilen.

### <a name="create-sentinel-account"></a>Erstellen eines Sentinel-Kontos

Ihr Azure FarmBeats-Setup ermöglicht es Ihnen, Satellitenbilder von der [Sentinel-2](https://scihub.copernicus.eu/)-Satellitenmission der Europäischen Weltraumbehörde für Ihren landwirtschaftlichen Betrieb zu erhalten. Zum Konfigurieren dieses Setups benötigen Sie ein Sentinel-Konto.

Führen Sie folgende Schritte durch, um ein kostenloses Sentinel-Konto zu erstellen:

1. Wechseln Sie zur offiziellen [Registrierungsseite](https://aka.ms/SentinelRegistration).
2. Stellen Sie die erforderlichen Angaben (Vorname, Nachname, Benutzername, Kennwort und E-Mail-ID) bereit und füllen Sie das Formular aus.
3. Ein Link für die Überprüfung wird an die registrierte E-Mail-ID gesendet. Wählen Sie den in der E-Mail angegebenen Link und schließen Sie die Überprüfung ab.

Der Registrierungsvorgang ist abgeschlossen. Notieren Sie sich Ihren **Sentinel-Benutzernamen** und Ihr **Sentinel-Kennwort** , sobald die Überprüfung ebenfalls abgeschlossen ist.

## <a name="install"></a>Installieren

Sie können nun FarmBeats installieren. Folgen Sie den Schritten unten, um die Installation zu starten:

1. Melden Sie sich beim Azure-Portal an. Wählen Sie oben rechts Ihr Konto aus, und wechseln Sie zum Azure AD-Mandanten, in dem Sie Azure FarmBeats installieren möchten.

2. Wechseln Sie innerhalb des Portals zum Azure Marketplace und suchen Sie dort nach **Azure FarmBeats** .

3. Es wird ein neues Fenster mit einer Übersicht über Azure FarmBeats angezeigt. Klicken Sie auf **Erstellen** .

4. Es wird ein neues Fenster angezeigt. Schließen Sie den Registrierungsvorgang ab, indem Sie das richtige Abonnement, die richtige Ressourcengruppe und den richtigen Speicherort auswählen, an dem Sie Azure FarmBeats installieren möchten.

5. Geben Sie im Abschnitt für die **FarmBeats-Dienstwarnungen** die E-Mail-Adresse an, an die auf Azure FarmBeats bezogene Dienstmitteilungen gesendet werden sollen. Wählen Sie unten auf der Seite **Weiter** aus, um zur Registerkarte mit den **Abhängigkeiten** zu wechseln.

    ![Registerkarte „Grundlagen“](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Kopieren Sie die einzelnen Einträge aus der Ausgabe des [AAD-Skripts](#create-an-aad-application) in die Eingaben im AAD-Anwendungsabschnitt.

7. Geben Sie den Benutzernamen und das Kennwort für das [Sentinel-Konto](#create-sentinel-account) im Abschnitt für das Sentinel-Konto ein. Klicken Sie auf **Weiter** , um zur Registerkarte **Überprüfen + erstellen** zu gelangen.

    ![Registerkarte „Abhängigkeiten“](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Sobald die eingegebenen Daten bestätigt sind, wählen Sie **OK** aus. Die Seite „Nutzungsbedingungen“ wird angezeigt. Überprüfen Sie die Bestimmungen, und wählen Sie **Erstellen** aus, um die Installation zu starten. Sie werden zu der Seite umgeleitet, auf der Sie den Installationsfortschritt verfolgen können.

Nachdem die Installation abgeschlossen ist, können Sie die Installation überprüfen und das FarmBeats-Portal verwenden, indem Sie zu dem Websitenamen navigieren, den Sie während der Installation angegeben haben: https://\<FarmBeats-website-name>.azurewebsites.net. Sie sollten die FarmBeats-Benutzeroberfläche mit einer Option zum Erstellen von landwirtschaftlichen Betrieben sehen.

Der **Datenhub** befindet sich unter „https://\<FarmBeats-website-name>-api.azurewebsites.net/swagger“. Hier werden die verschiedenen FarmBeats-API-Objekte angezeigt und REST-Vorgänge für die APIs durchgeführt.

## <a name="upgrade"></a>Aktualisieren

Führen Sie die folgenden Schritte in einer Cloud Shell-Instanz unter Verwendung der PowerShell-Umgebung aus, um ein Upgrade von FarmBeats auf die neueste Version durchzuführen. Der Benutzer muss der Besitzer des Abonnements sein, in dem FarmBeats installiert ist.

Benutzer, die sich erstmalig anmelden, werden aufgefordert, ein Abonnement auszuwählen und ein Speicherkonto zu erstellen. Schließen Sie das Setup wie vorgegeben ab.

1. Herunterladen des [Upgradeskripts](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Standardmäßig wird die Datei in Ihr Basisverzeichnis heruntergeladen. Navigieren Sie zum Verzeichnis.

    ```azurepowershell-interactive
        cd
    ```

3. Ausführen des Upgradeskripts

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Der Pfad zur Datei „input.json“ ist optional. Wenn keine Angabe erfolgt, fragt das Skript nach allen erforderlichen Eingaben. Das Upgrade sollte in etwa 30 Minuten abgeschlossen sein.

## <a name="uninstall"></a>Deinstallieren

Führen Sie die folgenden Schritte aus, um den Azure FarmBeats-Datenhub oder -Accelerator zu deinstallieren:

1. Melden Sie sich beim Azure-Portal an und **löschen Sie die Ressourcengruppen** , in denen diese Komponenten installiert sind.

2. Wechseln Sie zu Azure Active Directory und löschen Sie die mit der Azure FarmBeats-Installation verknüpfte **Azure AD-Anwendung** .

## <a name="next-steps"></a>Nächste Schritte

Sie haben erfahren, wie Sie Azure FarmBeats in Ihrem Azure-Abonnement installieren. Erfahren Sie, wie Sie Benutzer zu Ihrer Azure RemoteApp-Sammlung [hinzufügen](manage-users-in-azure-farmbeats.md#manage-users).