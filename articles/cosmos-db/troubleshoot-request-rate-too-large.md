---
title: Troubleshooting für Ausnahmen aufgrund einer zu hohen Azure Cosmos DB-Anforderungsrate
description: Erfahren Sie mehr über das Diagnostizieren und Beheben von Problemen bei einer Ausnahme aufgrund einer zu hohen Anforderungsrate.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c985cea24ea772fe9e0c9bcad59f63a66b2489cc
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340106"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Diagnostizieren und Behandeln von Problemen im Zusammenhang mit der Azure Cosmos DB-Ausnahme, dass die Anforderungsrate zu hoch ist
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Eine Fehlermeldung über eine zu hohe Anforderungsrate oder der Fehlercode 429 deuten darauf hin, dass Ihre Anforderungen gedrosselt werden.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
Der folgende Abschnitt enthält bekannte Gründe und Lösungen für zu viele Anforderungen.

### <a name="check-the-metrics"></a>Überprüfen der Metriken
Unter [Azure Cosmos DB-Überwachung](monitor-cosmos-db.md) finden Sie eine Reihe von 429-Ausnahmen.

#### <a name="cause"></a>Ursache:
Der verbrauchte Durchsatz (RU/s, Anforderungseinheiten pro Sekunde) liegt über dem [bereitgestellten Durchsatz](set-throughput.md). Das SDK wiederholt Anforderungen basierend auf der angegebenen Wiederholungsrichtlinie automatisch. Wenn dieser Fehler häufig auftritt, erwägen Sie das Erhöhen des Durchsatzes für die Sammlung. Überprüfen Sie die Metriken im Portal auf 429-Fehler. Überprüfen Sie Ihren Partitionsschlüssel, um sicherzustellen, dass eine [gleichmäßige Verteilung von Speicher- und Anforderungsvolumen](partitioning-overview.md) gewährleistet ist.

#### <a name="solution"></a>Lösung:
1. Verwenden Sie das [Portal oder das SDK](set-throughput.md), um den bereitgestellten Durchsatz zu erhöhen.
1. Wählen Sie für die Datenbank oder den Container die Option [Autoskalierung](provision-throughput-autoscale.md) aus.

## <a name="next-steps"></a>Nächste Schritte
* [Diagnostizieren und Behandeln](troubleshoot-dot-net-sdk.md) von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
* Weitere Informationen zu Leistungsrichtlinien für [.NET Version 3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET Version 2](performance-tips.md)