---
title: Herstellen einer Verbindung mit Azure Analysis Services-Servern | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure eine Verbindung mit Analysis Services herstellen und Daten abrufen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9a8863189ee9cb63d86b157c0bbebb6fd16116b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61027982"
---
# <a name="connecting-to-servers"></a>Herstellen einer Verbindung mit Servern

In diesem Artikel wird beschrieben, wie Sie mithilfe von Anwendungen für die Datenmodellierung und -verwaltung, z.B. SQL Server Management Studio (SSMS) oder SQL Server Data Tools (SSDT), eine Verbindung mit einem Server herstellen. Oder mithilfe von Clientanwendungen für die Berichterstellung wie Microsoft Excel, Power BI Desktop oder benutzerdefinierten Anwendungen. Für Verbindungen mit Azure Analysis Services wird HTTPS verwendet.

## <a name="client-libraries"></a>Clientbibliotheken

[Abrufen der neuesten Clientbibliotheken](analysis-services-data-providers.md)

Für alle Verbindungen mit einem Server sind unabhängig vom Typ aktualisierte AMO-, ADOMD.NET- und OLEDB-Clientbibliotheken erforderlich, um eine Verbindung mit einem Analysis Services-Server herzustellen und mit ihm zu kommunizieren. Für SSMS, SSDT, Excel 2016 und höhere Versionen sowie Power BI werden die neuesten Clientbibliotheken installiert oder mit monatlichen Releases aktualisiert. In einigen Fällen ist es jedoch möglich, dass eine Anwendung nicht über die neuesten Clientbibliotheken verfügt. Dies kann beispielsweise der Fall sein, wenn Updates durch Richtlinien verzögert werden oder wenn Office 365-Updates über den verzögerten Kanal erfolgen.

## <a name="server-name"></a>Servername

Wenn Sie einen Analysis Services-Server in Azure erstellen, geben Sie einen eindeutigen Namen, und die Region an, in der der Server erstellt werden soll. Wenn Sie einen Servernamen in der Verbindung angeben, lautet das Benennungsschema des Servers wie folgt:

```
<protocol>://<region>/<servername>
```
 Wenn „protocol“ die Zeichenfolge **asazure** enthält, ist die Region der URI der Region, in welcher der Server erstellt wurde (z.B. „westus.asazure.windows.net“), und der Servername der Name Ihres eindeutigen Servers innerhalb der Region.

### <a name="get-the-server-name"></a>Abrufen des Servernamens

Kopieren Sie den gesamten Servernamen über **Azure-Portal** > Server > **Übersicht** > **Servername**. Wenn auch andere Benutzer in Ihrer Organisation Verbindungen mit diesem Server herstellen, können Sie diesen Servernamen mit ihnen teilen. Wenn Sie einen Servernamen angeben, muss der gesamte Pfad verwendet werden.

![Abrufen von Servernamen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Das Protokoll für die Region „USA, Osten 2“ ist **aspaaseastus2**.

## <a name="connection-string"></a>Verbindungszeichenfolge

Wenn Sie eine Verbindung zu Azure Analysis Services mithilfe des Tabellenobjektmodells herstellen, verwenden Sie folgende Formate für Verbindungszeichenfolgen:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrierte Authentifizierung über Azure Active Directory

Die integrierte Authentifizierung ruft den Cache für Anmeldeinformationen von Azure Active Directory ab, falls vorhanden. Wenn dies nicht der Fall ist, öffnet sich das Azure-Anmeldefenster.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Authentifizierung über Azure Active Directory mit Benutzername und Kennwort

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-Authentifizierung (integrierte Sicherheit)

Verwenden Sie das Windows-Konto, unter dem der aktuelle Prozess ausgeführt wird.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Herstellen einer Verbindung mithilfe einer ODC-Datei

Bei Verwendung älterer Versionen von Excel können Benutzer mithilfe einer ODC-Datei (Office Data Connection) eine Verbindung mit einem Azure Analysis Services-Server herstellen. Weitere Informationen finden Sie unter [Erstellen einer ODC-Datei (Office Data Connection)](analysis-services-odc.md).


## <a name="next-steps"></a>Nächste Schritte

[Herstellen einer Verbindung mit Excel](analysis-services-connect-excel.md)    
[Herstellen einer Verbindung mit Power BI](analysis-services-connect-pbi.md)   
[Manage your server (Verwalten des Servers)](analysis-services-manage.md)   

