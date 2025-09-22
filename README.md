# asp.net-core-mvc
## View
### ViewData
controller
```c#
public IActionResult index()
{
  ViewData["要傳的變數"] = 要傳得值;

  return View();
}
```
view\controllerName\index
```c#
@ViewData["要傳的變數"] //Razor語法，想要在這用c#語法或變數時要加@
```
### ViewBag
controller
```c#
public IActionResult index()
{
  ViewBag.要傳的變數 = 要傳得值;

  return View();
}
```
view\controllerName\index
```c#
@ViewBag.要傳的變數 //Razor語法，想要在這用c#語法或變數時要加@
```
### 他們同個變數會共用
controller
```c#
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
結果兩個變數被視為同一個
<img width="675" height="218" alt="image" src="https://github.com/user-attachments/assets/e637c555-66a4-49a4-b7d1-dcc55a29e829" />
