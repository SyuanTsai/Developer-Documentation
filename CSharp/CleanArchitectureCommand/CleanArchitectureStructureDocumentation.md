# Clean Architecture 結構說明 (.NET 9.0)

## 目錄

- [Clean Architecture 結構說明 (.NET 9.0)](#clean-architecture-結構說明-net-90)
  - [目錄](#目錄)
  - [1. `src` 資料夾 (主要業務邏輯與應用程式核心)](#1-src-資料夾-主要業務邏輯與應用程式核心)
    - [Application 專案](#application-專案)
    - [Domain 專案](#domain-專案)
    - [Infrastructure 專案](#infrastructure-專案)
    - [Web 專案](#web-專案)
  - [2. `tests` 資料夾 (測試專案)](#2-tests-資料夾-測試專案)
    - [Application.FunctionalTests](#applicationfunctionaltests)
    - [Application.UnitTests](#applicationunittests)
    - [Domain.UnitTests](#domainunittests)
    - [Infrastructure.IntegrationTests](#infrastructureintegrationtests)
    - [Web.AcceptanceTests](#webacceptancetests)
  - [3. `template` 資料夾](#3-template-資料夾)
  - [完整目錄結構](#完整目錄結構)

---

## 1. `src` 資料夾 (主要業務邏輯與應用程式核心)

### Application 專案

負責應用程式的業務邏輯，包含 Use Cases（用例）或 Application Services。

- **Common**: 放置共用功能或工具類別，例如輔助工具（Helpers）、擴充方法（Extension Methods）。

### Domain 專案

代表業務核心，包含企業的業務規則與邏輯，是最純粹且最少依賴的部分。

- **Common**: 共用基礎類別或抽象類別，例如基礎實體（Base Entity）、聚合根（Aggregate Root）。
- **Constants**: 定義應用常數，避免硬編碼（magic numbers/strings）。
- **Entities**: 核心業務實體，代表實際業務對象。
- **Enums**: 定義業務相關的列舉類型，清楚表達業務邏輯。
- **Events**: 定義領域事件（Domain Events），用於系統內部通知。
- **Exceptions**: 自訂例外類別，處理業務邏輯錯誤。
- **ValueObjects**: 定義值物件（Value Objects），例如貨幣、地址等不可變且無唯一標識的物件。

### Infrastructure 專案

負責與外部系統（如資料庫、檔案系統、第三方 API）的互動，並實作 Domain 層定義的介面。

- **Data**: 處理資料存取邏輯，負責資料庫操作。
  - **Configurations**: 設定 Entity Framework 或其他 ORM 的資料表與實體對應關係。
  - **Interceptors**: 資料庫攔截器，用於日誌紀錄、審計等攔截邏輯。
  - **Migrations**: 資料庫遷移檔案，進行資料庫結構版本控制。

- **Services**: 提供基礎設施服務，例如郵件服務、檔案系統存取等。
  - **BackgroundServices**: 背景服務，用於執行長時間運行的背景作業。
  
### Web 專案

應用程式的外部介面，通常是 Web API 或 MVC 應用。

- **Endpoints**: 定義 API 端點，通常為控制器（Controllers）或 Minimal API。
- **Infrastructure**: Web 層特有的基礎建設，包含驗證、授權或其他設定。
- **Services**: 定義 Web 層專屬服務，例如 API 回應格式處理、錯誤處理等。

---

## 2. `tests` 資料夾 (測試專案)

### Application.FunctionalTests

測試應用層的功能是否如預期運作。

- **TodoItemsTests.cs**: 測試 Todo 項目的功能。

### Application.UnitTests

針對應用層的個別功能進行單元測試。

- **TodoItemsServiceTests.cs**: 驗證 Todo 服務的正確性。

### Domain.UnitTests

檢查領域層核心業務邏輯的單元測試。

- **EntitiesTests.cs**: 驗證基本實體行為。

### Infrastructure.IntegrationTests

進行整合測試，確保資料存取邏輯與外部系統互動正常。

- **DataAccessTests.cs**: 測試資料庫操作的正確性。

### Web.AcceptanceTests

測試 Web 層 API 端點的功能與使用者驗收標準是否一致。

- **ApiEndpointsTests.cs**: 驗證 API 端點的功能正確性。

---

## 3. `template` 資料夾

可能包含範本或腳手架工具，用於生成新的模組或專案。

- **Dependencies**: 定義範本專案所需依賴或配置。

---

## 完整目錄結構

```plaintext
CleanArchitecture/
│
├── infra
│   ○ 執行環境或系統相關資源。
│
├── src
│   ├── Application
│   │   ○ 應用層，包含問題解決策略、應保持商業邏輯純粹
│   │
│   ├── Domain
│   │   ○ 定義核心業務邏輯與模型。
│   │
│   ├── Infrastructure
│       ├── Data
│       │   ○ 資料存取相關實作。
│       │
│       ├── Identity
│       │   ○ 身份驗證與授權功能。
│       │
│       ├── DependencyInjection.cs
│       │   ○ 設定依賴注入。
│       │
│       ├── GlobalUsings.cs
│       │   ○ 設定共用命名空間。
│       │
│       └── Infrastructure.csproj
│           ○ 資料層專案設定。
│
├── Web
│   ├── ClientApp-React
│   │   ○ React 前端應用程式。
│   │
│   ├── ClientApp
│   │   ○ 額外前端應用。
│   │
│   ├── Endpoints
│   │   ○ API 端點定義與管理、僅應負責 API 入口。
│   │
│   ├── Infrastructure
│   │   └── Services
│   │       ○ 背景服務實作。
│   │       ○ Web 層服務實作。
│   │
│   ├── Pages
│   │   ○ Razor 頁面模板。
│   │
│   ├── Properties
│   │   └── launchSettings.json
│   │       ○ 執行環境配置。
│   │
│   ├── Services
│   │   ○ Web 層服務定義與實作。
│   │
│   ├── Templates
│   │   ○ 前端模板。
│   │
│   └── wwwroot
│       ├── css
│       │   ○ CSS 樣式檔。
│       │
│       ├── js
│       │   ○ JavaScript 檔案。
│       │
│       └── images
│           ○ 圖片資源。
│
├── templates
│   └── ca-use-case
│       ├── .template.config
│       │   ○ 模板配置檔。
│       │
│       └── FeatureName
│           ├── Commands
│           │   └── CleanArchitectureCommand.cs
│           │       ○ 範例命令實作。
│           │
│           └── Queries
│               └── CleanArchitectureUseCaseQuery.cs
│                   ○ 範例查詢實作。
│
└── tests
    ├── Application.FunctionalTests
    │   └── TodoItemsTests.cs
    │       ○ 應用層功能測試。
    │
    ├── Application.UnitTests
    │   └── TodoItemsServiceTests.cs
    │       ○ 應用層單元測試。
    │
    ├── Domain.UnitTests
    │   └── EntitiesTests.cs
    │       ○ 領域層單元測試。
    │
    ├── Infrastructure.IntegrationTests
    │   └── DataAccessTests.cs
    │       ○ 資料存取整合測試。
    │
    └── Web.AcceptanceTests
        └── ApiEndpointsTests.cs
            ○ API 端點驗收測試。
```

