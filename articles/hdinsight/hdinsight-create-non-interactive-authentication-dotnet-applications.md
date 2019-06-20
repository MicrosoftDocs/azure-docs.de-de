---
title: Erstellen von .NET-Anwendungen für die nicht interaktive Authentifizierung in Azure HDInsight
description: Erfahren Sie, wie Sie Microsoft .NET-Anwendungen für die nicht interaktive Authentifizierung in Azure HDInsight erstellen.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 8b96c38d5bb24a267ad0203083e485d1780f28c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241464"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Erstellen einer .NET HDInsight-Anwendung für die nicht interaktive Authentifizierung
Sie können Ihre Microsoft .NET Azure HDInsight-Anwendung entweder unter der eigenen Identität der Anwendung (nicht interaktiv) oder unter der Identität des angemeldeten Benutzers der Anwendung (interaktiv) ausführen. In diesem Artikel erfahren Sie, wie Sie eine .NET-Anwendung für die nicht interaktive Authentifizierung erstellen, um eine Verbindung mit Azure herzustellen und HDInsight zu verwalten. Ein Beispiel für die interaktive Anwendung finden Sie unter [Verbinden mit Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Für die nicht interaktive .NET-Anwendung benötigen Sie Folgendes:

* Mandanten-ID Ihres Azure-Abonnements auch *Verzeichnis-ID*). Siehe [Abrufen der Mandanten-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Client-ID der Azure Active Directory-Anwendung (Azure AD). Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendung](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) und [Abrufen der Anwendungs-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* Geheimer Schlüssel der Azure AD-Anwendung. Siehe [Abrufen des Anwendungsauthentifizierungsschlüssels](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Voraussetzungen
* Einen HDInsight-Cluster Siehe [Erste-Schritte-Tutorial](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Zuweisen einer Rolle zur Azure AD-Anwendung
Weisen Sie Ihrer Azure AD-Anwendung eine [Rolle](../role-based-access-control/built-in-roles.md) zu, um ihr Berechtigungen zum Ausführen von Aktionen zu gewähren. Sie können den Umfang auf Abonnement-, Ressourcengruppen- oder Ressourcenebene festlegen. Die Berechtigungen werden von niedrigeren Ebenen mit geringerem Umfang geerbt. (Wenn z.B. der Leserolle für eine Ressourcengruppe eine Anwendung hinzugefügt wird, kann diese Anwendung die Ressourcengruppe und alle darin enthaltenen Ressourcen lesen.) In diesem Tutorial legen Sie den Bereich auf Ressourcengruppenebene fest. Weitere Informationen finden Sie unter [Verwenden von Rollenzuweisungen zum Verwalten Ihrer Azure-Abonnementressourcen](../role-based-access-control/role-assignments-portal.md).

**So fügen Sie der Azure AD-Anwendung die Rolle „Besitzer“ hinzu**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü auf der linken Seite die Option **Ressourcengruppen** aus.
3. Wählen Sie die Ressourcengruppe mit dem HDInsight-Cluster aus, in dem Sie später in diesem Tutorial die Hive-Abfrage ausführen möchten. Wenn Sie über eine große Anzahl von Ressourcengruppen verfügen, können Sie mit dem Filter die gewünschte suchen.
4. Wählen Sie im Ressourcengruppenmenü **Zugriffssteuerung (IAM)** .
5. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die aktuellen Rollenzuweisungen anzuzeigen.
6. Klicken Sie oben auf der Seite auf **Rollenzuweisung hinzufügen**.
7. Führen Sie die Anweisungen zum Hinzufügen der Rolle „Besitzer“ zu Ihrer Azure AD-Anwendung aus. Nachdem Sie die Rolle erfolgreich hinzugefügt haben, wird die Anwendung unter der Rolle „Besitzer“ aufgelistet. 

## <a name="develop-an-hdinsight-client-application"></a>Entwickeln einer HDInsight-Clientanwendung

1. Erstellen Sie eine C#-Konsolenanwendung.
2. Fügen Sie die folgenden [NuGet](https://www.nuget.org/)-Pakete hinzu:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Führen Sie den folgenden Code aus:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```


## <a name="next-steps"></a>Nächste Schritte
* [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../active-directory/develop/howto-create-service-principal-portal.md).
* Weitere Informationen finden Sie unter [Authentifizieren eines Dienstprinzipals mit Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Weitere Informationen finden Sie unter [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen](../role-based-access-control/role-assignments-portal.md).
