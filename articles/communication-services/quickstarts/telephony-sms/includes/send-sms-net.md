---
title: include file
description: include file
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: a084295aec2cafadd07d47e85a0116a89d37c985
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816919"
---
Steigen Sie in Azure Communication Services ein, indem Sie die C#-Clientbibliothek für SMS von Communication Services nutzen, um SMS-Nachrichten zu senden.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Die aktuelle Version der [.NET Core-Clientbibliothek](https://dotnet.microsoft.com/download/dotnet-core) für Ihr Betriebssystem.
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../../create-communication-resource.md)
- Eine für SMS-Nachrichten geeignete Telefonnummer. [Beziehen Sie eine Telefonnummer.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

- Führen Sie in einem Terminal- oder Befehlsfenster den Befehl `dotnet` aus, um sich zu vergewissern, dass die .NET-Clientbibliothek installiert ist.
- Melden Sie sich zum Anzeigen der Telefonnummern für Ihre Communication Services-Ressource beim [Azure-Portal](https://portal.azure.com/) an, suchen Sie nach Ihrer Communication Services-Ressource, und öffnen Sie im linken Navigationsbereich die Registerkarte **Telefonnummern**.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `SmsQuickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Wechseln Sie zum neu erstellten App-Ordner, und verwenden Sie den Befehl `dotnet build`, um Ihre Anwendung zu kompilieren.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mithilfe des Befehls `dotnet add package` das .NET-Paket der Clientbibliothek für SMS von Azure Communication Services.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.3
```

Fügen Sie am Anfang von **Program.cs** eine Anweisung vom Typ `using` hinzu, um den Namespace `Azure.Communication` einzuschließen.

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features der C#-Clientbibliothek für SMS von Azure Communication Services verwendet:

| Name                                       | Beschreibung                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Diese Klasse ist für alle SMS-Funktionen erforderlich. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Senden von SMS-Nachrichten.                           |
| SendSmsOptions | Diese Klasse bietet Optionen zum Konfigurieren von Zustellberichten. Ist „enable_delivery_report“ auf „True“ festgelegt, wird bei erfolgreicher Zustellung ein Ereignis ausgegeben. |

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

 Öffnen Sie **Program.cs** in einem Text-Editor, und ersetzen Sie den Text der Methode `Main` durch Code zum Initialisieren eines SMS-Clients (`SmsClient`) mit Ihrer Verbindungszeichenfolge. Im folgenden Code wird die Verbindungszeichenfolge für die Ressource aus einer Umgebungsvariablen namens `COMMUNICATION_SERVICES_CONNECTION_STRING` abgerufen. Informationen zur Verwaltung der Verbindungszeichenfolge Ihrer Ressource finden Sie [hier](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Senden einer SMS

Senden Sie eine SMS-Nachricht durch Aufrufen der Methode „Send“. Fügen Sie in **Program.cs** am Ende der Methode `Main` den folgenden Code hinzu:

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

Ersetzen Sie `<leased-phone-number>` durch eine für SMS geeignete Telefonnummer, die Ihrer Communication Services-Ressource zugeordnet ist, und ersetzen Sie `<to-phone-number>` durch die Telefonnummer, an die Sie eine Nachricht senden möchten.

Der Parameter `EnableDeliveryReport` ist ein optionaler Parameter zum Konfigurieren von Zustellberichten. Dies ist in Szenarien hilfreich, in denen Ereignisse ausgegeben werden sollen, wenn SMS-Nachrichten zugestellt wurden. Informationen zum Konfigurieren von Zustellberichten für SMS-Nachrichten finden Sie in der Schnellstartanleitung [Behandeln von SMS-Ereignissen](../handle-sms-events.md).

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```console
dotnet run
```

## <a name="sample-code"></a>Beispielcode

Sie können die Beispiel-App von [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS) herunterladen.
