---
title: 'Hinzufügen von Key Vault-Unterstützung zu Ihrem ASP.NET-Projekt mit Visual Studio: Azure Key Vault | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie einer ASP.NET- oder ASP.NET Core-Webanwendung Key Vault-Unterstützung hinzufügen.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure, devx-track-csharp
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 9c62534acdbfbff7fd4e718bad1f07a92c641626
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792394"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Hinzufügen von Key Vault zu Ihrer Webanwendung mithilfe der Option „Verbundene Dienste“ in Visual Studio

In diesem Tutorial erfahren Sie, wie Sie auf einfache Weise alles hinzufügen, was Sie zum Verwalten Ihrer Geheimnisse mit Azure Key Vault für Webprojekte in Visual Studio benötigen – ganz unabhängig davon, ob Sie ASP.NET Core oder einen beliebigen Typ von ASP.NET-Projekt verwenden. Mit dem Feature „Verbundene Dienste“ in Visual Studio werden alle NuGet-Pakete und Konfigurationseinstellungen, die für eine Verbindung mit Key Vault in Azure erforderlich sind, von Visual Studio automatisch hinzugefügt.

Ausführliche Informationen zu den Änderungen, die durch verbundene Dienste in Ihrem Projekt durchgeführt werden, um Key Vault zu aktivieren, finden Sie unter [Verbundener Key Vault-Dienst – Auswirkungen auf mein ASP.NET 4.7.1-Projekt](#how-your-aspnet-framework-project-is-modified) bzw. unter [Verbundener Key Vault-Dienst – Auswirkungen auf mein ASP.NET Core-Projekt](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Voraussetzungen

- **Ein Azure-Abonnement** . Falls Sie kein Abonnement besitzen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.
- **Visual Studio 2019, Version 16.3** oder höher. [Jetzt herunterladen](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)


## <a name="add-key-vault-support-to-your-project"></a>Hinzufügen von Key Vault-Unterstützung zu Ihrem Projekt

Vergewissern Sie sich zunächst, dass Sie bei Visual Studio angemeldet sind. Melden Sie sich mit dem gleichen Konto an, das Sie auch für Ihr Azure-Abonnement verwenden. Öffnen Sie dann ein Webprojekt (ASP.NET 4.7.1 oder höher oder ASP.NET Core 2.0), und führen Sie die folgenden Schritte aus:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, dem Sie die Key Vault-Unterstützung hinzufügen möchten, und klicken Sie dann auf **Hinzufügen** > **Verbundener Dienst** > **Hinzufügen** .
   Die Seite „Verbundener Dienst“ wird mit den Diensten angezeigt, die Sie dem Projekt hinzufügen können.
1. Wählen Sie im Menü der verfügbaren Dienste **Azure Key Vault** aus, und klicken Sie auf **Weiter** .

   ![Auswählen von „Azure Key Vault“](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. Wählen Sie das zu verwendende Abonnement und anschließend eine bereits vorhandene Key Vault-Instanz aus, und klicken Sie auf **Fertig stellen** . 

   ![Wählen Sie Ihr Abonnement aus.](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

Nun wird die Verbindung mit Key Vault hergestellt, und Sie können auf Ihre Geheimnisse im Code zugreifen. Die nächsten Schritte unterscheiden sich abhängig davon, ob Sie ASP.NET 4.7.1 oder ASP.NET Core verwenden.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Zugreifen auf Ihre Geheimnisse in Code (ASP.NET Core)

1. Öffnen Sie eine der Seitendateien (z. B. *Index.cshtml.cs* ), und schreiben Sie den folgenden Code:
   1. Schließen Sie mithilfe der folgenden using-Anweisung einen Verweis auf `Microsoft.Extensions.Configuration` ein:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Fügen Sie die Konfigurationsvariable hinzu.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Fügen Sie diesen Konstruktor hinzu, oder ersetzen Sie den vorhandenen Konstruktor durch Folgendes:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Aktualisieren Sie die `OnGet`-Methode. Ersetzen Sie den hier gezeigten Platzhalterwert durch den Namen des Geheimnisses, den Sie mit den oben angegebenen Befehlen erstellt haben.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. Um den Wert zur Laufzeit zu bestätigen, fügen Sie Code hinzu, um `ViewData["Message"]` für die *CSHTML* -Datei anzuzeigen, um den geheimen Schlüssel in einer Nachricht anzuzeigen.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Sie können die App lokal ausführen, um zu überprüfen, ob das Geheimnis erfolgreich aus dem Key Vault abgerufen wurde.

## <a name="access-your-secrets-aspnet"></a>Zugreifen auf Ihre Geheimnisse (ASP.NET)
Sie können die Konfiguration so einrichten, dass die Datei „web. config“ im `appSettings`-Element einen Dummywert enthält, der zur Laufzeit durch den Wert TRUE ersetzt wird. Sie können dann über die `ConfigurationManager.AppSettings`-Datenstruktur darauf zugreifen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt und dann auf Sie „NuGet-Pakete verwalten“. Suchen Sie in der Registerkarte „Durchsuchen“ nach [Microsoft.Configuration.ConfigurationBuilders.Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/), und installieren Sie die Version.
 
1. Öffnen Sie die web.config-Datei, und schreiben Sie den folgenden Code:
    1. Fügen Sie `configSections` und `configBuilders` hinzu:
        ```xml
         <configSections>
            <section
                name="configBuilders"
                type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
                restartOnExternalChanges="false"
                requirePermission="false" />
         </configSections>
         <configBuilders>
            <builders>
            <add
                    name="AzureKeyVault"
                    vaultName="vaultname"
                    type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
                    vaultUri="https://vaultname.vault.azure.net" />
            </builders>
         </configBuilders>
        ```
    1. Suchen Sie das appSettings-Tag, fügen Sie ein Attribut `configBuilders="AzureKeyVault"` hinzu, und fügen Sie eine Zeile hinzu:
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. Bearbeiten Sie die `About`-Methode in *HomeController.cs* , um den Wert zur Bestätigung anzuzeigen.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. Führen Sie die App lokal unter dem Debugger aus, wechseln Sie zur Registerkarte **Info** , und überprüfen Sie, ob der Wert aus dem Key Vault angezeigt wird.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Ihr Key Vault in einem anderen Microsoft-Konto als dem ausgeführt wird, mit dem Sie bei Visual Studio angemeldet sind (wenn z.B. der Key Vault in Ihrem Geschäftskonto ausgeführt wird, für Visual Studio jedoch Ihr privates Konto verwendet wird), wird in der Datei „Program.cs“ die Fehlermeldung angezeigt, dass Visual Studio keinen Zugriff auf den Key Vault hat. So beheben Sie dieses Problem:

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), und öffnen Sie den Schlüsseltresor.

1. Wählen Sie **Zugriffsrichtlinien** und dann **Zugriffsrichtlinie hinzufügen** , und wählen Sie das Konto, bei dem Sie angemeldet sind, als Prinzipal aus.

1. Wählen Sie in Visual Studio **Datei** > **Kontoeinstellungen** aus.
Wählen Sie **Konto hinzufügen** im Abschnitt **Alle Konten** aus. Melden Sie sich mit dem Konto an, das Sie als Prinzipal der Zugriffsrichtlinie ausgewählt haben.

1. Wählen Sie **Tools** > **Optionen** aus, und suchen Sie **Azure-Dienstauthentifizierung** . Wählen Sie dann das Konto aus, das Sie soeben Visual Studio hinzugefügt haben.

Wenn Sie nun die Anwendung debuggen, stellt Visual Studio eine Verbindung mit dem Konto her, in dem sich Ihr Key Vault befindet.

## <a name="how-your-aspnet-core-project-is-modified"></a>Ändern des ASP.NET Core-Projekts

In diesem Abschnitt werden die genauen Änderungen identifiziert, die an einem ASP.NET-Projekt vorgenommen werden, wenn der verbundene Key Vault-Dienst mit Visual Studio hinzugefügt wird.

### <a name="added-references-for-aspnet-core"></a>Hinzugefügte Verweise für ASP.NET Core

Betrifft die .NET-Verweise der Projektdatei und NuGet-Verweise.

| type | Verweis |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Hinzugefügte Dateien für ASP.NET Core

- `ConnectedService.json` wurde hinzugefügt. Diese Datei enthält Informationen zum Anbieter des verbundenen Diensts und zur Version sowie einen Link zur Dokumentation.

### <a name="project-file-changes-for-aspnet-core"></a>Änderungen an der Projektdatei für ASP.NET Core

- Die Elementgruppe (ItemGroup) der verbundenen Dienste und die Datei `ConnectedServices.json` wurden hinzugefügt.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>Änderungen an „launchsettings.json“ für ASP.NET Core

- Dem IIS Express-Profil und dem Profil, das Ihrem Webprojektnamen entspricht, wurde die folgende Umgebungsvariable hinzugefügt:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Änderungen in Azure für ASP.NET Core

- Eine Ressourcengruppe wurde erstellt (oder eine bereits vorhandene wurde verwendet).
- Eine Key Vault-Instanz wurde in der angegebenen Ressourcengruppe erstellt.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Ändern des ASP.NET Framework-Projekts

In diesem Abschnitt werden die genauen Änderungen identifiziert, die an einem ASP.NET-Projekt vorgenommen werden, wenn der verbundene Key Vault-Dienst mit Visual Studio hinzugefügt wird.

### <a name="added-references-for-aspnet-framework"></a>Hinzugefügte Verweise für ASP.NET Framework

Betrifft die Projektdatei (.NET-Verweise) und `packages.config` (NuGet-Verweise).

| type | Verweis |
| --- | --- |
| .NET; NuGet | Azure.Identity |
| .NET; NuGet | Azure.Security.KeyVault.Keys |
| .NET; NuGet | Azure.Security.KeyVault.Secrets |

> [!IMPORTANT] 
> Standardmäßig ist Azure.Identity 1.1.1 installiert. Diese Implementierung unterstützt keine Visual Studio-Anmeldeinformationen. Sie können den Paketverweis manuell auf Version 1.2 oder höher aktualisieren, um Visual Studio-Anmeldeinformationen zu verwenden.

### <a name="added-files-for-aspnet-framework"></a>Hinzugefügte Dateien für ASP.NET Framework

- `ConnectedService.json` wurde hinzugefügt. Diese Datei enthält Informationen zum Anbieter des verbundenen Diensts und zur Version sowie einen Link zur Dokumentation.

### <a name="project-file-changes-for-aspnet-framework"></a>Änderungen an der Projektdatei für ASP.NET Framework

- Die Elementgruppe des verbundenen Diensts und die Datei „ConnectedServices.json“ wurden hinzugefügt.
- Verweise auf die .NET-Assemblys werden im Abschnitt [Hinzugefügte Verweise](#added-references-for-aspnet-framework) beschrieben.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie dieses Tutorial befolgt haben, sind Ihre Key Vault-Berechtigungen so eingerichtet, dass sie mit Ihrem eigenen Azure-Abonnement ausgeführt werden, aber das ist für ein Produktionsszenario möglicherweise nicht wünschenswert. Sie können eine verwaltete Identität erstellen, um Key Vault-Zugriff für Ihre App zu verwalten. Weitere Informationen finden Sie unter [Authentifizieren bei Key Vault](./authentication.md) und [Zuweisen einer Key Vault-Zugriffsrichtlinie](./assign-access-policy-portal.md).

Weitere Informationen zur Key Vault-Entwicklung finden Sie im [Key Vault-Entwicklerhandbuch](developers-guide.md).