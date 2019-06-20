---
title: Verwenden von ReportViewer auf einer Website | Microsoft Docs
description: In diesem Thema wird beschrieben, wie eine Microsoft Azure-Website mit dem Visual Studio-Steuerelement „ReportViewer“ erstellt wird, in dem ein Bericht angezeigt wird, der auf einem virtuellen Microsoft Azure-Computer gespeichert ist.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: b554dc1fa33519d87aa0c9c5ba9130b47cbea142
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60580067"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Verwenden von ReportViewer auf einer in Azure gehosteten Website
> [!IMPORTANT]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.

Sie können eine Microsoft Azure-Website mit dem Visual Studio-Steuerelement „ReportViewer“ erstellen, in dem ein Bericht angezeigt wird, der auf einem virtuellen Microsoft Azure-Computer gespeichert ist. Das ReportViewer-Steuerelement ist eine Webanwendung, die Sie mithilfe der Vorlage für ASP.NET-Webanwendungen erstellen.

> [!IMPORTANT]
> Die Vorlagen für ASP.NET MVC-Webanwendungen unterstützen das ReportViewer-Steuerelement nicht.

Um „ReportViewer“ in Ihre Microsoft Azure-Website zu integrieren, müssen Sie die folgenden Aufgaben ausführen.

* **Hinzufügen** von Assemblys zum Bereitstellungspaket
* **Konfigurieren** von Authentifizierung und Autorisierung
* **Veröffentlichen** der ASP.NET-Webanwendung in Azure

## <a name="prerequisites"></a>Voraussetzungen
Lesen Sie den Abschnitt "Allgemeine Empfehlungen und bewährte Methoden" in [SQL Server-Business Intelligence auf virtuellen Azure-Computern](../classic/ps-sql-bi.md).

> [!NOTE]
> ReportViewer-Steuerelemente werden mit Visual Studio Standard Edition oder höher geliefert. Wenn Sie die Web Developer Express Edition verwenden, müssen Sie die [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) installieren, damit Sie die ReportViewer-Laufzeitfunktionen verwenden können.
>
> Ein im lokalen Verarbeitungsmodus konfiguriertes ReportViewer-Steuerelement wird in Microsoft Azure nicht unterstützt.

## <a name="adding-assemblies-to-the-deployment-package"></a>Hinzufügen von Assemblys zu dem Bereitstellungspaket
Wenn Sie Ihre ASP.NET-Anwendung lokal hosten, werden die ReportViewer-Assemblys bei der Installation von Visual Studio in der Regel direkt im globalen Assemblycache (GAC) des IIS-Servers installiert, sodass die Anwendung direkt auf sie zugreifen kann. Wenn Sie Ihre ASP.NET-Anwendung dagegen in der Cloud hosten, lässt Microsoft Azure es nicht zu, dass irgendeine Komponente im GAC installiert wird. Daher müssen Sie sicherstellen, dass die ReportViewer-Assemblys lokal für Ihre Anwendung verfügbar sind. Sie können dies erreichen, indem Sie in Ihrem Projekt Verweise auf die Assemblys hinzufügen und diese so konfigurieren, dass sie lokal kopiert werden.

Im Remoteverarbeitungsmodus verwendet das ReportViewer-Steuerelement die folgenden Assemblys:

* **Microsoft.ReportViewer.WebForms.dll**: Enthält den ReportViewer-Code, den Sie benötigen, um das ReportViewer-Steuerelement auf Ihrer Seite zu verwenden. Ein Verweis auf diese Assembly wird dem Projekt hinzugefügt, wenn Sie ein ReportViewer-Steuerelement auf einer ASP.NET-Seite in Ihrem Projekt ablegen.
* **Microsoft.ReportViewer.Common.dll**: Enthält Klassen, die vom ReportViewer-Steuerelement zur Laufzeit verwendet werden. Diese Assembly wird Ihrem Projekt nicht automatisch hinzugefügt.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>So fügen Sie einen Verweis auf „Microsoft.ReportViewer.Common“ hinzu
* Klicken Sie mit der rechten Maustaste auf den Knoten **Verweise** des Projekts, wählen Sie **Verweis hinzufügen** und die Assembly auf der .NET-Registerkarte aus, und klicken Sie auf **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>So erreichen Sie, dass Ihre ASP.NET-Anwendung lokal auf die Assemblys zugreifen kann
1. Klicken Sie im Ordner **Verweise** auf die Assembly "Microsoft.ReportViewer.Common", sodass seine Eigenschaften im Eigenschaftenbereich angezeigt werden.
2. Legen Sie im Eigenschaftenbereich die Eigenschaft **Lokale Kopie** auf "Wahr" fest.
3. Wiederholen Sie die Schritte 1 und 2 für „Microsoft.ReportViewer.WebForms“.

### <a name="to-get-reportviewer-language-pack"></a>So rufen Sie ReportViewer Language Pack ab
1. Installieren Sie das entsprechende verteilbare Microsoft Report Viewer 2012-Laufzeit-Paket aus dem [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=317386).
2. Wählen Sie die Sprache in der Dropdownliste aus. Danach wird die Seite auf die entsprechende Download Center-Seite umgeleitet.
3. Klicken Sie auf **Herunterladen** , um mit dem Herunterladen von "ReportViewerLP.exe" zu beginnen.
4. Nachdem Sie „ReportViewerLP.exe“ heruntergeladen haben, klicken Sie zur sofortigen Installation auf **Ausführen**, oder klicken Sie auf **Speichern**, damit die Datei auf dem Computer gespeichert wird. Wenn Sie auf **Speichern**klicken, sollten Sie sich den Namen des Ordners merken, in dem Sie die Datei speichern.
5. Navigieren Sie zu dem Ordner, in dem Sie die Datei gespeichert haben. Klicken Sie mit der rechten Maustaste auf „ReportViewerLP.exe“, klicken Sie auf **Als Administrator ausführen**, und klicken Sie dann auf **Ja**.
6. Nach dem Ausführen von „ReportViewerLP.exe“ können Sie sehen, dass „c:\windows\assembly“ die Ressourcendateien **Microsoft.ReportViewer.Webforms.Resources** und **Microsoft.ReportViewer.Common.Resources** enthält.

### <a name="to-configure-for-localized-reportviewer-control"></a>So konfigurieren Sie für ein lokalisiertes ReportViewer-Steuerelement
1. Gehen Sie gemäß den oben angegebenen Anweisungen vor, um das verteilbare Microsoft Report Viewer 2012-Laufzeit-Paket herunterzuladen und zu installieren.
2. Erstellen Sie im Projekt den \<Sprachordner\>, und kopieren Sie die zugehörigen Ressourcenassemblydateien. Dies sind die zu kopierenden Ressourcenassemblydateien: **Microsoft.ReportViewer.Webforms.Resources.dll** und **Microsoft.ReportViewer.Common.Resources.dll**. Wählen Sie die Ressourcenassemblydateien aus, und legen Sie im Eigenschaftenbereich die Eigenschaft **In Ausgabeverzeichnis kopieren** auf **Immer kopieren** fest.
3. Legen Sie die Kultur und Benutzeroberflächenkultur für das Webprojekt fest. Weitere Informationen dazu, wie die Kultur und Benutzeroberflächenkultur für eine ASP.NET-Webseite festgelegt werden, finden Sie unter [Vorgehensweise: Festlegen der Kultur und Benutzeroberflächenkultur für die Globalisierung von ASP.NET-Webseiten](https://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Konfigurieren von Authentifizierung und Autorisierung
Das ReportViewer-Steuerelement muss geeignete Anmeldeinformationen verwenden, um sich beim Berichtsserver zu authentifizieren, und die Anmeldeinformationen müssen vom Berichtsserver autorisiert werden, damit Sie auf die gewünschten Berichte zuzugreifen können. Informationen über die Authentifizierung finden Sie im Whitepaper [ReportViewer-Steuerelement von Reporting Services und Berichtsserver, die auf virtuellen Microsoft Azure-Computern basieren](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Veröffentlichen der ASP.NET-Webanwendung in Azure
Anweisungen zum Veröffentlichen einer ASP.NET-Webanwendung in Azure finden Sie unter [Vorgehensweise: Migrieren und Veröffentlichen einer Webanwendung in Azure aus Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) und [Erste Schritte mit Web-Apps und ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Wenn der Befehl „Azure-Bereitstellungsprojekt hinzufügen“ oder „Azure-Clouddienst-Projekt hinzufügen“ nicht im Kontextmenü im Projektmappen-Explorer angezeigt wird, müssen Sie das Zielframework für das Projekt in .NET Framework 4 ändern.
>
> Die beiden Befehle bieten im Wesentlichen die gleiche Funktionalität. Je nachdem, welche Version von Microsoft Azure SDK Sie installiert haben, wird der entsprechende dieser beiden Befehle im Kontextmenü angezeigt.
>
>

## <a name="resources"></a>Ressourcen
[Microsoft-Berichte](https://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server-Business Intelligence auf virtuellen Azure-Computern](../classic/ps-sql-bi.md)

[Verwenden von PowerShell zum Erstellen einer Azure-VM mit einem Berichtsserver im einheitlichen Modus](../classic/ps-sql-report.md)
