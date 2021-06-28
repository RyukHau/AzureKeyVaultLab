概述
===

專案開發有時需要使用 Key 或 Token

假如流出這些資訊，將會造成資安上的疑慮

故我們可以使用 Azure Key Vault 進行保存

文章將會使用 ASP.NET Core Web API (.Net 5) 進行實作

正文
===

使用 Virtual Studio 建立一個 ASP.Net Core Web API (.Net 5) 的專案

然後安裝 Microsoft Extensions Configuration Azure Key Vault 的 NuGet

```
install-package microsoft.extensions.configuration.azurekeyvault
```

完成後， 在 Program.cs 內加入執行 Azure Key Vault 方法以授權專案可以取得 Azure Key Vault 內的 Secret，並且需要知道 Azure Key Vault 的連線， App Register 註冊後的 Client Id 與 Client Secret

```
public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var root = config.Build();
                    config.AddAzureKeyVault($"https://{root["KeyVault:Vault"]}.vault.azure.net/", root["KeyVault:ClientId"], root["KeyVault:ClientSecret"]);
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
```

完成後， 在 AppSettings.json 假如

```
"KeyVault": {
    "Vault": "Azure Key Vault Name",
    "ClientId": "Client Id (Application Id)",
    "ClientSecret": "Client Secret"
  },
```

* Vault : Azure Key Vault 的名稱

* Client Id : App Register 登記應用程式後的 Client Id (Application Id)

* Client Secret : App Register 登記應用程式後建立的 Client Secret

之後建立一個 Controller 並使用以下資訊

```
private readonly IConfiguration _configuration;

        public ValuesController(IConfiguration configuration)
        {
            _configuration = configuration;
        }

        [HttpGet]
        public string Get()
        {
        	// test 為 Secret 名稱
            var value = _configuration["test"];
            return $"Value for secret [test] is : {value}";
        }
```

完成後，我們僅需填入相關資訊即可取得 Secret。

以上即需要的相關資訊

參考鏈接
---

* Easily Read Key Vault Secrets From ASP.NET Core Web API Application - https://www.c-sharpcorner.com/article/easily-read-key-vault-secrets-from-asp-net-core-web-api-application/
* Create Azure Key Vault - https://docs.microsoft.com/en-us/azure/key-vault/general/quick-create-portal
* App Register - https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app

[Link](https://dotblogs.com.tw/ryuk/2021/06/27/150608)