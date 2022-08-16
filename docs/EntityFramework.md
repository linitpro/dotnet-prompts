# EntityFramework
## Конфигурирование

В формате Elasticsearch. Необходим пакет [https://www.nuget.org/packages/Serilog.Sinks.Elasticsearch]()
#### appsettings.json
~~~json
{
    "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=achivka;User Id=postgres;Password=123456;Port=5432"
    }
}
~~~


#### ConnectionStringsSettings.cs
~~~csharp
public class ConnectionStringsSettings
{
    public string DefaultConnection { get; set; }
}
~~~

#### ConnectionStringsSettings.Options.cs
~~~csharp
public class ConnectionStringsSettingsOptions: IConfigureNamedOptions<ConnectionStringsSettings>
{
    public void Configure(ConnectionStringsSettings options)
    {
        this.Configure("", options);
    }

    public void Configure(string? name, ConnectionStringsSettings options)
    {
        // ... манипулирование данными
        // options.Value = options.Value + "fooBar";
    }
}
~~~


#### Program.cs
~~~csharp
services.Configure<ConnectionStringsSettings>(opts => configuration.GetSection("ConnectionStrings").Bind(opts));
services.AddSingleton<IConfigureOptions<ConnectionStringsSettings>, ConnectionStringsSettingsOptions>();

services
    .AddEntityFrameworkNpgsql()
    .AddDbContextFactory<DataContext>()
    .AddDbContext<DataContext>();
~~~

#### DataContext.cs
~~~csharp
public class DataContext: DbContext
{
    private readonly ConnectionStringsSettings connectionStringsSettings;
    
    public DataContext(
        DbContextOptions<DataContext> options, 
        IOptions<ConnectionStringsSettings> connectionStrings): base(options)
    {    
        this.connectionStringsSettings = connectionStrings.Value;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseNpgsql(this.connectionStringsSettings.DefaultConnection, b =>
         {
             // b.SetPostgresVersion(new Version(major, minor));
             b.MigrationsAssembly("Project.Assembly");
             b.MigrationsHistoryTable("migrations_history", "main");
         });
    }
}
~~~