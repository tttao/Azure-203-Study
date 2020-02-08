Create table

```java
string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());
CloudTable table = tableClient.GetTableReference(tableName);
```

Define entity

```java
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Insert or update

```java
TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);
TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
CustomerEntity insertedCustomer = result.Result as CustomerEntity;
```

Insert or update (batch)

```
TableBatchOperation batchOperation = new TableBatchOperation();
batchOperation.insertOrReplace(entity1);
batchOperation.insertOrReplace(entity2);
TableBatchResult results = await table.ExecuteBatchAsync(batchOperation);
```


Get By PartitionKey / RowKey

```java
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
TableResult result = await table.ExecuteAsync(retrieveOperation);
CustomerEntity customer = result.Result as CustomerEntity;
```

Delete

```java
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
TableResult result = await table.ExecuteAsync(deleteOperation);
```

https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started

