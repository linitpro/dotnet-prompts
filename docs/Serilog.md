# Serilog
## Конфигурация appsettings.json
### Формат записи и куда писать
В формате Elasticsearch. Необходим пакет [https://www.nuget.org/packages/Serilog.Sinks.Elasticsearch]()
~~~json
{
  "WriteTo": [
    {
      "Name": "Console",
      "Args": {
        "formatter": "Serilog.Formatting.Elasticsearch.ElasticsearchJsonFormatter,Serilog.Formatting.Elasticsearch"
      }
    }
  ]
}
~~~

В классическом формате (полезно для дебага)
~~~json
{
  "WriteTo": [
    {
      "Name": "Console",
      "Args": {
        "theme": "Serilog.Sinks.SystemConsole.Themes.AnsiConsoleTheme::Code, Serilog.Sinks.Console",
        "outputTemplate": "[{Timestamp:HH:mm:ss} {Level:u3}] {Message:lj} <s:{SourceContext}>{NewLine}{Exception}"
      }
    }
  ]
}
~~~