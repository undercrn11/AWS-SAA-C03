#### 有关html，css的基础知识复习：



## **HTML Basics**

HTML (HyperText Markup Language) is used to structure content on the web.

### **1. Document Structure**

```
htmlCopyEdit<!DOCTYPE html>
<html>
  <head>
    <title>Page Title</title>
  </head>
  <body>
    <!-- Page content goes here -->
  </body>
</html>
```

### **2. Common Tags**

- **Headings**: `<h1>` to `<h6>`

- **Paragraph**: `<p>`

- **Links**: `<a href="url">Text</a>`

- **Images**: `<img src="image.jpg" alt="description">`

- **Lists**:

  - Ordered: `<ol><li>Item</li></ol>`
  - Unordered: `<ul><li>Item</li></ul>`

- **Forms**:

  ```
  htmlCopyEdit<form method="post">
    <input type="text" name="username">
    <input type="submit" value="Submit">
  </form>
  ```

### **3. Layout Tags**

- `<div>`: Block container
- `<span>`: Inline container
- `<header>`, `<footer>`, `<section>`, `<nav>`, `<main>`: Semantic elements

------

## 🔹 **CSS Basics**

CSS (Cascading Style Sheets) styles the HTML content.

### **1. Syntax**

```
cssCopyEditselector {
  property: value;
}
```

### **2. Selectors**

- Element: `p { }`
- Class: `.box { }`
- ID: `#main { }`
- Nested: `div p { }`

### **3. Properties**

- **Text**: `color`, `font-size`, `text-align`
- **Box model**: `margin`, `padding`, `border`, `width`, `height`
- **Layout**:
  - `display: flex;`, `grid`, `inline-block`
  - `position: relative/absolute/fixed`
- **Backgrounds**: `background-color`, `background-image`
- **Others**: `opacity`, `z-index`, `box-shadow`, `border-radius`

### **4. Ways to Apply CSS**

- Inline: `<div style="color:red;">`
- Internal: `<style>div { color:red; }</style>`
- External: `<link rel="stylesheet" href="style.css">`

------

## 🔹 **Razor Page Notes**

In Razor (`.cshtml`) files, HTML is written directly, and C# logic is embedded using `@`.

```
htmlCopyEdit@model YourApp.Models.ExampleModel

<form method="post">
    <input type="text" name="Name" value="@Model.Name">
    <button type="submit">Save</button>
</form>
```





### **4. Form Controls**

Useful when binding to Razor models:

```
htmlCopyEdit<form method="post">
  <label for="username">Username:</label>
  <input type="text" id="username" name="Username" />

  <label for="password">Password:</label>
  <input type="password" id="password" name="Password" />

  <label for="remember">Remember Me:</label>
  <input type="checkbox" id="remember" name="RememberMe" />

  <select name="Role">
    <option value="admin">Admin</option>
    <option value="user">User</option>
  </select>

  <button type="submit">Login</button>
</form>
```

### **5. Tables**

For structured data display:

```
htmlCopyEdit<table>
  <thead>
    <tr><th>ID</th><th>Name</th></tr>
  </thead>
  <tbody>
    <tr><td>1</td><td>Alice</td></tr>
    <tr><td>2</td><td>Bob</td></tr>
  </tbody>
</table>
```

------

## 🔸 **More CSS Techniques**

### **5. Flexbox**

For flexible layouts:

```
cssCopyEdit.container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
htmlCopyEdit<div class="container">
  <div>Left</div>
  <div>Right</div>
</div>
```

### **6. Grid**

For complex 2D layouts:

```
cssCopyEdit.grid {
  display: grid;
  grid-template-columns: 1fr 2fr;
  gap: 10px;
}
htmlCopyEdit<div class="grid">
  <div>Sidebar</div>
  <div>Main Content</div>
</div>
```

### **7. Responsive Design**

```
cssCopyEdit@media (max-width: 768px) {
  .container {
    flex-direction: column;
  }
}
```

------

## 🔸 **Razor-Specific Concepts**

### **1. HTML Helpers**

```
csharpCopyEdit@Html.TextBoxFor(m => m.Username)
@Html.PasswordFor(m => m.Password)
@Html.ValidationMessageFor(m => m.Password)
```

### **2. Tag Helpers (Modern Razor Syntax)**

```
htmlCopyEdit<form method="post">
  <input asp-for="Username" class="form-control" />
  <span asp-validation-for="Username" class="text-danger"></span>
</form>
```

> **Tip**: Tag helpers are better for HTML/CSS structure readability than traditional `@Html` helpers.

### **3. Layouts and Partials**

```
htmlCopyEdit<!-- _Layout.cshtml -->
<body>
  <header> ... </header>
  <main>
    @RenderBody()
  </main>
</body>
htmlCopyEdit<!-- Page.cshtml -->
@{
  Layout = "_Layout";
}
```

------

## 🔸 **Visual Example: Login Page**

```
htmlCopyEdit<form method="post" class="login-form">
  <label asp-for="Username"></label>
  <input asp-for="Username" />

  <label asp-for="Password"></label>
  <input asp-for="Password" type="password" />

  <button type="submit">Login</button>
</form>

<style>
.login-form {
  max-width: 300px;
  margin: auto;
  display: flex;
  flex-direction: column;
}
.login-form input {
  margin-bottom: 10px;
  padding: 8px;
}
</style>
```
