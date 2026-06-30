# Nối GitHub ↔ Vercel để tự cập nhật (dùng VS Code)

Mục tiêu: đẩy code lên GitHub bằng VS Code → Vercel tự deploy mỗi khi bạn sửa & lưu lên GitHub.

Thư mục cần deploy: **`deploy-tiktok-pdh`** (đã có sẵn `index.html` + `README.md`).
> Nhớ dán `SUPA_URL` / `SUPA_KEY` vào `index.html` trước (xem HUONG-DAN-DEPLOY.md).

---

## Bước 1 — Mở thư mục trong VS Code
1. VS Code → **File → Open Folder** → chọn thư mục `deploy-tiktok-pdh`.
2. Nếu chưa có Git: cài Git tại https://git-scm.com/download/win rồi mở lại VS Code.
3. (Lần đầu dùng Git) mở Terminal trong VS Code (**Terminal → New Terminal**) gõ 2 dòng, thay tên/email của bạn:
   ```
   git config --global user.name "Ten Ban"
   git config --global user.email "email@cua-ban.com"
   ```

## Bước 2 — Đưa lên GitHub (không cần gõ lệnh)
1. Bấm icon **Source Control** ở thanh trái (biểu tượng nhánh cây), hoặc `Ctrl+Shift+G`.
2. Bấm **Initialize Repository**.
3. Gõ một dòng mô tả ở ô Message (vd "lần đầu") → bấm **✓ Commit** → chọn **Yes** nếu hỏi stage all.
4. Bấm **Publish Branch** → chọn **Publish to GitHub** → đăng nhập GitHub (lần đầu VS Code mở trình duyệt để cấp quyền).
5. Chọn **Private** (riêng tư) hoặc **Public** → đặt tên repo (vd `tiktok-pdh-app`) → **OK**.
   → Code đã nằm trên GitHub.

## Bước 3 — Nối Vercel với GitHub
1. Vào https://vercel.com → đăng nhập **bằng GitHub**.
2. **Add New… → Project**.
3. Vercel hiện danh sách repo GitHub → chọn repo `tiktok-pdh-app` → **Import**.
4. Framework Preset để **Other**, không cần đổi gì → **Deploy**.
5. Xong → Vercel cho link `https://tiktok-pdh-app.vercel.app`. Đây là link gửi team.
6. Quay lại **Supabase → Authentication → URL Configuration**, dán link Vercel này vào **Site URL** và **Redirect URLs**.

## Bước 4 — Từ nay mỗi lần sửa
1. Sửa `index.html` trong VS Code → **Ctrl+S** lưu.
2. Vào **Source Control** → gõ message → **✓ Commit** → bấm **Sync Changes** (hoặc nút mũi tên ↑).
3. Vercel tự nhận và deploy lại sau ~30 giây. Link giữ nguyên.

---

### Mẹo
- Muốn xem app đã deploy: vào dashboard Vercel → tab Deployments.
- Lỡ sửa hỏng: trong Vercel có thể **Rollback** về bản deploy trước.
- File `index.html` ở thư mục gốc và trong `deploy-tiktok-pdh` là 2 bản — chỉ bản trong `deploy-tiktok-pdh` được đẩy lên GitHub/Vercel. Khi sửa, nên sửa thẳng bản trong `deploy-tiktok-pdh` cho khỏi nhầm.
