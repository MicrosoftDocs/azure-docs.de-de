---
title: Konfigurieren von Apps im Portal
description: Erfahren Sie, wie Sie allgemeine Einstellungen für eine App Service-App im Azure-Portal konfigurieren. App-Einstellungen, Verbindungszeichenfolgen, Plattform, Sprachstapel, Container usw.
keywords: Azure App Service, Web-App, App-Einstellungen, Umgebungsvariablen
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
ms.openlocfilehash: 76cfefa3f104ecef69e28fecd1c37fc336b0ce8c
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854647"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Konfigurieren einer App Service-App im Azure-Portal

In diesem Thema wird erläutert, wie Sie allgemeine Einstellungen für Web-Apps, ein mobiles Back-End oder eine API-App über das [Azure-Portal] konfigurieren.

## <a name="configure-app-settings"></a>Konfigurieren von App-Einstellungen

In App Service handelt es sich bei App-Einstellungen um Variablen, die als Umgebungsvariablen an den Anwendungscode übergeben werden. Bei Linux-Apps und benutzerdefinierten Containern übergibt App Service mithilfe des Flags `--env` App-Einstellungen an den Container, um die Umgebungsvariable im Container festzulegen.

Suchen Sie im [Azure-Portal] die Option **App Services**, wählen Sie sie aus, und wählen Sie anschließend Ihre App aus. 

![Suchen nach App Services](./media/configure-common/search-for-app-services.png)

Wählen Sie im linken Menü der App **Konfiguration** > **Anwendungseinstellungen** aus.

![Anwendungseinstellungen](./media/configure-common/open-ui.png)

Für ASP.NET- und ASP.NET Core-Entwickler entspricht die Festlegung von App-Einstellungen in App Service der Festlegung in `<appSettings>` in *Web.config* oder *appsettings.json*, aber die Werte in App Service überschreiben die in *Web.config* oder *appsettings.json*. Sie können Entwicklungseinstellungen (z. B. das lokale MySQL-Kennwort) in *Web.config* oder *appsettings.json* und Produktionsgeheimnisse (z. B. das Azure MySQL-Datenbank-Kennwort) sicher in App Service speichern. Der gleiche Code verwendet Ihre Entwicklungseinstellungen, wenn Sie lokal debuggen, und verwendet Ihre Produktionsgeheimnisse, wenn sie in Azure bereitgestellt werden.

Andere Sprachstapel erhalten die App-Einstellungen ebenfalls als Umgebungsvariablen zur Laufzeit. Sprachstapelspezifische Schritte finden Sie unter:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Benutzerdefinierte Container](configure-custom-container.md#configure-environment-variables)

App-Einstellungen werden bei der Speicherung stets verschlüsselt (encrypted-at-rest).

> [!NOTE]
> App-Einstellungen können auch über [Key Vault](../key-vault/index.yml) mithilfe von [Key Vault-Referenzen](app-service-key-vault-references.md) aufgelöst werden.

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

### <a name="automate-app-settings-with-the-azure-cli"></a>Automatisieren von App-Einstellungen mit der Azure CLI

Sie können die Azure CLI verwenden, um Einstellungen über die Befehlszeile zu erstellen und zu verwalten.

- Weisen Sie einer Einstellung mit [az webapp config app settings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) einen Wert zu:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings <setting-name>="<value>"
    ```
        
    Ersetzen Sie `<setting-name>` durch den Namen der Einstellung und `<value>` durch den Wert, der ihr zugewiesen werden soll. Dieser Befehl erstellt die Einstellung, wenn diese noch nicht vorhanden ist.
    
- Zeigen Sie alle Einstellungen und deren Werte mit [az webapp config appsettings list](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_list) an:
    
    ```azurecli-interactive
    az webapp config appsettings list --name <app-name> --resource-group <resource-group-name>
    ```
    
- Entfernen Sie mindestens eine Einstellung mit [az webapp config app settings delete](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_delete):

    ```azurecli-interactive
    az webapp config appsettings delete --name <app-name> --resource-group <resource-group-name> --setting-names {<names>}
    ```
    
    Ersetzen Sie `<names>` durch eine durch Leerzeichen getrennte Liste von Einstellungsnamen.

## <a name="configure-connection-strings"></a>Konfigurieren von Verbindungszeichenfolgen

Suchen Sie im [Azure-Portal] die Option **App Services**, wählen Sie sie aus, und wählen Sie anschließend Ihre App aus. Wählen Sie im linken Menü der App **Konfiguration** > **Anwendungseinstellungen** aus.

![Anwendungseinstellungen](./media/configure-common/open-ui.png)

Für ASP.NET- und ASP.NET Core-Entwickler entspricht die Einstellung von Verbindungszeichenfolgen in App Service der Einstellung in `<connectionStrings>` in *Web.config*, aber die Werte, die Sie in App Service festlegen, überschreiben die in *Web.config*. Sie können Entwicklungseinstellungen (z. B. eine Datenbankdatei) in *Web.config* und Produktionsgeheimnisse (z. B. SQL-Datenbank-Anmeldeinformationen) sicher in App Service beibehalten. Der gleiche Code verwendet Ihre Entwicklungseinstellungen, wenn Sie lokal debuggen, und verwendet Ihre Produktionsgeheimnisse, wenn sie in Azure bereitgestellt werden.

Für andere Sprachstapel sollten Sie stattdessen besser [App-Einstellungen](#configure-app-settings) verwenden, da Verbindungszeichenfolgen spezielle Formatierung in den Variablenschlüsseln erfordern, um auf die Werte zuzugreifen. Hier ist jedoch eine Ausnahme: Bestimmte Azure-Datenbanktypen werden zusammen mit der App gesichert, wenn Sie ihre Verbindungszeichenfolgen in Ihrer App konfigurieren. Weitere Informationen finden Sie unter [Was wird gesichert?](manage-backup.md#what-gets-backed-up). Wenn Sie diese automatisierte Sicherung nicht benötigen, verwenden Sie App-Einstellungen.

Zur Laufzeit stehen Verbindungszeichenfolgen als Umgebungsvariablen zur Verfügung, wobei sie mit Präfixen für die folgenden Verbindungstypen versehen werden:

* SQLServer: `SQLCONNSTR_`  
* MySQL: `MYSQLCONNSTR_` 
* SQLAzure: `SQLAZURECONNSTR_` 
* Benutzerdefiniert: `CUSTOMCONNSTR_`
* PostgreSQL: `POSTGRESQLCONNSTR_`  

Wenn beispielsweise eine MySql-Verbindungszeichenfolge *connectionstring1* heißt, kann sie über die Umgebungsvariable `MYSQLCONNSTR_connectionString1` aufgerufen werden. Sprachstapelspezifische Schritte finden Sie unter:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Benutzerdefinierte Container](configure-custom-container.md#configure-environment-variables)

Verbindungszeichenfolgen werden bei der Speicherung stets verschlüsselt (encrypted-at-rest).

> [!NOTE]
> Verbindungszeichenfolgen können auch über [Key Vault](../key-vault/index.yml) mithilfe von [Key Vault-Referenzen](app-service-key-vault-references.md) aufgelöst werden.

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

Suchen Sie im [Azure-Portal] die Option **App Services**, wählen Sie sie aus, und wählen Sie anschließend Ihre App aus. Wählen Sie im linken Menü der App **Konfiguration** > **Allgemeine Einstellungen** aus.

![Allgemeine Einstellungen](./media/configure-common/open-general.png)

Hier können Sie einige allgemeine Einstellungen für die App konfigurieren. Einige Einstellungen erfordern das [Hochskalieren auf höhere Tarife](manage-scale-up.md).

- **Stapeleinstellungen**: Der Softwarestapel zum Ausführen der App, einschließlich der Sprach- und SDK-Version.

    Für Linux-Apps und benutzerdefinierte Container-Apps können Sie die Language Runtime-Version auswählen und einen optionalen **Startbefehl** oder eine Startbefehlsdatei festlegen.

    ![Allgemeine Einstellungen für Linux-Container](./media/configure-common/open-general-linux.png)

- **Plattformeinstellungen**: Ermöglicht das Konfigurieren von Einstellungen für die Hostingplattform, einschließlich:
    - **Bitanzahl**: 32-Bit oder 64-Bit.
    - **WebSocket-Protokoll**: Z.B. für [ASP.NET SignalR] oder [socket.io](https://socket.io/).
    - **Always On**: Hält die App auch dann geladen, wenn kein Datenverkehr stattfindet. Dies ist für fortlaufende WebJobs oder WebJobs, die mithilfe eines CRON-Ausdrucks ausgelöst werden, erforderlich.
      > [!NOTE]
      > Mit der Always On-Funktion sendet der Front-End-Lastenausgleich eine Anforderung an den Anwendungsstamm. Dieser Anwendungsendpunkt des App Service kann nicht konfiguriert werden.
    - **Verwalteter Pipelinemodus**: Der IIS-[Pipelinemodus]. Legen Sie ihn auf **Klassisch** fest, wenn Sie eine ältere App haben, die eine ältere Version von IIS erfordert.
    - **HTTP-Version**: Legen Sie die Einstellung auf **2.0** fest, um die Unterstützung für das [HTTPS/2](https://wikipedia.org/wiki/HTTP/2)-Protokoll zu aktivieren.
    > [!NOTE]
    > Die aktuellen Browser unterstützen das HTTP/2-Protokoll in der Regel nur über TLS, während unverschlüsselter Datenverkehr weiterhin HTTP/1.1 verwendet. Um sicherzustellen, dass Clientbrowser eine Verbindung mit Ihrer App mit HTTP/2 herstellen, sichern Sie Ihren benutzerdefinierten DNS-Namen. Weitere Informationen finden Sie unter [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS/SSL-Bindung in Azure App Service](configure-ssl-bindings.md).
    - **ARR-Affinität**: In einer Bereitstellung mit mehreren Instanzen stellen Sie sicher, dass der Client für die Lebensdauer der Sitzung an die gleiche Instanz weitergeleitet wird. Sie können für zustandslose Anwendungen für diese Option **Aus** festlegen.
- **Debuggen**: Aktivieren Sie das Remotedebuggen für [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug)-, [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)- oder [Node.js](configure-language-nodejs.md#debug-remotely)-Apps. Diese Option wird nach 48 Stunden automatisch deaktiviert.
- **Eingehende Clientzertifikate**: erforderliche Clientzertifikate bei [gegenseitiger Authentifizierung](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurieren von Standarddokumenten

Diese Einstellung gilt nur für Windows-Apps.

Suchen Sie im [Azure-Portal] die Option **App Services**, wählen Sie sie aus, und wählen Sie anschließend Ihre App aus. Wählen Sie im linken Menü der App **Konfiguration** > **Standarddokumente** aus.

![Standarddokumente](./media/configure-common/open-documents.png)

Das Standarddokument ist die Webseite, die an der Stamm-URL für eine Website angezeigt wird. Die erste übereinstimmende Datei in der Liste wird verwendet. Klicken Sie zum Hinzufügen eines neuen Standarddokuments auf **Neues Dokument**. Vergessen Sie das **Speichern** nicht.

Wenn die App Module verwendet, die auf Grundlage der URL weiterleiten, anstatt statischen Inhalt bereitzustellen, besteht keine Notwendigkeit für Standarddokumente.

## <a name="configure-path-mappings"></a>Konfigurieren von Pfadzuordnungen

Suchen Sie im [Azure-Portal] die Option **App Services**, wählen Sie sie aus, und wählen Sie anschließend Ihre App aus. Wählen Sie im linken Menü der App **Konfiguration** > **Pfadzuordnungen** aus.

![Pfadzuordnungen](./media/configure-common/open-path.png)

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

Sie können [für Ihre Container-Apps benutzerdefinierten Speicher hinzufügen](configure-connect-to-azure-storage.md). Zu den Container-Apps zählen alle Linux-Apps sowie die in App Service ausgeführten benutzerdefinierten Windows- und Linux-Container. Klicken Sie auf **Neue Azure-Speicherbereitstellung** , und konfigurieren Sie Ihren benutzerdefinierten Speicher wie folgt:

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

Weitere Informationen finden Sie unter [Konfigurieren von Azure Files in einem Windows-Container in App Service](configure-connect-to-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurieren von Sprachstapeleinstellungen

Für Linux-Apps finden Sie Informationen unter:

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurieren eines benutzerdefinierten Containers

Siehe [Konfigurieren eines benutzerdefinierten Linux-Containers für Azure App Service](configure-custom-container.md).

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service]
- [Einrichten von Stagingumgebungen in Azure App Service]
- [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS/SSL-Bindung in Azure App Service](configure-ssl-bindings.md)
- [Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](troubleshoot-diagnostic-logs.md)
- [Skalieren einer App in Azure App Service]
- [Grundlagen der Überwachung in Azure App Service]
- [Ändern der „applicationHost.config“-Einstellungen mit „applicationHost.xdt“](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure-Portal]: https://portal.azure.com/
[Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Einrichten von Stagingumgebungen in Azure App Service]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: ./web-sites-monitor.md
[Grundlagen der Überwachung in Azure App Service]: ./web-sites-monitor.md
[Pipelinemodus]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skalieren einer App in Azure App Service]: ./manage-scale-up.md
