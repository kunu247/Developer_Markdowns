To convert the following HTML link tag:

```html
<a class="nav-link active" aria-current="page" href="~/Views/Home/Index.cshtml">Home</a>
```

into an `@Html.ActionLink` in ASP.NET MVC, you would use the `@Html.ActionLink` helper to generate a link to a specific controller action. Here's how to do it:

### Original HTML Link:
```html
<a class="nav-link active" aria-current="page" href="~/Views/Home/Index.cshtml">Home</a>
```

### Converted `@Html.ActionLink`:
```csharp
@Html.ActionLink("Home", "Index", "Home", null, new { @class = "nav-link active", aria_current = "page" })
```

### Explanation:
- `"Home"`: The link text that will be displayed.
- `"Index"`: The action method in the `HomeController` that the link points to.
- `"Home"`: The controller name (`HomeController`).
- `null`: This is for route values (if any), which you can pass if needed.
- `new { @class = "nav-link active", aria_current = "page" }`: This is the HTML attributes object where you define the CSS classes (`class="nav-link active"`) and other attributes like `aria-current="page"`.

### Final Output:
This code will generate an anchor (`<a>`) tag like the original one:

```html
<a class="nav-link active" aria-current="page" href="/Home/Index">Home</a>
```

Now this link will point to the correct action method in the `HomeController` instead of using a direct path to the `.cshtml` file.