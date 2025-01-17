---
description: Configuring event handlers in Umbraco UI Builder, the backoffice UI builder for Umbraco.
---

# Events

Umbraco UI Builder fires a number of notification events during regular operation to allow for extending of the default behaviour.

## Registering event handlers

Umbraco UI Builder uses the same [Notification Mechanism built into Umbraco v9+](../../umbraco-cms/fundamentals/code/subscribing-to-notifications.md) and so uses the same registration process. First you will need to define a notification event handler for the event you wish to handle like below:

```csharp
public class MyEntitySavingEventHandler :  INotificationHandler<KonstruktEntitySavingNotification> {

    public void Handle(ContentPublishedNotification notification)
    {
        // Handle the event here
    }

}
```

Then register your event handler in the `ConfigureServices` method of your `Startup.cs` file like below:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddUmbraco(_env, _config)
        .AddBackOffice()             
        .AddWebsite()
        .AddComposers()
        .AddNotificationHandler<KonstruktEntitySavingNotification, MyEntitySavingEventHandler>()
        .Build();

}
```

## Repository events

### **KonstruktEntitySavingNotification**

Raised when the repository `Save` method is called and before the entity has been persisted. The notification contains an `Entity` property with `Before` and `After` inner properties. These properties provide access to a copy of the currently persisted entity (or null if a new entity) and the updated entity that´s saved.
Changes can be made to the `After` entity and they will be persisted as part of the save operation. If the `Cancel` property of the notification is set to `true` then the save operation will be canceled and no changes will be saved.

````csharp
// Example
public class MyEntitySavingEventHandler :  INotificationHandler<KonstruktEntitySavingNotification> {

    public void Handle(KonstruktEntitySavingNotification notification)
    {
        var person = notification.Entity.After as Person;
        if (person != null){
            ...
        }
    }

}
````

### **KonstruktEntitySavedNotification**

Raised when the repository `Save` method is called and after the entity has been persisted. The notification contains an `Entity` property with `Before` and `After` inner properties. These properties provide access to a copy of the previously persisted entity (or null if a new entity) and the updated entity that´s saved.  

````csharp
// Example
public class MyEntitySavedEventHandler :  INotificationHandler<KonstruktEntitySavedNotification> {

    public void Handle(KonstruktEntitySavedNotification notification)
    {
        var person = notification.Entity.After as Person;
        if (person != null){
            ...
        }
    }

}
````

### **KonstruktEntityDeletingNotification**

Raised when the repository `Delete` method is called and **before** the entity is deleted. The notification contains an `Entity` property providing access to a copy of the entity about to be deleted. If the `Cancel` property of notification is set to `true` then the delete operation will be cancelled and entity won't be deleted.

````csharp
// Example
public class MyEntityDeletingEventHandler :  INotificationHandler<KonstruktEntityDeletingNotification> {

    public void Handle(KonstruktEntityDeletingNotification notification)
    {
        var person = notification.Entity.After as Person;
        if (person != null){
            ...
        }
    }

}
````

### **KonstruktEntityDeletedNotification**

Raised when the repository `Delete` method is called and **after** the entity has been deleted. The notification contains an `Entity` property providing access to a copy of the entity that´s deleted.

````csharp
// Example
public class MyEntityDeletedEventHandler :  INotificationHandler<KonstruktEntityDeletedNotification> {

    public void Handle(KonstruktEntityDeletedNotification notification)
    {
        var person = notification.Entity.After as Person;
        if (person != null){
            ...
        }
    }

}
````

### **KonstruktSqlQueryBuildingNotification**

Raised when the repository is **preparing** a SQL query. The notification contains the collection alias + type, the NPoco `Sql<ISqlContext>` object, and the where clause/order by clauses. These will be used to generate the SQL query.

````csharp
// Example
public class MySqlQueryBuildingEventHandler :  INotificationHandler<KonstruktSqlQueryBuildingNotification> {

    public void Handle(KonstruktSqlQueryBuildingNotification notification)
    {
        notification.Sql = notification.Sql.Append("WHERE MyId = @0", 1);
    }

}
````

### **KonstruktSqlQueryBuiltNotification**

Raised when the repository has **repaired** a SQL query. The notification contains the collection alias + type, the NPoco `Sql<ISqlContext>` object and the where clause/order by clauses that was used to generate the SQL query.

````csharp
// Example
public class MySqlQueryBuiltEventHandler :  INotificationHandler<KonstruktSqlQueryBuiltNotification> {

    public void Handle(KonstruktSqlQueryBuiltNotification notification)
    {
        notification.Sql = notification.Sql.Append("WHERE MyId = @0", 1);
    }

}
````
