---
title: 'Tutorial: Abrufen von Daten per Azure AD-Berichterstellungs-API mit Zertifikaten | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie die Azure AD-Berichterstellungs-API mit Zertifikatanmeldeinformationen verwenden, um Daten ohne Benutzereingriff aus Verzeichnissen abzurufen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e006111cce7f53ff87f1c6d60b2a5147da02e1e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60284945"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Tutorial: Abrufen von Daten per Berichtserstellungs-API von Azure Active Directory mit Zertifikaten

Die [Berichtserstellungs-APIs von Azure Active Directory (Azure AD)](concept-reporting-api.md) bieten Ihnen über eine Gruppe von REST-basierten APIs programmgesteuerten Zugriff auf die Daten. Sie können diese APIs über verschiedene Programmiersprachen und Tools aufrufen. Wenn Sie ohne Benutzereingriff auf die Azure AD-Berichterstellungs-API zugreifen möchten, müssen Sie für Ihren Zugriff die Verwendung von Zertifikaten konfigurieren.

In diesem Tutorial erfahren Sie, wie Sie ein Testzertifikat verwenden, um zu Berichterstellungszwecken auf die MS Graph-API zuzugreifen. Wir raten von der Verwendung des Testzertifikats in einer Produktionsumgebung ab. 

## <a name="prerequisites"></a>Voraussetzungen

1. Stellen Sie für den Zugriff auf Anmeldedaten sicher, dass Sie über einen Azure Active Directory-Mandanten mit einer Premium-Lizenz (P1/P2) verfügen. Unter [Erste Schritte mit Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) erfahren Sie, wie Sie ein Upgrade für Ihre Azure Active Directory-Edition durchführen. Beachten Sie Folgendes: Wenn Sie vor dem Upgrade über keine Aktivitätsdaten verfügten, dauert es ein paar Tage, bis die Daten in den Berichten angezeigt werden, nachdem Sie ein Upgrade auf eine Premium-Lizenz durchgeführt haben. 

2. Erstellen Sie ein Benutzerkonto in der Rolle **Globaler Administrator**, **Sicherheitsadministrator**, **Sicherheitsleseberechtigter** oder **Benutzer mit Leseberechtigung für Berichte** für den Mandanten, oder wechseln Sie zu einem entsprechenden Benutzerkonto. 

3. Schließen Sie die [Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API](howto-configure-prerequisites-for-reporting-api.md) ab. 

4. Laden Sie [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md) herunter, und installieren Sie die Software.

5. Installieren Sie [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Dieses Modul enthält mehrere Hilfsprogramm-Cmdlets, z.B.:
    - Die für die Authentifizierung benötigten ADAL-Bibliotheken
    - Zugriffstoken für Benutzer, Anwendungsschlüssel und Zertifikate mit Verwendung von ADAL
    - Graph-API zur Verarbeitung von auf Seiten aufgeteilten Ergebnissen

6. Wenn Sie das Modul erstmalig verwenden, führen Sie **Install-MSCloudIdUtilsModule** aus. Ansonsten können Sie es einfach mit dem PowerShell-Befehl **Import-Module** importieren. Ihre Sitzung sollte etwa wie folgt aussehen: ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Verwenden Sie das PowerShell-Cmdlet **New-SelfSignedCertificate**, um ein Testzertifikat zu erstellen.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Verwenden Sie das Cmdlet **Export-Certificate**, um es in eine Zertifikatdatei zu exportieren.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Abrufen von Daten per Berichtserstellungs-API von Azure Active Directory mit Zertifikaten

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), wählen Sie **Azure Active Directory** aus und dann **App-Registrierungen**, und wählen Sie Ihre Anwendung aus der Liste aus. 

2. Wählen Sie **Einstellungen** > **Schlüssel** und dann **Öffentlichen Schlüssel** hochladen aus.

3. Wählen Sie die Zertifikatdatei aus dem vorherigen Schritt aus, und wählen Sie dann **Speichern** aus. 

4. Notieren Sie die Anwendungs-ID und den Fingerabdruck des Zertifikats, das Sie gerade in Ihrer Anwendung registriert haben. Um den Fingerabdruck zu finden, wechseln Sie auf Ihrer Anwendungsseite im Portal zu **Einstellungen**, und klicken Sie auf **Schlüssel**. Der Fingerabdruck befindet sich unter der Liste **Öffentliche Schlüssel**.

5. Öffnen Sie das Anwendungsmanifest im Inline-Manifest-Editor, und ersetzen Sie die Eigenschaft *keyCredentials* durch Ihre neuen Zertifikatinformationen, indem Sie das folgende Schema verwenden. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Speichern Sie das Manifest. 
  
7. Jetzt können Sie mit diesem Zertifikat ein Zugriffstoken für die MS Graph-API abrufen. Verwenden Sie das Cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** aus dem PowerShell-Modul „MSCloudIdUtils“, und übergeben Sie dabei als Eingabe die Anwendungs-ID und den Fingerabdruck, die Sie im vorherigen Schritt erhalten haben. 

   ![Azure-Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Verwenden Sie das Zugriffstoken in Ihrem PowerShell-Skript zum Abfragen der Graph-API. Verwenden Sie das Cmdlet **Invoke-MSCloudIdMSGraphQuery** aus „MSCloudIDUtils“, um die Endpunkte „signins“ und „directoryAudits“ aufzuzählen. Mit diesem Cmdlet werden mehrseitige Ergebnisse verarbeitet, die an die PowerShell-Pipeline gesendet werden.

9. Abfragen des Endpunkts „directoryAudits“, um die Überwachungsprotokolle abzurufen. 
   ![Azure-Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Abfragen des Endpunkts „signins“, um die Anmeldeprotokolle abzurufen.
    ![Azure-Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Sie können nun diese Daten in eine CSV-Datei exportieren und Datei in einem SIEM-System speichern. Außerdem können Sie Ihr Skript mit einer geplanten Aufgabe umschließen, mit der Azure AD-Daten regelmäßig aus Ihrem Mandanten abgerufen werden, ohne dass Anwendungsschlüssel im Quellcode gespeichert werden müssen. 

## <a name="next-steps"></a>Nächste Schritte

* [Verschaffen eines ersten Eindrucks über die Berichterstellungs-APIs](concept-reporting-api.md)
* [Referenz zur Überwachungs-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referenz zur Anmeldeaktivitätsbericht-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
