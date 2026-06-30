# Hướng dẫn deploy & đồng bộ team (TIKTOK x PDH)

App giờ đã có: đăng nhập bằng email (magic link) + lưu dữ liệu chung trên Supabase + đồng bộ thời gian thực cho cả team. Cài đặt giao diện (cỡ chữ, dark mode, độ rộng sidebar) vẫn lưu riêng từng máy.

Làm theo 3 bước: **(A) Tạo bảng trên Supabase → (B) Dán key vào file → (C) Deploy lên Vercel**.

---

## A. Chuẩn bị Supabase (làm 1 lần)

1. Vào https://supabase.com → mở project bạn đã có (hoặc tạo project mới).
2. Vào **SQL Editor** → New query → dán đoạn dưới → **Run**:

```sql
create table if not exists boards (
  id text primary key,
  data jsonb,
  updated_at timestamptz default now()
);

alter table boards enable row level security;

create policy "auth read"   on boards for select to authenticated using (true);
create policy "auth insert" on boards for insert to authenticated with check (true);
create policy "auth update" on boards for update to authenticated using (true) with check (true);

-- bật realtime để đồng bộ tức thì
alter publication supabase_realtime add table boards;
```

3. Lấy khóa kết nối: **Project Settings → API**, copy 2 giá trị:
   - **Project URL** (dạng `https://xxxx.supabase.co`)
   - **anon public** key (khóa công khai — an toàn để nhúng vào web tĩnh vì đã bật RLS).

4. Cho phép đăng nhập bằng email: **Authentication → Providers → Email** bật **Enable**. (Để dùng magic link không cần mật khẩu thì bật "Email OTP / Magic Link".)

5. **Authentication → URL Configuration**: thêm địa chỉ web sẽ deploy (vd `https://ten-app.vercel.app`) vào **Site URL** và **Redirect URLs**. Thêm cả `http://localhost` nếu muốn test máy.

---

## B. Dán key vào file

Mở `index.html`, tìm gần cuối đoạn:

```js
/* ====== CẤU HÌNH SUPABASE — điền 2 dòng dưới trước khi deploy cho team ====== */
var SUPA_URL="";
var SUPA_KEY="";
```

Điền Project URL và anon key vào, ví dụ:

```js
var SUPA_URL="https://xxxx.supabase.co";
var SUPA_KEY="eyJhbGciOi...(anon key)";
```

(Nếu để trống, app vẫn chạy: lần đầu mở sẽ hiện ô để dán URL/key — tiện test, nhưng để deploy cho team thì nên điền sẵn trong file.)

---

## C. Deploy lên Vercel

Cách dễ nhất, không cần cài gì:

1. Vào https://vercel.com → đăng nhập (bằng GitHub/email).
2. **Add New → Project → Deploy** — kéo–thả nguyên thư mục chứa `index.html` (chỉ cần file `index.html` là đủ), hoặc dùng nút "Deploy" rồi upload.
3. Vercel tạo link dạng `https://ten-app.vercel.app`. Đây là link gửi cho team.
4. Quay lại Supabase bước A.5, dán đúng link Vercel này vào Site URL + Redirect URLs.

> Mỗi lần sửa app: sửa `index.html` rồi deploy lại (kéo–thả lại, hoặc nối Vercel với GitHub để tự cập nhật).

---

## Cách team dùng

- Mở link → nhập email → bấm **Gửi link đăng nhập** → mở email bấm vào link → vào thẳng app.
- Mọi người thấy chung danh sách dự án, sửa tới đâu đồng bộ tới đó (hiện toast "Đồng bộ thay đổi từ team").
- Đăng xuất ở mục **Cài đặt** trong sidebar.

## Ghi chú
- Hiện mọi thành viên đăng nhập đều có quyền xem & sửa như nhau (phân quyền chi tiết làm sau).
- Lưu theo cơ chế "ai lưu sau thắng" — phù hợp nhóm nhỏ. Nếu 2 người sửa cùng 1 ô cùng lúc thì lần lưu sau ghi đè.
- Muốn nhiều workspace tách biệt (vd mỗi kênh 1 không gian): đổi `WORKSPACE_ID="default"` thành tên khác trong từng bản deploy.
