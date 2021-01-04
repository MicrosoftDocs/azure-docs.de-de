---
title: Tutorial für die Azure AD-Berichterstellungs-API mit Zertifikaten | Microsoft-Dokumentation
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
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: c3443cb73e85fc69349e7293597a5f4a723959d3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130050"
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

6. Wenn Sie das Modul erstmalig verwenden, führen Sie **Install-MSCloudIdUtilsModule** aus. Andernfalls können Sie es mit dem PowerShell-Befehl **Import-Module** importieren. Ihre Sitzung sollte etwa wie folgt aussehen: ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
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

2. Wählen Sie auf dem Blatt „Anwendungsregistrierung“ im Abschnitt **Verwalten** die Option **Zertifikate und Geheimnisse** aus, und wählen Sie dann **Zertifikat hochladen** aus.

3. Wählen Sie die Zertifikatdatei aus dem vorherigen Schritt aus, und wählen Sie dann **Hinzufügen** aus. 

4. Notieren Sie die Anwendungs-ID und den Fingerabdruck des Zertifikats, das Sie gerade in Ihrer Anwendung registriert haben. Um den Fingerabdruck zu finden, navigieren Sie auf Ihrer Anwendungsseite im Portal im Abschnitt **Verwalten** zu **Zertifikate und Geheimnisse**. Der Fingerabdruck befindet sich unter der Liste **Zertifikate**.

5. Öffnen Sie das Anwendungsmanifest im Inline-Manifest-Editor, und überprüfen Sie, ob die Eigenschaft *keyCredentials* mit Ihren neuen Zertifikatinformationen aktualisiert wurde, wie unten dargestellt. 

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
6. Jetzt können Sie mit diesem Zertifikat ein Zugriffstoken für die MS Graph-API abrufen. Verwenden Sie das Cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** aus dem PowerShell-Modul „MSCloudIdUtils“, und übergeben Sie dabei als Eingabe die Anwendungs-ID und den Fingerabdruck, die Sie im vorherigen Schritt erhalten haben. 

   ![Screenshot: PowerShell-Fenster mit einem Befehl, der ein Zugriffstoken erstellt](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Verwenden Sie das Zugriffstoken in Ihrem PowerShell-Skript, um die Graph-API abzufragen. Verwenden Sie das Cmdlet **Invoke-MSCloudIdMSGraphQuery** aus „MSCloudIDUtils“, um die Endpunkte „signins“ und „directoryAudits“ aufzuzählen. Mit diesem Cmdlet werden mehrseitige Ergebnisse verarbeitet, die an die PowerShell-Pipeline gesendet werden.

8. Abfragen des Endpunkts „directoryAudits“, um die Überwachungsprotokolle abzurufen. 

   ![Screenshot: PowerShell-Fenster mit einem Befehl zum Abfragen des Endpunkts „directoryAudits“ mithilfe des Zugriffstokens, das Sie weiter oben in diesem Verfahren abgerufen haben](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Abfragen des Endpunkts „signins“, um die Anmeldeprotokolle abzurufen.

    ![Screenshot: PowerShell-Fenster mit einem Befehl zum Abfragen des Endpunkts „signins“ mithilfe des Zugriffstokens, das Sie weiter oben in diesem Verfahren abgerufen haben](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Sie können nun diese Daten in eine CSV-Datei exportieren und Datei in einem SIEM-System speichern. Außerdem können Sie Ihr Skript mit einer geplanten Aufgabe umschließen, mit der Azure AD-Daten regelmäßig aus Ihrem Mandanten abgerufen werden, ohne dass Anwendungsschlüssel im Quellcode gespeichert werden müssen. 

## <a name="next-steps"></a>Nächste Schritte

* [Verschaffen eines ersten Eindrucks über die Berichterstellungs-APIs](concept-reporting-api.md)
* [Referenz zur Überwachungs-API](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Referenz zur Anmeldeaktivitätsbericht-API](/graph/api/resources/signin?view=graph-rest-beta)