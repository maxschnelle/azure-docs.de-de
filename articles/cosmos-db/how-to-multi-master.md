---
title: Konfigurieren von Schreibvorgängen in mehreren Regionen in Azure Cosmos DB
description: Erfahren Sie, wie Sie mithilfe verschiedener SDKs in Azure Cosmos DB Schreibvorgänge in mehreren Regionen konfigurieren.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/10/2020
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 95337f88133c9493250e9197654288dc0af59ed1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486139"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurieren von Schreibvorgängen in mehreren Regionen in Ihren Anwendungen, die Azure Cosmos DB verwenden

Nachdem ein Konto mit mehreren aktivierten Schreibregionen erstellt wurde, müssen Sie in Ihrer Anwendung in ConnectionPolicy für DocumentClient zwei Änderungen vornehmen, um die Funktionen für Schreibvorgänge in mehreren Regionen und Multihoming in Azure Cosmos DB zu aktivieren. Legen Sie in ConnectionPolicy das UseMultipleWriteLocations-Element auf TRUE fest, und übergeben Sie den Namen der Region, in der die Anwendung bereitgestellt wird, an SetCurrentLocation. Dadurch wird die PreferredLocations-Eigenschaft basierend auf der geografischen Nähe zum eingegebenen Standort aufgefüllt. Wenn dem Konto später eine neue Region hinzugefügt wird, muss die Anwendung nicht aktualisiert oder neu bereitgestellt werden. Sie erkennt automatisch die nähere Region und greift automatisch darauf zurück, wenn ein regionales Ereignis eintritt.

> [!Note]
> Cosmos-Konten, die ursprünglich mit einer einzelnen Schreibregion konfiguriert wurden, können ohne Ausfallzeiten für die Verwendung mehrerer Schreibregionen konfiguriert werden. Weitere Informationen finden unter [Konfigurieren mehrerer Schreibregionen](how-to-manage-database-account.md#configure-multiple-write-regions).

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Um in Ihrer Anwendung Schreibvorgänge in mehreren Regionen zu aktivieren, legen Sie `UseMultipleWriteLocations` auf `true` fest. Legen Sie außerdem `SetCurrentLocation` auf die Region fest, in der die Anwendung bereitgestellt wird und wo Azure Cosmos DB repliziert wird:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

Um in Ihrer Anwendung Schreibvorgänge in mehreren Regionen zu aktivieren, legen Sie `ApplicationRegion` auf die Region fest, in der die Anwendung bereitgestellt und Cosmos DB repliziert wird:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Optional können Sie `CosmosClientBuilder` und `WithApplicationRegion` verwenden, um das gleiche Ergebnis zu erzielen:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Java V4 SDK

Um in Ihrer Anwendung Schreibvorgänge in mehreren Regionen zu aktivieren, rufen Sie `.multipleWriteRegionsEnabled(true)` und `.preferredRegions(preferredRegions)` im Client-Generator auf. Dabei ist `preferredRegions` eine `List`, die ein Element enthält. Dies ist die Region, in der die Anwendung bereitgestellt und Cosmos DB repliziert wird:

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async-API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Sync](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync-API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> Async Java V2 SDK

Das Java V2 SDK verwendete Maven [com.microsoft.azure::azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Um in Ihrer Anwendung Schreibvorgänge in mehreren Regionen zu aktivieren, legen Sie `policy.setUsingMultipleWriteLocations(true)` fest. Legen Sie außerdem `policy.setPreferredLocations` auf die Region fest, in der die Anwendung bereitgestellt und Cosmos DB repliziert wird:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript und TypeScript SDK

Um in Ihrer Anwendung Schreibvorgänge in mehreren Regionen zu aktivieren, legen Sie `connectionPolicy.UseMultipleWriteLocations` auf `true` fest. Legen Sie außerdem `connectionPolicy.PreferredLocations` auf die Region fest, in der die Anwendung bereitgestellt wird und wo Cosmos DB repliziert wird:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Um in Ihrer Anwendung Schreibvorgänge in mehreren Regionen zu aktivieren, legen Sie `connection_policy.UseMultipleWriteLocations` auf `true` fest. Legen Sie außerdem `connection_policy.PreferredLocations` auf die Region fest, in der die Anwendung bereitgestellt wird und wo Cosmos DB repliziert wird.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel:

* [Verwalten von Konsistenzebenen in Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Konflikttypen und Konfliktauflösungsrichtlinien](conflict-resolution-policies.md)
* [Hochverfügbarkeit mit Azure Cosmos DB](high-availability.md)
* [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md)
* [Auswählen der richtigen Konsistenzebene](./consistency-levels.md)
* [Kompromisse in Bezug auf Konsistenz, Verfügbarkeit und Leistung](./consistency-levels.md)
* [Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen](./consistency-levels.md)
* [Globales Skalieren von bereitgestelltem Durchsatz](./request-units.md)
* [Globale Verteilung: Im Hintergrund](global-dist-under-the-hood.md)