---
title: Wählen zwischen bereitgestelltem Durchsatz und serverlos in Azure Cosmos DB
description: Erfahren Sie, wie Sie zwischen bereitgestelltem Durchsatz und serverlos für ihre Workload wählen.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: d16343864d9602d644b31d34a2b66e39211b6ece
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079337"
---
# <a name="how-to-choose-between-provisioned-throughput-and-serverless"></a>Wählen zwischen bereitgestelltem Durchsatz und serverlos
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB ist in zwei verschiedenen Kapazitätsmodi verfügbar: [bereitgestellter Durchsatz](set-throughput.md) und [serverlos](serverless.md). In beiden Modi können Sie genau die gleichen Datenbankvorgänge ausführen, aber die Abrechnung für diese Vorgänge unterscheidet sich radikal. Im folgenden Video werden die wichtigsten Unterschiede zwischen diesen Modi und deren Eignung für verschiedene Arten von Workloads erläutert:

> [!VIDEO https://www.youtube.com/embed/CgYQo6uHyt0]

> [!NOTE]
> Serverlos wird derzeit nur von der API Azure Cosmos DB Core (SQL) unterstützt.

## <a name="detailed-comparison"></a>Ausführlicher Vergleich

| Kriterien | Bereitgestellter Durchsatz | Serverlos |
| --- | --- | --- |
| Status | Allgemein verfügbar | In der Vorschau |
| Am besten geeignet für | Unternehmenswichtige Workloads, die vorhersagbare Leistung erfordern | Kleine bis mittlere nicht kritische Workloads mit geringem Datenverkehr |
| Funktionsweise | Für jeden Ihrer Container stellen Sie eine gewisse Durchsatzmenge bereit, ausgedrückt in [Anforderungseinheiten](request-units.md) pro Sekunde. Jede Sekunde steht diese Menge von Anforderungseinheiten für Ihre Datenbankvorgänge zur Verfügung. Bereitgestellter Durchsatz kann manuell aktualisiert oder automatisch mit [Autoskalierung](provision-throughput-autoscale.md) angepasst werden. | Sie führen die Datenbankvorgänge für Ihre Container aus, ohne Kapazität bereitstellen zu müssen. |
| Einschränkungen pro Konto | Maximale Anzahl von Azure-Regionen: unbegrenzt | Maximale Anzahl von Azure-Regionen: 1 |
| Einschränkungen pro Container | Maximaler Durchsatz: unbegrenzt<br>Maximaler Speicher: unbegrenzt | Maximaler Durchsatz: 5.000 RU/s<br>Maximaler Speicher: 50 GB |
| Leistung | 99,99 % bis 99,999 % Verfügbarkeit abgedeckt durch SLA<br>< 10 ms Latenz für Punktlese- und Schreibvorgänge abgedeckt durch SLA<br>99,99 % garantierter, durch SLA abgedeckter Durchsatz | 99,9 % bis 99,99 % Verfügbarkeit abgedeckt durch SLA<br>< 10 ms Latenz für Punktlese- und < 30 ms für Schreibvorgänge abgedeckt durch SLA<br>95 % durch SLO abgedeckte Burstfähigkeit |
| Abrechnungsmodell | Die Abrechnung erfolgt auf Stundenbasis für die bereitgestellten RU/s und unabhängig davon, wie viele RUs verbraucht wurden. | Die Abrechnung erfolgt auf Stundenbasis für die Menge an RUs, die von den Datenbankvorgängen beansprucht werden. |

> [!IMPORTANT]
> Einige dieser serverlosen Einschränkungen können gelockert oder aufgehoben werden, wenn serverlos allgemein verfügbar wird, und **Ihr Feedback** wird uns bei der Entscheidung unterstützen! Nehmen Sie Kontakt mit uns auf, und erzählen Sie uns mehr über Ihre Erfahrungen mit serverlos: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com).

## <a name="burstability-and-expected-consumption"></a>Burstfähigkeit und erwartete Nutzung

In manchen Situationen kann es unklar sein, ob für eine bestimmte Workload bereitgestellter Durchsatz oder serverlos gewählt werden sollte. Zur Unterstützung dieser Entscheidung können Sie Folgendes schätzen:

- Die Anforderung an die **Burstfähigkeit** der Workload, d. h. die maximale Menge an RUs, die Sie in einer Sekunde nutzen müssen.
- Ihr gesamter **erwarteter Verbrauch** , d. h. die Gesamtzahl der RUs, die Sie im Laufe eines Monats ggf. nutzen (Sie können diesen Wert mithilfe der [hier](plan-manage-costs.md#estimating-serverless-costs) gezeigten Tabelle schätzen)

Wenn Ihre Workload einen Burst über 5.000 RU pro Sekunde erfordert, sollte bereitgestellter Durchsatz gewählt werden, da serverlose Container nicht über diesen Grenzwert hinausgehen können. Andernfalls können Sie die Kosten beider Modi basierend auf dem erwarteten Verbrauch vergleichen.

**Beispiel 1** : Es wird davon ausgegangen, dass eine Workload auf maximal 10.000 RU/s anwächst und insgesamt 20 Millionen RUs im Laufe eines Monat beansprucht.

- Nur der Modus „bereitgestellter Durchsatz“ kann einen Durchsatz von 10.000 RU/s bereitstellen.

**Beispiel 2** : Es wird davon ausgegangen, dass eine Workload auf maximal 500 RU/s anwächst und insgesamt 20 Millionen RUs im Laufe eines Monat beansprucht.

- Im Modus „bereitgestellter Durchsatz“ würden Sie einen Container mit 500 RU/s mit den folgenden monatlichen Kosten bereitstellen: 0,008 USD * 5 * 730 = **29,20 USD**
- Im serverlosen Modus zahlen Sie folgendermaßen für die verbrauchten RUs: 0,25 USD * 20 = **5,00 USD**

**Beispiel 3** : Es wird davon ausgegangen, dass eine Workload auf maximal 500 RU/s anwächst und insgesamt 250 Millionen RUs im Laufe eines Monat beansprucht.

- Im Modus „bereitgestellter Durchsatz“ würden Sie einen Container mit 500 RU/s mit den folgenden monatlichen Kosten bereitstellen: 0,008 USD * 5 * 730 = **29,20 USD**
- Im serverlosen Modus zahlen Sie folgendermaßen für die verbrauchten RUs: 0,25 USD * 250 = **62,50 USD**

(diese Beispiele berücksichtigen nicht die Speicherkosten, die für beide Modi gleich hoch sind)

> [!NOTE]
> Die im vorherigen Beispiel gezeigten Kosten dienen nur zu Demonstrationszwecken. Aktuelle Preisinformationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Bereitstellen von Durchsatz in Azure Cosmos DB](set-throughput.md)
- Weitere Informationen zu [Azure Cosmos DB serverlos](serverless.md)
- Einführung in das Konzept von [Anforderungseinheiten](request-units.md)
