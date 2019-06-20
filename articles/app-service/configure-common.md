---
title: Konfigurieren von Apps im Portal – Azure App Service
description: Erfahren Sie, wie Sie allgemeine Einstellungen für eine App Service-App im Azure-Portal konfigurieren.
keywords: Azure App Service, Web-App, App-Einstellungen, Umgebungsvariablen
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957565"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurieren einer App Service-App im Azure-Portal

In diesem Thema wird erläutert, wie Sie allgemeine Einstellungen für Web-Apps, ein mobiles Back-End oder eine API-App über das [Azure-Portal] konfigurieren.

## <a name="configure-app-settings"></a>Konfigurieren von App-Einstellungen

In App Service verwenden Sie App-Einstellungen wie Umgebungsvariablen. Navigieren Sie im [Azure-Portal] zur Verwaltungsseite Ihrer App. Klicken Sie im linken Menü der App auf **Konfiguration** > **Anwendungseinstellungen**.

![Anwendungseinstellungen](./media/configure-common/open-ui.png)

Für ASP.NET- und ASP.NET Core-Entwickler entspricht die Einstellung von App-Einstellungen in App Service der Einstellung in `<appSettings>` in *Web.config*, aber die Werte in App Service überschreiben die in *Web.config*. Sie können Entwicklungseinstellungen (z.B. das lokale MySQL-Kennwort) in *Web.config* beibehalten, aber Produktionsgeheimnisse (z.B. das Azure MySQL-Datenbank-Kennwort) sicher in App Service. Der gleiche Code verwendet Ihre Entwicklungseinstellungen, wenn Sie lokal debuggen, und verwendet Ihre Produktionsgeheimnisse, wenn sie in Azure bereitgestellt werden.

Andere Sprachstapel erhalten die App-Einstellungen ebenfalls als Umgebungsvariablen zur Laufzeit. Sprachstapelspezifische Schritte finden Sie unter:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Benutzerdefinierte Container](containers/configure-custom-container.md#configure-environment-variables)

App-Einstellungen werden bei der Speicherung stets verschlüsselt (encrypted-at-rest).

> [!NOTE]
> App-Einstellungen können auch über [Key Vault](/azure/key-vault/) mithilfe von [Key Vault-Referenzen](app-service-key-vault-references.md) aufgelöst werden.

### <a name="show-hidden-values"></a>Anzeigen ausgeblendeter Werte

Standardmäßig werden Werte für App-Einstellungen aus Sicherheitsgründen im Portal ausgeblendet. Um einen ausgeblendeten Wert einer App-Einstellung anzuzeigen, klicken Sie auf das **Wert**-Feld dieser Einstellung. Um die Werte aller App-Einstellungen anzuzeigen, klicken Sie auf die **Wert anzeigen**-Schaltfläche.

### <a name="add-or-edit"></a>Hinzufügen oder Bearbeiten

Klicken Sie zum Hinzufügen einer neuen App-Einstellung auf **Neue Anwendungseinstellung**. Im Dialogfeld können Sie [die Einstellung im aktuellen Slot beibehalten](deploy-staging-slots.md#which-settings-are-swapped).

Um eine Einstellung zu bearbeiten, klicken Sie auf die **Bearbeiten**-Schaltfläche auf der rechten Seite.

Klicken Sie abschließend auf **Aktualisieren**. Vergessen Sie nicht, auf **Speichern** zu klicken, wenn Sie wieder auf der Seite **Konfiguration** sind.

> [!NOTE]
> In einem standardmäßigen Linux-Container oder einem benutzerdefinierten Linux-Container muss jede geschachtelte JSON-Schlüsselstruktur im App-Einstellungsnamen wie `ApplicationInsights:InstrumentationKey` in App Service als `ApplicationInsights__InstrumentationKey` für den Schlüsselnamen konfiguriert werden. Mit anderen Worten: Jedes `:` muss durch `__` (doppelter Unterstrich) ersetzt werden.
>

### <a name="edit-in-bulk"></a>Bearbeiten im Massenvorgang

Um App-Einstellungen in einem Massenvorgang hinzuzufügen oder zu bearbeiten, klicken Sie auf die **Erweiterte Bearbeitung**-Schaltfläche. Klicken Sie abschließend auf **Aktualisieren**. Vergessen Sie nicht, auf **Speichern** zu klicken, wenn Sie wieder auf der Seite **Konfiguration** sind.

App-Einstellungen haben folgende JSON-Formatierung:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Konfigurieren von Verbindungszeichenfolgen

Navigieren Sie im [Azure-Portal] zur Verwaltungsseite der App. Klicken Sie im linken Menü der App auf **Konfiguration** > **Anwendungseinstellungen**.

![Anwendungseinstellungen](./media/configure-common/open-ui.png)

Für ASP.NET- und ASP.NET Core-Entwickler entspricht die Einstellung von Verbindungszeichenfolgen in App Service der Einstellung in `<connectionStrings>` in *Web.config*, aber die Werte, die Sie in App Service festlegen, überschreiben die in *Web.config*. Sie können Entwicklungseinstellungen (z.B. eine Datenbankdatei) in *Web.config* und Produktionsgeheimnisse (z.B. SQL-Datenbank-Anmeldeinformationen) sicher in App Service beibehalten. Der gleiche Code verwendet Ihre Entwicklungseinstellungen, wenn Sie lokal debuggen, und verwendet Ihre Produktionsgeheimnisse, wenn sie in Azure bereitgestellt werden.

Für andere Sprachstapel sollten Sie stattdessen besser [App-Einstellungen](#configure-app-settings) verwenden, da Verbindungszeichenfolgen spezielle Formatierung in den Variablenschlüsseln erfordern, um auf die Werte zuzugreifen. Hier ist jedoch eine Ausnahme: Bestimmte Azure-Datenbanktypen werden zusammen mit der App gesichert, wenn Sie ihre Verbindungszeichenfolgen in Ihrer App konfigurieren. Weitere Informationen finden Sie unter [Was wird gesichert?](manage-backup.md#what-gets-backed-up). Wenn Sie diese automatisierte Sicherung nicht benötigen, verwenden Sie App-Einstellungen.

Zur Laufzeit stehen Verbindungszeichenfolgen als Umgebungsvariablen zur Verfügung, wobei sie mit Präfixen für die folgenden Verbindungstypen versehen werden:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL-Datenbank: `SQLAZURECONNSTR_`
* Benutzerdefiniert: `CUSTOMCONNSTR_`

Wenn beispielsweise eine MySql-Verbindungszeichenfolge *connectionstring1* heißt, kann sie über die Umgebungsvariable `MYSQLCONNSTR_connectionString1` aufgerufen werden. Sprachstapelspezifische Schritte finden Sie unter:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Benutzerdefinierte Container](containers/configure-custom-container.md#configure-environment-variables)

Verbindungszeichenfolgen werden bei der Speicherung stets verschlüsselt (encrypted-at-rest).

> [!NOTE]
> Verbindungszeichenfolgen können auch über [Key Vault](/azure/key-vault/) mithilfe von [Key Vault-Referenzen](app-service-key-vault-references.md) aufgelöst werden.

### <a name="show-hidden-values"></a>Anzeigen ausgeblendeter Werte

Standardmäßig werden Werte für Verbindungszeichenfolgen aus Sicherheitsgründen im Portal ausgeblendet. Um einen ausgeblendeten Wert einer Verbindungszeichenfolge anzuzeigen, klicken Sie einfach auf das **Wert**-Feld dieser Zeichenfolge. Um die Werte aller Verbindungszeichenfolgen anzuzeigen, klicken Sie auf die **Wert anzeigen**-Schaltfläche.

### <a name="add-or-edit"></a>Hinzufügen oder Bearbeiten

Um eine neue Verbindungszeichenfolge hinzuzufügen, klicken Sie auf **Neue Verbindungszeichenfolge**. Im Dialogfeld können Sie [die Verbindungszeichenfolge im aktuellen Slot beibehalten](deploy-staging-slots.md#which-settings-are-swapped).

Um eine Einstellung zu bearbeiten, klicken Sie auf die **Bearbeiten**-Schaltfläche auf der rechten Seite.

Klicken Sie abschließend auf **Aktualisieren**. Vergessen Sie nicht, auf **Speichern** zu klicken, wenn Sie wieder auf der Seite **Konfiguration** sind.

### <a name="edit-in-bulk"></a>Bearbeiten im Massenvorgang

Um Verbindungszeichenfolgen in einem Massenvorgang hinzuzufügen oder zu bearbeiten, klicken Sie auf die **Erweiterte Bearbeitung**-Schaltfläche. Klicken Sie abschließend auf **Aktualisieren**. Vergessen Sie nicht, auf **Speichern** zu klicken, wenn Sie wieder auf der Seite **Konfiguration** sind.

Verbindungszeichenfolgen haben folgende JSON-Formatierung:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Konfigurieren allgemeiner Einstellungen

Navigieren Sie im [Azure-Portal] zur Verwaltungsseite der App. Klicken Sie im linken Menü der App auf **Konfiguration** > **Anwendungseinstellungen**.

![Allgemeine Einstellungen](./media/configure-common/open-general.png)

Hier können Sie einige allgemeine Einstellungen für die App konfigurieren. Einige Einstellungen erfordern [zentrales Hochskalieren auf höhere Tarife](web-sites-scale.md).

- **Stapeleinstellungen**: Der Softwarestapel zum Ausführen der App, einschließlich der Sprach- und SDK-Version. Für Linux-Apps und benutzerdefinierte Container-Apps können Sie auch optional einen Startbefehl oder eine Startdatei festlegen.
- **Plattformeinstellungen**: Ermöglicht das Konfigurieren von Einstellungen für die Hostingplattform, einschließlich:
    - **Bitanzahl**: 32-Bit oder 64-Bit.
    - **WebSocket-Protokoll**: Z.B. für [ASP.NET SignalR] oder [socket.io](https://socket.io/).
    - **Always On**: Lassen Sie die App auch dann geladen, wenn kein Datenverkehr stattfindet. Sie müssen sie für fortlaufende WebJobs oder WebJobs, die mithilfe eines CRON-Ausdrucks ausgelöst werden, aktivieren.
    - **Verwalteter Pipelinemodus**: Der IIS-[Pipelinemodus]. Legen Sie ihn auf **Klassisch** fest, wenn Sie eine ältere App haben, die eine ältere Version von IIS erfordert.
    - **HTTP-Version**: Legen Sie die Einstellung auf **2.0** fest, um die Unterstützung für das [HTTPS/2](https://wikipedia.org/wiki/HTTP/2)-Protokoll zu aktivieren.
    > [!NOTE]
    > Die aktuellen Browser unterstützen das HTTP/2-Protokoll in der Regel nur über TLS, während unverschlüsselter Datenverkehr weiterhin HTTP/1.1 verwendet. Um sicherzustellen, dass sich Clientbrowser mit Ihrer App über HTTP/2 verbinden, erwerben Sie entweder [ein App Service Certificate](web-sites-purchase-ssl-web-site.md) für die benutzerdefinierte Domäne der App, oder [binden Sie ein Zertifikat eines Drittanbieters](app-service-web-tutorial-custom-ssl.md).
    - **ARR-Affinität**: In einer Bereitstellung mit mehreren Instanzen stellen Sie sicher, dass der Client für die Lebensdauer der Sitzung an die gleiche Instanz weitergeleitet wird. Sie können für zustandslose Anwendungen für diese Option **Aus** festlegen.
- **Debuggen**: Aktivieren Sie das Remotedebuggen für [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)-, [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)- oder [Node.js](containers/configure-language-nodejs.md#debug-remotely)-Apps. Diese Option wird nach 48 Stunden automatisch deaktiviert.
- **Eingehende Clientzertifikate**: erforderliche Clientzertifikate bei [gegenseitiger Authentifizierung](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurieren von Standarddokumenten

Diese Einstellung gilt nur für Windows-Apps.

Navigieren Sie im [Azure-Portal] zur Verwaltungsseite der App. Klicken Sie im linken Menü der App auf **Konfiguration** > **Standarddokumente**.

![Allgemeine Einstellungen](./media/configure-common/open-documents.png)

Das Standarddokument ist die Webseite, die an der Stamm-URL für eine Website angezeigt wird. Die erste übereinstimmende Datei in der Liste wird verwendet. Klicken Sie zum Hinzufügen eines neuen Standarddokuments auf **Neues Dokument**. Vergessen Sie das **Speichern** nicht.

Wenn die App Module verwendet, die auf Grundlage der URL weiterleiten, anstatt statischen Inhalt bereitzustellen, besteht keine Notwendigkeit für Standarddokumente.

## <a name="configure-path-mappings"></a>Konfigurieren von Pfadzuordnungen

Navigieren Sie im [Azure-Portal] zur Verwaltungsseite der App. Klicken Sie im linken Menü der App auf **Konfiguration** > **Pfadzuordnungen**.

![Allgemeine Einstellungen](./media/configure-common/open-path.png)

Auf der Seite **Pfadzuordnungen** wird je nach BS-Typ Unterschiedliches angezeigt.

### <a name="windows-apps-uncontainerized"></a>Windows-Apps (nicht in Containern)

Für Windows-Apps können Sie die IIS-Handlerzuordnungen und virtuelle Anwendungen und Verzeichnisse anpassen.

Mit Handlerzuordnungen können Sie benutzerdefinierte Skriptprozessoren hinzufügen, die Anforderungen für bestimmte Dateierweiterungen verarbeiten. Um einen benutzerdefinierten Handler hinzuzufügen, klicken Sie auf **Neuer Handler**. Konfigurieren Sie den Handler folgendermaßen:

- **Erweiterung**. Die zu verarbeitende Dateierweiterung wie *\*.php* oder *handler.fcgi*.
- **Skriptprozessor**. Der absolute Pfad des Skriptprozessors zu Ihnen. Anforderungen für Dateien, die dieser Dateierweiterung entsprechen, werden vom Skriptprozessor verarbeitet. Verwenden Sie den Pfad `D:\home\site\wwwroot` , um auf das Stammverzeichnis Ihrer App zu verweisen.
- **Argumente**. Optionale Befehlszeilenargumente für den Skriptprozessor.

Für jede App ist der Standardstammpfad (`/`) `D:\home\site\wwwroot` zugeordnet, wo Ihr Code standardmäßig bereitgestellt wird. Wenn Ihr App-Stamm sich in einem anderen Ordner befindet oder Ihr Repository über mehrere Anwendungen verfügt, können Sie virtuelle Anwendungen und Verzeichnisse hier bearbeiten oder hinzufügen. Klicken Sie auf **Neue virtuelle Anwendung oder neues virtuelles Verzeichnis** .

Um die virtuellen Anwendungen und Verzeichnisse zu konfigurieren, geben Sie jedes virtuelle Verzeichnis und den zugehörigen physischen Pfad relativ zum Stammverzeichnis der Website (`D:\home`) an. Sie haben außerdem die Möglichkeit, mit dem Kontrollkästchen **Anwendung** ein virtuelles Verzeichnis als Anwendung zu markieren.

### <a name="containerized-apps"></a>Container-Apps

Sie können [für Ihre Container-Apps benutzerdefinierten Speicher hinzufügen](containers/how-to-serve-content-from-azure-storage.md). Zu den Container-Apps zählen alle Linux-Apps sowie die in App Service ausgeführten benutzerdefinierten Windows- und Linux-Container. Klicken Sie auf **Neue Azure-Speicherbereitstellung** , und konfigurieren Sie Ihren benutzerdefinierten Speicher wie folgt:

- **Name**: Neuer Anzeigename.
- **Konfigurationsoptionen**: **Einfach** oder **Erweitert**.
- **Speicherkonten**: Das Speicherkonto mit dem von Ihnen gewünschten Container.
- **Storage type** (Speichertyp): **Azure-Blobs** oder **Azure Files**.
  > [!NOTE]
  > Windows-Container-Apps unterstützen nur Azure Files.
- **Speichercontainer:** Für die Basiskonfiguration der von Ihnen gewünschte Container.
- **Freigabename**: Für die erweiterte Konfiguration der Dateifreigabename.
- **Zugriffsschlüssel**: Für die erweiterte Konfiguration der Zugriffsschlüssel.
- **Einbindungspfad**: Der absolute Pfad in Ihrem Container zum Bereitstellen des benutzerdefinierten Speichers.

Weitere Informationen finden Sie unter [Bereitstellen von Inhalt aus Azure Storage in App Service unter Linux](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurieren von Sprachstapeleinstellungen

Für Linux-Apps finden Sie Informationen unter:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurieren eines benutzerdefinierten Containers

Siehe [Konfigurieren eines benutzerdefinierten Linux-Containers für Azure App Service](containers/configure-custom-container.md).

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service]
- [Einrichten von Stagingumgebungen in Azure App Service]
- [Aktivieren von HTTPS für eine App in Azure App Service]
- [Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](troubleshoot-diagnostic-logs.md)
- [Skalieren einer App in Azure App Service]
- [Grundlagen der Überwachung in Azure App Service]
- [Ändern der „applicationHost.config“-Einstellungen mit „applicationHost.xdt“](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure-Portal]: https://portal.azure.com/
[Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Einrichten von Stagingumgebungen in Azure App Service]: ./deploy-staging-slots.md
[Aktivieren von HTTPS für eine App in Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Grundlagen der Überwachung in Azure App Service]: ./web-sites-monitor.md
[Pipelinemodus]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skalieren einer App in Azure App Service]: ./web-sites-scale.md
