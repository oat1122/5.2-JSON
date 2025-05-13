# การใช้ Express.js ในการสร้างเว็บเซิร์ฟเวอร์

เรียนรู้การใช้งาน **Express.js** เพื่อสร้างเซิร์ฟเวอร์ Node.js และจัดการกับเส้นทางต่าง ๆ (routes) ดังนี้:

- `GET /` – แสดงหน้าเริ่มต้นของเว็บไซต์
- `POST /recipe` – รับค่าจากฟอร์มและประมวลผลคำขอ

---

## 🧾 การจัดการข้อมูล JSON

- ใช้ `recipeJSON` เพื่อเก็บข้อมูลในรูปแบบ **JSON**
- ใช้ `JSON.parse()` เพื่อแปลง JSON ให้เป็น **JavaScript Object** ที่สามารถนำมาใช้งานได้ในโค้ด

---

## 📤 การส่งข้อมูลไปยัง EJS Template

- ส่งข้อมูลจากเซิร์ฟเวอร์ไปยังไฟล์ `.ejs` โดยใช้ `res.render()`
- ภายในไฟล์ EJS สามารถเข้าถึงค่าต่าง ๆ ได้ เช่น:

  ```ejs
  <%= recipe.name %>
  ```

---

## 🧾 การจัดการฟอร์มและคำขอ POST

- สร้างฟอร์ม HTML ที่ส่งคำขอแบบ `POST` ไปยังเซิร์ฟเวอร์
- ใช้ `req.body.choice` เพื่อดึงค่าที่ผู้ใช้กรอกในฟอร์มมาใช้งานในฝั่งเซิร์ฟเวอร์

---

## 🌐 การใช้ตัวแปรแบบ Global (Shared State)

- ใช้ตัวแปร `data` เพื่อเก็บข้อมูลที่เลือกจากฟอร์ม
- ตัวแปรนี้ถูกใช้ร่วมกันระหว่าง `POST /recipe` และ `GET /`
- **ข้อควรระวัง:** การใช้ตัวแปรร่วมกันเช่นนี้อาจเกิดปัญหาเมื่อมีผู้ใช้งานหลายคนในเวลาเดียวกัน

---

## 🔀 การจัดการเงื่อนไขใน EJS

- ใช้โครงสร้างเงื่อนไขใน EJS เพื่อควบคุมการแสดงผล เช่น:

  ```ejs
  <% if (recipe) { %>
    <!-- แสดงข้อมูล recipe -->
  <% } %>
  ```

---

## 🛠️ การแก้ไขข้อผิดพลาด

- เรียนรู้การแก้ปัญหา เช่น `ReferenceError: data is not defined`
- แนวทางคือการ **ตรวจสอบการประกาศตัวแปรให้ถูกต้อง** และไม่ลืมใช้ `let` หรือ `const` ในการประกาศ

---

> ✅ **สรุป:** ได้เรียนรู้การพัฒนาเว็บแอปด้วย Express.js โดยผสานการใช้ JSON, EJS, และฟอร์ม HTML

---

# 🧠 คำอธิบายโค้ดแอปพลิเคชัน Taco Recipes

เว็บแอปนี้ช่วยให้ผู้ใช้เลือกประเภทของทาโก้ (ไก่, เนื้อ, ปลา) ผ่านปุ่มบนหน้าเว็บ จากนั้นแสดงสูตรอาหารที่เกี่ยวข้อง โดยมีส่วนประกอบหลักดังนี้:

---

## 📁 โครงสร้างหลักของแอป

- **ฝั่งเซิร์ฟเวอร์ (server.js / app.js)**: ใช้ Node.js + Express.js เพื่อจัดการคำขอและข้อมูล
- **ฝั่งไคลเอนต์ (solution.ejs)**: ใช้ HTML + EJS Template เพื่อแสดงหน้าเว็บและข้อมูลไดนามิก

---

## 🚀 โค้ดฝั่งเซิร์ฟเวอร์ (Express.js)

```javascript
import express from "express";
import bodyParser from "body-parser";

const app = express();
const port = 3000;

const recipeJSON =
  '[{"id": "0001",...}, {"id": "0002",...}, {"id": "0003",...}]';

app.use(express.static("public"));
app.use(bodyParser.urlencoded({ extended: true }));

let data;

app.get("/", (req, res) => {
  res.render("solution.ejs", { recipe: data });
});

app.post("/recipe", (req, res) => {
  switch (req.body.choice) {
    case "chicken":
      data = JSON.parse(recipeJSON)[0];
      break;
    case "beef":
      data = JSON.parse(recipeJSON)[1];
      break;
    case "fish":
      data = JSON.parse(recipeJSON)[2];
      break;
    default:
      break;
  }
  res.redirect("/");
});

app.listen(port, () => {
  console.log(`Server running on port: ${port}`);
});
```

---

## 🌐 โค้ดฝั่งไคลเอนต์ (solution.ejs)

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Taco Recipes</title>
    <link rel="stylesheet" href="/styles/main.css" />
  </head>
  <body>
    <h1>Taco Town 🌮</h1>
    <form action="/recipe" method="POST" class="buttons">
      <button type="submit" value="chicken" name="choice">🍗</button>
      <button type="submit" value="beef" name="choice">🥩</button>
      <button type="submit" value="fish" name="choice">🐟</button>
    </form>
    <div id="recipeContainer" class="hidden">
      <% if (locals.recipe) { %>
      <h2><%= recipe.name %></h2>
      <h3>Ingredients:</h3>
      <ul>
        <li>
          <%= recipe.ingredients.protein.name %>, <%=
          recipe.ingredients.protein.preparation %>
        </li>
        <li><%= recipe.ingredients.salsa.name %></li>
        <% recipe.ingredients.toppings.forEach(topping => { %>
        <li><%= topping.quantity %> of <%= topping.name %></li>
        <% }) %>
      </ul>
      <% } else { %>
      <h2>Pick your favourite taco ingredient 👆</h2>
      <% } %>
    </div>
  </body>
</html>
```

---

## 📌 คำอธิบายเพิ่มเติม

- `<form>`: ส่งข้อมูล `choice` แบบ POST ไปที่ `/recipe`
- ปุ่ม `<button>` ส่งค่า `"chicken"`, `"beef"`, หรือ `"fish"`
- `recipeJSON`: เก็บสูตรอาหารทั้งหมดในรูปแบบ JSON string
- `JSON.parse(...)`: แปลง JSON string เป็นอ็อบเจกต์
- `res.render(...)`: ส่งตัวแปร `recipe` ไปยังเทมเพลต
- `<% %>`: คือไวยากรณ์ของ EJS สำหรับฝังโค้ด JavaScript ลงใน HTML

---

## 🔄 การทำงานของแอปโดยสรุป

1. ผู้ใช้เข้าชมหน้าแรก > เห็นปุ่มเลือกทาโก้
2. ผู้ใช้เลือกปุ่มใดปุ่มหนึ่ง > ส่ง POST ไปยัง `/recipe`
3. เซิร์ฟเวอร์แยกข้อมูลจาก JSON และเก็บไว้ใน `data`
4. เปลี่ยนเส้นทางกลับ `/` แล้วแสดงสูตรอาหารผ่าน EJS
