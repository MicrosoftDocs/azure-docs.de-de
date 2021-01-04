---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020348"
---
Wenn Sie keine Erweiterungspakete verwenden können, können Sie Azure Functions Core Tools lokal nutzen, um die für Ihr Projekt erforderlichen Erweiterungspakete zu installieren.

> [!IMPORTANT]
> Erweiterungen können nicht explizit in einer Funktions-App installiert werden, die Erweiterungspakete verwendet. Entfernen Sie den Abschnitt `extensionBundle` in *host.json*, bevor Sie Erweiterungen explizit installieren.

Die folgenden Elemente beschreiben einige Gründe, aus denen Erweiterungen möglicherweise manuell installiert werden müssen:

* Sie müssen auf eine bestimmte Version einer Erweiterung zugreifen, die nicht in einem Paket verfügbar ist.
* Sie müssen auf eine benutzerdefinierte Erweiterung zugreifen, die nicht in einem Paket verfügbar ist.
* Sie müssen auf eine bestimmte Kombination von Erweiterungen zugreifen, die nicht in einem einzelnen Paket verfügbar ist.

> [!NOTE]
> Um Erweiterungen mithilfe von Core Tools manuell zu installieren, müssen Sie das [.NET Core 2.x SDK](https://dotnet.microsoft.com/download) installiert haben. Das .NET Core SDK wird von Azure Functions Core Tools zum Installieren von Erweiterungen über NuGet verwendet. Für die Verwendung von Azure Functions-Erweiterungen müssen Sie nicht mit .NET vertraut sein.

Bei der expliziten Installation von Erweiterungen wird die .NET-Projektdatei „extensions.csproj“ zum Stamm Ihres Projekts hinzugefügt. Diese Datei definiert die von Ihren Funktionen benötigten NuGet-Pakete. Sie können zwar mit den [NuGet-Paketverweisen](/nuget/consume-packages/package-references-in-project-files) in dieser Datei arbeiten, mit Core Tools können Erweiterungen jedoch installiert werden, ohne die Datei manuell bearbeiten zu müssen.

Es gibt verschiedene Möglichkeiten, um Core Tools zum Installieren der erforderlichen Erweiterungen in Ihrem lokalen Projekt zu verwenden. 

#### <a name="install-all-extensions"></a>Installieren aller Erweiterungen 

Verwenden Sie den folgenden Befehl, um automatisch alle Erweiterungspakete hinzuzufügen, die von den Bindungen in Ihrem lokalen Projekt verwendet werden:

```dotnetcli
func extensions install
```
Der Befehl liest die Datei *function.json*, um zu ermitteln, welche Pakete Sie benötigen, installiert diese und erzeugt das Erweiterungenprojekt (extensions.csproj) neu. Er fügt alle neuen Bindungen an die aktuelle Version hinzu, aktualisiert aber keine vorhandenen Bindungen. Verwenden Sie die Option `--force`, um vorhandene Bindungen beim Installieren neuer auf die neueste Version zu aktualisieren.

Verwendet Ihre Funktions-App Bindungen, die von Core Tools nicht erkannt werden, müssen Sie die jeweilige Erweiterung manuell installieren.

#### <a name="install-a-specific-extension"></a>Installieren einer bestimmten Erweiterung

Verwenden Sie den folgenden Befehl, um ein bestimmtes Erweiterungspaket mit einer bestimmten Version zu installieren (in diesem Fall die Speichererweiterung):

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
