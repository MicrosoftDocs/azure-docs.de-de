---
title: Delegieren einer Unterdomäne – Azure DNS
description: Mit diesem Lernpfad können Sie mit dem Delegieren einer Azure DNS-Unterdomäne beginnen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011539"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegieren einer Azure DNS-Unterdomäne

Sie können das Azure-Portal zum Delegieren einer DNS-Unterdomäne verwenden. Wenn Sie beispielsweise die Domäne „contoso.com“ besitzen, können Sie eine Unterdomäne namens *engineering* an eine andere, separate Zone delegieren, die Sie getrennt von der Zone „contoso.com“ verwalten können.

Falls Sie dies vorziehen, können Sie eine Unterdomäne auch mithilfe von [Azure PowerShell](delegate-subdomain-ps.md) delegieren.

## <a name="prerequisites"></a>Voraussetzungen

Zum Delegieren einer Azure DNS-Unterdomäne müssen Sie zunächst Ihre öffentliche Domäne an Azure DNS delegieren. Anweisungen zum Konfigurieren Ihrer Namenserver für die Delegierung finden Sie unter [Delegieren einer Domäne an Azure DNS](./dns-delegate-domain-azure-dns.md). Sobald Ihre Domäne an Ihre Azure DNS-Zone delegiert ist, können Sie Ihre Unterdomäne delegieren.

> [!NOTE]
> In diesem Artikel wird „contoso.com“ als Beispiel verwendet. Ersetzen Sie Ihren eigenen Domänennamen durch „contoso.com“.

## <a name="create-a-zone-for-your-subdomain"></a>Erstellen einer Zone für die Unterdomäne

Erstellen Sie zuerst die Zone für die Unterdomäne **engineering**.

1. Wählen Sie im Azure-Portal die Option **Ressource erstellen**.
2. Geben Sie **DNS** in das Suchfeld ein, und wählen Sie **DNS-Zone** aus.
3. Klicken Sie auf **Erstellen**.
4. Geben Sie im Bereich **DNS-Zone erstellen** die Domäne **engineering.contoso.com** in das Textfeld **Name** ein.
5. Wählen Sie die Ressourcengruppe für Ihre Zone aus. Es wird empfohlen, dieselbe Ressourcengruppe wie die übergeordnete Zone zu verwenden, um ähnliche Ressourcen zusammenzuhalten.
6. Klicken Sie auf **Erstellen**.
7. Wechseln Sie nach erfolgreicher Bereitstellung zur neuen Zone.

## <a name="note-the-name-servers"></a>Notieren der Namenserver

Notieren Sie sich als Nächstes die vier Namenserver für die engineering-Unterdomäne.

Notieren Sie im Bereich der Zone **engineering** die vier Namenserver für die Zone. Sie verwenden diese Namenserver später.

## <a name="create-a-test-record"></a>Erstellen eines Testeintrags

Erstellen Sie einen **A**-Eintrag zum Testen. Erstellen Sie beispielsweise den A-Eintrag **www**, und konfigurieren Sie ihn mit der IP-Adresse **10.10.10.10**.

## <a name="create-an-ns-record"></a>Erstellen eines NS-Eintrags

Als Nächstes erstellen Sie einen Eintrag für den Namenserver (NS) für die Zone **engineering**.

1. Navigieren Sie zu der Zone für die übergeordnete Domäne.
2. Wählen Sie **+ Ressourceneintragssatz**.
3. Geben Sie im Bereich **Ressourceneintragssatz hinzufügen** den Namen **engineering** in das Textfeld **Name** ein.
4. Wählen Sie unter **Typ** die Option **NS** aus.
5. Geben Sie unter **Namenserver** die vier Namenserver ein, die Sie zuvor in der Zone **engineering** notiert haben.
6. Klicken Sie auf **OK**.

## <a name="test-the-delegation"></a>Testen der Delegierung

Verwenden Sie „nslookup“ zum Testen der Delegierung.

1. Öffnen Sie ein PowerShell-Fenster.
2. Geben Sie an einer Eingabeaufforderung Folgendes ein: `nslookup www.engineering.contoso.com.`.
3. Sie sollten eine nicht autoritative Antwort mit der Adresse **10.10.10.10** erhalten.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Reverse-DNS-Lookups für in Azure gehostete Dienste konfigurieren](dns-reverse-dns-for-azure-services.md).