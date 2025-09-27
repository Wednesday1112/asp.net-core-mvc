# asp.net core mvc 自學紀錄
參考影片https://www.youtube.com/watch?v=_oRJ8TNcHWo&list=PLneJIGUTIItsCaiHCd8Rte8kM0fIlyM2f&pp=0gcJCaIEOCosWNin
- [MVC 架構](#mvc-架構)
  - [Program.cs (路由)](#programcs-路由)
- [Database](#database)
  - [Data First](#data-first)
  - [Code First](#code-first)
  - [使用資料庫物件取得資料](#使用資料庫物件取得資料)
- [View](#view)
  - [ViewData](#viewdata)
  - [ViewBag](viewdbag)
  - [他們同個變數會共用](#他們同個變數會共用)
  - [方便性](#方便性)
  - [生命週期](#生命週期)
  - [TempData](#tempdata)
  - [架構的執行順序](#架構的執行順序)
  - [_ViewStart](#_viewstart)
- [Razor 基本語法](#razor-基本語法)
- [CRUD](#crud)
  - [join](#join)
  - [範例的 create 功能](#範例的-create-功能)

## MVC 架構
<img width="593" height="207" alt="image" src="https://github.com/user-attachments/assets/4275666d-a3a1-4e08-a31c-c47db00e81ff" /><br/>
檔案架構長這樣，有什麼 controller 就要有什麼 view 下的資料夾，<br/>
<img width="230" height="450" alt="image" src="https://github.com/user-attachments/assets/5da67d11-b319-4fa2-aeb4-d51b692d2530" /><br/>
有什麼 view，controller 就要有什麼函式<br/>
<img width="1261" height="512" alt="image" src="https://github.com/user-attachments/assets/17b7b310-5620-4de2-85b9-eaa2af85dcfa" />

### Program.cs (路由)
pattern 是網址路徑，controller 名字 / view 名字 / ID，有等於的代表胡果是這個就可以省略<br/>
圖中 controller = Home 代表 contrler 名字是 Home 就可以省略，後面 view 一樣意思<br/>
ID 的問號代表可有可無，沒有打上 ID，網址一樣可用，ID 是錯的也可用<br/>
<img width="918" height="449" alt="image" src="https://github.com/user-attachments/assets/0566413c-b78a-4bc7-90fa-06bb8c96fe2d" /><br/>
下面兩張圖是一樣的網頁(注意網址)<br/>
<img width="1316" height="459" alt="image" src="https://github.com/user-attachments/assets/dbccbdc9-6597-4cd9-80a8-9cf6b9d2f212" /><br/>
<img width="1230" height="436" alt="image" src="https://github.com/user-attachments/assets/2bee10d8-4a7c-4a9b-9d76-3c083bbcd997" />

## Database
使用套件：
- Microsoft.EntityFrameworkCore.SqlServer
- Microsoft.EntityFrameworkCore.Tools

### Data First
把已經建好的資料庫弄到專案裡的 Model<br/>
在套件管理主控台輸入以下指令以連接至資料庫<br/>
! 注意 ! 程式碼要確定沒有錯誤，不然指令輸入後會出錯，他也不會告訴你錯在哪
```
Scaffold-DbContext "Server=伺服器位置;Database=資料庫;User ID=帳號;Password=密碼;TrustServerCertificate=true" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -NoOnConfiguring -UseDatabaseNames -NoPluralize -Force
```
TrustServerCertificate=true：如果出現 "此憑證鏈結是由不受信任的授權單位發出的" 可以在連線字串裡加這個參數<br/>
-OutputDir Models：指說要將相關檔案產生在這個資料夾底下<br/>
-NoOnConfiguring：DbContext不要產生OnConfiguring片段，現在還不會用到<br/>
-UseDatabaseNames：使用跟資料庫一樣的大小寫命名，不然可能大小寫會被改成別的風格<br/>
<img width="903" height="522" alt="image" src="https://github.com/user-attachments/assets/2774ebbd-fc8b-4dbe-9804-0dbf682ec31c" /><br/>
-NoPluralize：不要加複數s，不然會幫你在命名結尾上加上s<br/>
<img width="883" height="519" alt="image" src="https://github.com/user-attachments/assets/fa7b2ddc-ee52-451c-b837-a0ccb1efe79a" /><br/>
-Force：是如果此位置已有相同檔案時覆蓋，就算沒檔案也可以多這個參數<br/>

### Code First
把專案裡打好的 Model，弄到資料庫<br/>
在 Modle 資料夾建立類別<br/>
<img width="1317" height="750" alt="image" src="https://github.com/user-attachments/assets/a4ce1cce-1843-4e24-923b-667699577679" /><br/>
打上欄位
```cs
namespace Kcg.Models;

public partial class TOPMenu
{
    public Guid TOPMenuId { get; set; }

    public string Name { get; set; }

    public string Url { get; set; }

    public string Icon { get; set; }
    
    public int Orders { get; set; }
}
```
再來一樣在 Model 建立一個類別，名稱為 資料庫名Context
```cs
public partial class KcgContext : DbContext //繼承 DbContext
{
    public KcgContext(DbContextOptions<KcgContext> options) : base(options)
    {
    }

    public DbSet<TOPMenu> TOPMenu { get; set; } //建立資料表
}
```
在 appsettings.json (參數檔)打上連線字串
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "KcgDatabase": "Server=伺服器位置;Database=資料庫名稱;User ID=使用者名稱;Password=密碼;TrustServerCertificate=true"
  }
}
```
在 Program.cs 加入資料庫物件的DI注入<br/>
UseSqlServer() 裡的一整串代表去 appsettings 抓連線字串(Server=伺服器位置;Database=資料庫名稱;User ID=使用者名稱;Password=密碼;TrustServerCertificate=true)
```cs
builder.Services.AddDbContext<KcgContext>(options =>
options.UseSqlServer(builder.Configuration.GetConnectionString("KcgDatabase")));
```
在套件管理主控台輸入以下指令，建立記錄檔(InitialCreate 是自己取的名字)
```
Add-Migration InitialCreate
```
在套件管理主控台輸入以下指令，更新資料庫，這時資料庫才會有剛剛打的資料表
```
Update-Database
```
新增資料表特性
```cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<TOPMenu>(entity =>
    {
        entity.Property(e => e.TOPMenuId).HasDefaultValueSql("(newid())"); //預設值或繫結
        entity.Property(e => e.Icon).IsRequired().HasMaxLength(50);
        entity.Property(e => e.Name).IsRequired().HasMaxLength(50);
        entity.Property(e => e.Url).IsRequired().HasMaxLength(50);
        entity.Property(e => e.Orders).IsRequired();
    });
}
```
再來要建立記錄檔，表示資料表有更新(一樣 TOPMenuUp 是自己取的名字)
```
Add-Migration TOPMenuUp
```
最後再 update 資料庫
```
Update-Database
```
新增資料到資料表
```cs
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<TOPMenu>().HasData(
        new TOPMenu { Name = "網站導覽", Url = "a1", Icon = "Icon1", Orders = 1 },
        new TOPMenu { Name = "回首頁", Url = "a2", Icon = "Icon2", Orders = 2 },
        new TOPMenu { Name = "English", Url = "a3", Icon = "Icon3", Orders = 3 },
        new TOPMenu { Name = "雙語詞彙", Url = "a4", Icon = "Icon4", Orders = 4 },
        new TOPMenu { Name = "市長信箱", Url = "a5", Icon = "Icon5", Orders = 5 },
        new TOPMenu { Name = "洽公導覽", Url = "a6", Icon = "Icon6", Orders = 6 });

    modelBuilder.Entity<TOPMenu>(entity =>
    {
        entity.Property(e => e.TOPMenuId).HasDefaultValueSql("(newid())");
        entity.Property(e => e.Icon).IsRequired().HasMaxLength(50);
        entity.Property(e => e.Name).IsRequired().HasMaxLength(50);
        entity.Property(e => e.Url).IsRequired().HasMaxLength(50);
        entity.Property(e => e.Orders).IsRequired();
    });
}
```
更新資料庫
```
Add-Migration AddData
```
```
Update-Database
```

### 使用資料庫物件取得資料
先在 controller 的全域宣告一個資料庫物件
```cs
private readonly KcgContext _kcgContext; //先在全域宣告資料庫物件

public HomeController(KcgContext kcgContext) //這邊是依賴注入，使用剛剛在 code first 的 Program.cs 設定好的資料庫物件的寫法
{
    _kcgContext = kcgContext; //這樣後面都可以藉由 _kcgContext 來取資料庫資料
}
```
例如我要回傳 TOPMenu 資料表的第一筆資料的 Name
```cs
public string Index()
{
    return _kcgContext.TOPMenu.FirstOrDefault().Name;
}
```
結果<br/>
<img width="181" height="123" alt="image" src="https://github.com/user-attachments/assets/e31b3d47-636d-4e1c-929e-074e118277d6" />

## View
### ViewData
controller
```cs
public IActionResult index()
{
    ViewData["要傳的變數"] = 要傳得值;

    return View();
}
```
view\controllerName\index
```cs
@ViewData["要傳的變數"] //Razor語法，想要在這用c#語法或變數時要加@
```

### ViewBag
controller
```cs
public IActionResult index()
{
    ViewBag.要傳的變數 = 要傳得值;

    return View();
}
```
view\controllerName\index
```cs
@ViewBag.要傳的變數 //Razor語法，想要在這用c#語法或變數時要加@
```

### 他們同個變數會共用
controller
```cs
public IActionResult index()
{
    ViewData["title"] = "這是 ViewDate 設定的值";
    ViewBag.Title = "這是 ViewBag 設定的值"; //這裡 T 我用大寫

    return View();
}
```
view\controllerName\index
```cshtml
@ViewData["title"]
<br/>
@ViewBag.Title
```
結果兩個變數被視為同一個<br/>
<img width="675" height="218" alt="image" src="https://github.com/user-attachments/assets/e637c555-66a4-49a4-b7d1-dcc55a29e829" />

### 方便性
ViewData 跟 ViewBag 不用事先宣告，可以直接使用，但會造成程式碼雜亂不好維護，所以要少用，或用在簡單的地方，例如<title/><br/>
! 注意 ! ViewData 稍微嚴謹一點
```cs
public IActionResult Index()
{
    ViewData["number1"] = 10;
    int ViewDataNum = 10 + (int)ViewData["number1"]; //ViewData 比較嚴謹，要給型別

    ViewBag.number2 = 10;
    int ViewBagNum = 10 + ViewBag.number2;

    return View();
}
```

### 生命週期
兩個的生命週期只有一個 request，下一個 request 又是新的變數，例如我在另一個 view 使用 @ViewData["number1"]，雖然我有先載入 index 頁面，但另一個 view 就是另一個 request 了，所以另一個 view 印不出 ViewData["number1"] 的值

### TempData
用起來跟 ViewData 差不多
```cs
public IActionResult index()
{
    TempData["Temp"] = "這是 Temp 設定的值";

    return View();
}
```
他預設的生命週期是使用過一次才會消失，例如我在另一個 view 使用 @TempData["Temp"]，然後先載入 index 頁面(index 的 view 沒有印 TempData)，在載入另一個 view，TempData就出來了，但我重新整理後又不見了，因為 TempData 使用過一次就會消失，除非再載入 index 一次<br/>
先去 Home 再去 Privacy<br/>
<img width="807" height="277" alt="image" src="https://github.com/user-attachments/assets/f23d1b81-129b-4bed-a222-1d92fd2615ce" /><br/>
重新整理<br/>
<img width="781" height="297" alt="image" src="https://github.com/user-attachments/assets/ba460bd9-00f1-43ba-b1b9-4c2a4ced5c3c" /><br/>
但只要加上 TempData.Keep() 就能讓他再存在一次
```cs
public IActionResult Privacy()
{
    TempData.Keep("Temp");

    return View();
}
```
先去 Home 再去 Privacy，不管重新整理幾次他都還在，可是這時候如果我讓另一個頁面也使用 Temp，而且沒有 TempData.Keep()，他就只會出現一次，重新整理或回到 Privacy 都不會出現 Temp 了，除非回到 Home 重新設定<br/>
```cshtml
@TempData["Temp"]
```
View2 頁面<br/>
<img width="652" height="242" alt="image" src="https://github.com/user-attachments/assets/0c706422-db24-4a5f-b24a-386b032277aa" /><br/>
重新整理<br/>
<img width="778" height="320" alt="image" src="https://github.com/user-attachments/assets/74e2cdee-f6b9-4555-871b-631225377ce0" /><br/>
回到 Privacy 頁面<br/>
<img width="782" height="308" alt="image" src="https://github.com/user-attachments/assets/5993e569-8ade-484e-86bb-50e05b2aabe3" />

### 架構的執行順序
request > Controller > View > Layout(主版型)

### _ViewStart
設定主板型(版型放在View\Shared)
View\_ViewStart
```cshtml
@{
    var controller = ViewContext.RouteData.Values["controller"].ToString();

    if(controller == "Demo")
    {
        Layout = "_DemoLayout";
    }
    else
    {
        Layout = "_Layout";
    }
}
```
View\Shared\_DemoLayout
```cdhtml
<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">Demo</a> //讓左上角標題變Demo
```
<img width="1346" height="371" alt="image" src="https://github.com/user-attachments/assets/8c5a974e-3fee-4b2b-9398-5852723bf9cb" /><br/>
也可以再放一個 _ViewStart 在要改版型的 controller 的 view 資料夾裡，這樣會先執行外面的 _ViewStart，再執行裡面的 _ViewStart，裡面蓋掉外面的，所以最後 Demo 的版型會以裡面的為主<br/>
<img width="213" height="524" alt="image" src="https://github.com/user-attachments/assets/c6885f5d-0f66-4fe8-b4fc-ab1b1c8abe74" /><br/>
不想用 _ViewStart，只想要某個 view 用其他版型，可以直接在 index 裡使用
View\Demo\index
```cshtml
@{
    Layout = "_DemoLayout"; //Layout = null; 代表不套版型
}

@{
    ViewData["Title"] = "Home Page";
}

<div class="text-center">
    <h1 class="display-4">Welcome</h1>
    <p>Learn about <a href="https://learn.microsoft.com/aspnet/core">building Web apps with ASP.NET Core</a>.</p>
</div>
```

## Razor 基本語法
```cshtml
@{
    string title = "在 @{ } 中可以使用 c# 語法";
    int number = 5;
}
@title : @number
```
<img width="630" height="255" alt="image" src="https://github.com/user-attachments/assets/b497172f-c4d0-48b2-be01-e15b9e2b2acc" /><br/>
if 判斷式
```cshtml
@if(number > 0)
{
    //在這邊還是寫程式碼區塊
    <div>如果今天要在這裡面顯示文字到網頁上，就寫在html標籤裡面即可</div>
    <text>如果怕破壞格式不想用html標籤，可以用text，則不會留下痕跡</text>
}
else
{
    <div>number 小於等於 0</div>
}
```
text 沒有留下痕跡<br/>
<img width="1599" height="643" alt="image" src="https://github.com/user-attachments/assets/2c5e3c21-2139-4efb-bb7f-b2b3e30db1af" /><br/>
如果要在畫面上打小老鼠
```cshtml
<div>@@</div>//這樣會顯示一個@
```
<img width="493" height="193" alt="image" src="https://github.com/user-attachments/assets/9f25900e-1d3a-4809-af22-77a4840ffc05" /><br/>
或是
```cshtml
@{
    string 小老鼠 = "@"; //可以用中文當變數，超酷
}
<!-- 想要在@後面加文字 -->
<div>@(小老鼠)大老鼠</div>
```
<img width="537" height="230" alt="image" src="https://github.com/user-attachments/assets/fcea51af-f939-41c0-9d39-e3ac11e23815" />

## CRUD
藉著範例檔能使用新增、查詢、修改和刪除的功能<br/>
<img width="1920" height="920" alt="image" src="https://github.com/user-attachments/assets/57bb8505-6eda-4d7c-b143-fd711f8a1fe8" /><br/>
首先在 Controller 建立新的控制器<br/>
<img width="955" height="657" alt="image" src="https://github.com/user-attachments/assets/09b6a044-e00d-4b76-a361-acde4ac52afd" /><br/>
使用 Kcg 資料庫的 News 資料表<br/>
<img width="653" height="446" alt="image" src="https://github.com/user-attachments/assets/d3f08e58-6b40-4cda-ba56-5a628c8ab2b3" /><br/>
然後他會幫我們新增 Views 下的 News 資料夾，裡面直接就有新增、刪除、細節、編輯及顯示五個檔案<br/>
<img width="192" height="177" alt="image" src="https://github.com/user-attachments/assets/351d062b-d66b-4089-8d3e-e52cf5a30cd2" /><br/>
為了更方便檢視畫面，在上面標提列新增 NewsIndex<br/>
<img width="1915" height="738" alt="image" src="https://github.com/user-attachments/assets/b6a96c15-ba8f-4a56-acb2-1a27b67d4cd2" /><br/>
結果會跟第一張圖一樣，新增、刪除及編輯都會跟資料庫同步<br/>
<br/>
可以發現 Contents 欄位的文字特別長，所以我們讓他只在 Details 顯示<br/>
把這邊刪除只是讓他不顯示，但仍然有耗費資源效能抓資料<br/>
<img width="1913" height="752" alt="image" src="https://github.com/user-attachments/assets/01edf403-6cba-4cdb-885d-c1e9b24c77e8" />
<img width="1912" height="754" alt="image" src="https://github.com/user-attachments/assets/b64f8ac9-820d-47ea-8d80-a7e045cb6995" /><br/>
Controller 的這個寫法代表把 News 所有資料取下來<br/>
<img width="952" height="734" alt="image" src="https://github.com/user-attachments/assets/f9028de7-b98e-48cb-af98-0ce09bc9fe9b" /><br/>
改成一欄一欄篩選
```cs
var result = from a in _context.News
                 select new News
                 {
                     Click = a.Click,
                     DepartmentId = a.DepartmentId,
                     Enable = a.Enable,
                     EndDateTime = a.EndDateTime,
                     InsertDateTime = a.InsertDateTime,
                     InsertEmployeeId = a.InsertEmployeeId,
                     NewsId = a.NewsId,
                     StartDateTime = a.StartDateTime,
                     Title = a.Title,
                     UpdateDateTime = a.UpdateDateTime,
                     UpdateEmployeeId = a.UpdateEmployeeId
                 };
    return View(await result.ToListAsync());
```
啟動偵錯可以看到 SQL 語法<br/>
<img width="1351" height="160" alt="image" src="https://github.com/user-attachments/assets/2d7a95e6-cf96-4b39-acfc-c30fe9c37aed" />

### join
現在畫面上的 DepartmentId、InsertEmployeeId 跟 UpdateEmployeeId 欄位都只是代號，所以現在要 join 其他資料表的資料近來(先 DepartmentId 跟 UpdateEmployeeId)，把上面的程式碼改成下面這樣
```cs
var result = from a in _context.News
             join b in _context.Department on a.DepartmentId equals b.DepartmentId //join Department 資料表，且 DepartmentId 欄位要跟 a.DepartmentId 欄位一樣
             join c in _context.Employee on a.UpdateEmployeeId equals c.EmployeeId
             select new //去掉 News 型別，因為裡面不只有 News 資料表的資料
             {
                 Click = a.Click,
                 DepartmentId = a.DepartmentId,
                 Enable = a.Enable,
                 EndDateTime = a.EndDateTime,
                 InsertDateTime = a.InsertDateTime,
                 InsertEmployeeId = a.InsertEmployeeId,
                 NewsId = a.NewsId,
                 StartDateTime = a.StartDateTime,
                 Title = a.Title,
                 UpdateDateTime = a.UpdateDateTime,
                 UpdateEmployeeId = a.UpdateEmployeeId,
                 UpdateEmployeeName = c.Name,
                 DepartmentName = b.Name
             };
```
去掉 News 型別後，他就變無型別，這樣不好維護，所以使用DTO寫法，讓他加上強型別<br/>
<img width="222" height="334" alt="image" src="https://github.com/user-attachments/assets/2e0c791f-a6e5-4047-bd20-a658418142a9" />
```cs
namespace Kcg.Dtos
{
    public class NewsDto
    {
        //DTO 也可以做到減少不必要的欄位
        public Guid NewsId { get; set; }
        public string Title { get; set; }
        public string DepartmentName { get; set; }
        public DateTime StartDateTime { get; set; }
        public DateTime EndDateTime { get; set; }
        public DateTime UpdateDateTime { get; set; }
        public string UpdateEmployeeName { get; set; }
        public int Click { get; set; }
        public bool Enable { get; set; }
    }
}
```
回到 Controller，可以加上型別 NewsDto，把不必要的欄位刪掉<br/>
! 注意 ! 要 using 剛剛的 Dtos
```cs
using Kcg.Dtos
```
<img width="1643" height="730" alt="image" src="https://github.com/user-attachments/assets/5065bb6b-78aa-4de7-be20-ac29e4592b5f" /><br/>
Views/Index 的 model 要改成 NewsDto<br/> 
<img width="579" height="107" alt="image" src="https://github.com/user-attachments/assets/93a1bf20-27aa-4bdd-8923-a14651bc4044" /><br/>
會需要 using Dtos，放在 Views/Shared/_ViewImports.cshtml 可以省去一直 using 的麻煩，這邊打了，Views 底下其他檔案都有效
```cshtml
@using Kcg.Dtos
```
<img width="1877" height="751" alt="image" src="https://github.com/user-attachments/assets/68d21264-b6fe-4bb7-a452-9637b373299e" /><br/>
回到 Index 把欄位名改成剛剛 controller 設定的，以及把該刪的欄位刪掉<br/>
<img width="1017" height="731" alt="image" src="https://github.com/user-attachments/assets/157e3b21-d9c6-4695-b26d-83d47235c0fb" /><br/>
<img width="939" height="740" alt="image" src="https://github.com/user-attachments/assets/661efd6f-40d4-4d8d-860d-7ec274c32e27" /><br/>
結果圖<br/>
<img width="1649" height="751" alt="image" src="https://github.com/user-attachments/assets/115154b1-2e5c-4e6e-9066-5d5493f6fb76" />

### 範例的 create 功能
範例的 create 頁面<br/>
<img width="1785" height="741" alt="image" src="https://github.com/user-attachments/assets/c4a3c187-22ed-4a41-806c-0b989d49f8ab" /><br/>
<img width="559" height="742" alt="image" src="https://github.com/user-attachments/assets/5ee8437b-7513-4054-84c5-bc05444a58f0" /><br/>
他在 controller 有兩個區塊<br/>
在 index 點 create new 用的是上面的，在 create 頁面點 create 是下面的<br/>
<img width="1061" height="719" alt="image" src="https://github.com/user-attachments/assets/9a6a16b1-0a31-42e9-b50b-164191590fe3" /><br/>
下面的 HttpPost 意思是方法走 Post，要知道是走 Psot 要去網頁的 html 看<br/>
<img width="1125" height="605" alt="image" src="https://github.com/user-attachments/assets/3c5b442a-915a-403f-b0d3-baff68db186f" /><br/>
<img width="467" height="721" alt="image" src="https://github.com/user-attachments/assets/2fed8abf-ccfe-4972-9fcf-0b6c4a2c3c8d" /><br/>
Bind 裡面有太多欄位，不好維護
```cs
//create 一個 news，裡面 Bind 會收到的資料
public async Task<IActionResult> Create([Bind("NewsId,Title,Contents,DepartmentId,StartDateTime,EndDateTime,InsertDateTime,InsertEmployeeId,UpdateDateTime,UpdateEmployeeId,Click,Enable")] News news)
{
    if (ModelState.IsValid)
    {
        news.NewsId = Guid.NewGuid();
        _context.Add(news); //加資料
        await _context.SaveChangesAsync(); //存到資料庫
        return RedirectToAction(nameof(Index)); //返回 Index
    }
    return View(news);
}
```
可以用 Dto 方法替代，先建 dto 檔，一樣有刪掉一些不必要的欄位<br/>
<img width="199" height="58" alt="image" src="https://github.com/user-attachments/assets/3fc67002-5269-4c19-896d-90a0460596f9" /><br/>
```cs
public class NewsCreateDto
{
    public string Title { get; set; }
    public string Contents { get; set; }
    public int DepartmentId { get; set; }
    public DateTime StartDateTime { get; set; }
    public DateTime EndDateTime { get; set; }
}
```
然後剛剛 controller 裡 Bind 一大串就跟裡面改成這樣
```cs
public async Task<IActionResult> Create(NewsCreateDto news)
{
    if (ModelState.IsValid) //欄位值要是有效的
    {
        News insert = new News()
        {
            Title = news.Title,
            Contents = news.Contents,
            DepartmentId = news.DepartmentId,
            StartDateTime = news.StartDateTime,
            EndDateTime = news.EndDateTime,
            Click = 0, //這4個欄位直接給定
            Enable = true,
            InsertEmployeeId = 1,
            UpdateEmployeeId = 1
        };

        _context.News.Add(insert);

        await _context.SaveChangesAsync();

        return RedirectToAction(nameof(Index));
    }
    return View(news); //有欄位無效就繼續在 Create 頁面，且剛剛填的資料還保存著
}
```
! 注意 ! Views/Create.cshtml
```cshtml
@model NewsCreateDto
```
改好後下面有欄位需要刪，這邊體現 dto 的好處，他會有智慧提示錯誤在哪裡，如果是 Bind 就沒有<br/>
<img width="1175" height="600" alt="image" src="https://github.com/user-attachments/assets/73d4cebb-6fee-469c-bc9c-e4019e5d9041" /><br/>
在有空格的情況下點 Create 按鈕，發現根本沒有 request，沒有進到 controller 的下面區塊，因為前端就擋掉了，在 Views/Create.cshtml 就擋掉了<br/>
<img width="906" height="485" alt="image" src="https://github.com/user-attachments/assets/915fc0e5-109b-40c4-886b-81a37b3f7bbe" /><br/>
試著建一筆資料<br/>
! 注意 ! DepartmentId 欄位的值要有對上 Department 資料表的代號，不然抓不到資料，也就不會顯示有新增一筆資料了(雖然資料庫還是有新增資料，但網頁抓不到)<br/>
<img width="1316" height="681" alt="image" src="https://github.com/user-attachments/assets/32ed4034-5fed-4c23-9479-12b1b4afdbe7" />
