# 替所有的API新增401回應的Response

## 1. 新增GlobalApiDescriptionProvider

這個`Provider`會在每個API的描述中，新增401回應，但不會包含描述內容。

```csharp

public class GlobalApiDescriptionProvider : IApiDescriptionProvider
{
    public int Order => 0;

    public void OnProvidersExecuting(ApiDescriptionProviderContext context)
    {
        foreach (var description in context.Results)
        {
            if (description.ActionDescriptor.EndpointMetadata.OfType<AllowAnonymousAttribute>().Any())
                continue;

            var unauthorizedResponse = description.SupportedResponseTypes
                .FirstOrDefault(r => r.StatusCode == StatusCodes.Status401Unauthorized);

            if (unauthorizedResponse == null)
            {
                description.SupportedResponseTypes.Add(new ApiResponseType
                {
                    StatusCode = StatusCodes.Status401Unauthorized,
                    Type = typeof(void),
                    ApiResponseFormats =
                    {
                        new ApiResponseFormat
                        {
                            MediaType = "application/json"
                        }
                    }
                });
            }
        }
    }

    public void OnProvidersExecuted(ApiDescriptionProviderContext context) { }
}
```

## 2. 新增GlobalUnauthorizedResponseHeaderFilter

這個`Filter`會在每個API的回應中，新增401回應的`Header`描述，會比較貼近預設的回應格式。(可選)

```csharp
public class GlobalUnauthorizedResponseHeaderFilter : IOperationFilter
{
    public void Apply(OpenApiOperation operation, OperationFilterContext context)
    {
        var hasAllowAnonymous = context.ApiDescription.ActionDescriptor.EndpointMetadata
            .OfType<AllowAnonymousAttribute>().Any();

        if (hasAllowAnonymous)
            return;

        var headers = new Dictionary<string, OpenApiHeader>
        {
            ["Content-Length"] = new OpenApiHeader
            {
                Description = "Response content length",
                Schema = new OpenApiSchema { Type = "integer", Format = "int32" }
            },
            ["Date"] = new OpenApiHeader
            {
                Description = "Date when the response is generated",
                Schema = new OpenApiSchema { Type = "string", Format = "date-time" }
            },
            ["Server"] = new OpenApiHeader
            {
                Description = "Server name",
                Schema = new OpenApiSchema { Type = "string" }
            },
            ["WWW-Authenticate"] = new OpenApiHeader
            {
                Description = "Authentication 的錯誤訊息",
                Schema = new OpenApiSchema { Type = "string" }
            }
        };

        foreach (var header in headers)
        {
            operation.Responses["401"].Headers[header.Key] = header.Value;
        }
    }
}
```

3. 註冊方法

```csharp

service.AddSingleton<IApiDescriptionProvider, GlobalApiDescriptionProvider>();
service.AddSwaggerGen(uiAnnotation =>
{
    uiAnnotation.ExampleFilters(); // 需要有這個才能啟用ExampleFilter
    uiAnnotation.OperationFilter<GlobalUnauthorizedResponseHeaderFilter>();
});
```

## 4. 如何排除

如果有些API不想要加入401回應，可以在Metadata上加上`AllowAnonymousAttribute`，這樣就不會被加入401回應。

```csharp

group.MapPost("/", Create)
    .RequireAuthorization(nameof(RolePolicies.Standard))
    .WithMetadata(new AllowAnonymousAttribute());
```

---

### 更新紀錄

#### **建立時間：** 2025-03-03-12:36

#### **修改時間：** 2025-03-03-12:36

#### 版本紀錄

- Version: 1.0.0
  Note: 初版
