---
title: Automatische Installation des Azure AD-Anwendungsproxyconnectors | Microsoft-Dokumentation
description: Dieses Thema erläutert, wie Sie eine unbeaufsichtigte Installation des Azure AD-Anwendungsproxyconnectors durchführen, um sicheren Remotezugriff auf Ihre lokalen Apps zu gewähren.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eb3e52dfd02bd7948f1b5ffd908ac1255118008
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782914"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Erstellen eines Skripts für die unbeaufsichtigte Installation für den Azure AD-Anwendungsproxyconnector

In diesem Thema wird das Erstellen eines Windows PowerShell-Skripts erläutert, das eine unbeaufsichtigte Installation und Registrierung des Azure AD-Anwendungsproxyconnectors ermöglicht.

Diese Funktion ist in folgenden Fällen nützlich:

* Installieren des Connectors auf Windows-Servern, auf denen keine Benutzeroberfläche aktiviert ist oder auf die nicht per Remotedesktop zugegriffen werden kann.
* Gleichzeitiges Installieren und Registrieren vieler Connectors.
* Integrieren der Connectorinstallation und Registrierung als Teil einer anderen Prozedur.
* Erstellen eines standardmäßigen Serverimages, das die Connectorbits enthält, aber nicht registriert ist.

Damit der [Anwendungsproxyconnector](application-proxy-connectors.md) funktioniert, muss er von einem Anwendungsadministrator mit Kennwort im Azure AD-Verzeichnis registriert werden. Diese Informationen werden normalerweise bei der Installation des Connectors in einem Popupdialogfeld eingegeben. Der Prozess kann jedoch mithilfe von PowerShell automatisiert werden.

Eine unbeaufsichtigte Installation umfasst zwei Schritte. Erstens: Die Installation des Connectors. Zweitens: Die Registrierung des Connectors bei Azure AD. 

## <a name="install-the-connector"></a>Installieren des Connectors
Gehen Sie wie folgt vor, um den Connector zu installieren, ohne ihn zu registrieren:

1. Öffnen Sie eine Eingabeaufforderung.
2. Führen Sie den folgenden Befehl aus. („/q“ steht hierbei für die automatische Installation.) Bei einer automatischen Installation werden Sie nicht zum Akzeptieren der Lizenzbedingungen aufgefordert.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrieren des Connectors bei Azure AD
Es gibt zwei Methoden zum Registrieren des Connectors:

* Registrieren des Connectors mit einem Windows PowerShell-Anmeldeinformationsobjekt
* Registrieren des Connectors mithilfe eines offline erstellten Tokens

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrieren des Connectors mit einem Windows PowerShell-Anmeldeinformationsobjekt
1. Erstellen Sie ein Windows PowerShell-Anmeldeinformationsobjekt (`$cred`) mit einem Administratorbenutzernamen und einem Kennwort für Ihr Verzeichnis. Führen Sie den folgenden Befehl aus, und ersetzen Sie dabei *\<username\>* und *\<password\>* durch die entsprechenden Werte:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Wechseln Sie zu **C:\Programme\Microsoft AAD App Proxy Connector**, und führen Sie das folgende Skript unter Verwendung des zuvor erstellten Objekts `$cred` aus:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrieren des Connectors mithilfe eines offline erstellten Tokens
1. Erstellen Sie mithilfe der AuthenticationContext-Klasse ein Offlinetoken unter Verwendung der Werte im folgenden Codeausschnitt bzw. in den PowerShell-Cmdlets:

    **Mithilfe von C#:**

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }

    **Mithilfe von PowerShell:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. Sobald das Token vorliegt, erstellen Sie SecureString mit diesem Token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Führen Sie folgenden Windows PowerShell-Befehl aus, und ersetzen Sie \<tenant GUID\> durch die ID Ihres Verzeichnisses:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Nächste Schritte 
* [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](application-proxy-configure-custom-domain.md)
* [Aktivieren der einmaligen Anmeldung](application-proxy-configure-single-sign-on-with-kcd.md)
* [Problembehandlung von Anwendungsproxys](application-proxy-troubleshoot.md)


