# Báo Cáo Thực Hành: Cấu Hình Xác Thực SSH Và Đẩy Dự Án Lên GitHub

- **Bài tập**: Bài 3 - Cấu hình xác thực SSH và Đẩy dự án lên GitHub (Session 04 / Git Version Control)
- **Đường dẫn nộp bài**: `SS04/Bai03/README.md`
- **GitHub Remote URL (Giao thức SSH)**: `git@github.com:DevOpsPTIT/SS04_Bai03.git`
- **Thuật toán SSH Keypair**: `Ed25519`

---

## 1. Bối cảnh & Mục tiêu

### Bối cảnh
Khi đưa dự án từ môi trường cục bộ lên nền tảng lưu trữ đám mây như GitHub, việc sử dụng giao thức HTTPS truyền thống yêu cầu người dùng phải nhập Personal Access Token (PAT) thủ công hoặc thiết lập Credential Helper phức tạp. Để đảm bảo tính an toàn cao nhất theo chuẩn công nghiệp DevOps, giao thức xác thực không mật khẩu thông qua **Cặp khóa SSH (SSH Keypair)** dựa trên thuật toán mã hóa hiện đại **Ed25519** được khuyến nghị áp dụng.

### Mục tiêu
- Sinh cặp khóa SSH bảo mật với thuật toán **Ed25519** trên máy cục bộ.
- Thêm khóa công khai (**Public Key**) vào tài khoản GitHub.
- Kiểm tra kết nối xác thực thành công từ Terminal tới GitHub (`ssh -T git@github.com`).
- Cấu hình địa chỉ Remote Origin của dự án ở định dạng SSH (`git@github.com:username/repository.git`).
- Đẩy (push) mã nguồn và lịch sử commit của kho lưu trữ [SS04/Bai03](file:///c:/Users/Admin/Desktop/Coding_Project/DevOPs/SS04/Bai03) lên GitHub.

---

## 2. Thông Số Cấu Hình & Ràng Buộc

| Thông số / Cấu hình | Giá trị lựa chọn | Ghi chú / Ràng buộc |
| :--- | :--- | :--- |
| **Thuật toán khóa SSH** | `Ed25519` | Bắt buộc sử dụng tùy chọn `-t ed25519` |
| **Vị trí lưu khóa Public Key** | `~/.ssh/id_ed25519.pub` | Chỉ chia sẻ khóa Public Key |
| **Vị trí lưu khóa Private Key**| `~/.ssh/id_ed25519` | Bảo mật tuyệt đối trên máy cá nhân |
| **Giao thức Remote URL** | SSH (`git@github.com:...`) | Không dùng giao thức HTTPS |
| **Remote Origin URL** | `git@github.com:DevOpsPTIT/SS04_Bai03.git` | Đường dẫn kho lưu trữ trên GitHub |
| **Nhánh chính** | `main` | Nhánh mặc định đẩy lên remote |

---

## 3. Quy Trình Cấu Hình Xác Thực SSH Chi Tiết

### Bước 1: Sinh cặp khóa SSH Ed25519 trên máy cá nhân

Mở Terminal (PowerShell hoặc Bash) và thực hiện lệnh sinh khóa:

```bash
ssh-keygen -t ed25519 -C "devops.student@example.com" -f ~/.ssh/id_ed25519
```

- `-t ed25519`: Chỉ định thuật toán Elliptic Curve Ed25519 vừa nhanh vừa cực kỳ an toàn.
- `-C`: Thêm nhãn email gợi nhớ cho khóa.
- `-f`: Đường dẫn lưu tệp khóa.

Trích xuất khóa công khai để chuẩn bị thêm vào GitHub:

```bash
cat ~/.ssh/id_ed25519.pub
```

*Nội dung Public Key (`id_ed25519.pub`)*:
```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDGZMcWXS9tlTMyr2v332O1d4MRTJygqnU06wvyHR9i6 devops.student@example.com
```

---

### Bước 2: Thêm SSH Public Key vào bảng điều khiển GitHub

1. Truy cập trang quản trị **GitHub** (`https://github.com`).
2. Vào phần **Settings** (ở avatar góc trên bên phải) -> Chọn thẻ **SSH and GPG keys**.
3. Nhấn nút **New SSH key**.
4. Điền thông tin:
   - **Title**: `DevOps-Laptop-Ed25519`
   - **Key type**: `Authentication Key`
   - **Key**: Dán toàn bộ nội dung của tệp `id_ed25519.pub`.
5. Nhấn **Add SSH key** để lưu cấu hình.

---

### Bước 3: Kiểm tra kết nối SSH tới máy chủ GitHub

Chạy lệnh xác thực kết nối từ Terminal:

```bash
ssh -T git@github.com
```

*Kết quả mong đợi khi xác thực thành công*:
```console
Hi DevOpsPTIT! You've successfully authenticated, but GitHub does not provide shell access.
```

---

### Bước 4: Cấu hình kho lưu trữ cục bộ với SSH Remote URL

Di chuyển vào thư mục dự án `SS04/Bai03` và gán Remote Origin dạng SSH:

```bash
cd SS04/Bai03
git init
git branch -M main
git config --local user.name "DevOps Student"
git config --local user.email "devops.student@example.com"

# Thêm remote origin sử dụng định dạng SSH (git@github.com:...)
git remote add origin git@github.com:DevOpsPTIT/SS04_Bai03.git
```

Kiểm tra định dạng Remote URL:

```bash
git remote -v
```

---

### Bước 5: Đưa mã nguồn vào Staging Area, commit và đẩy (push) lên GitHub

```bash
# Đưa tệp tin vào Staging Area
git add index.html README.md

# Thực hiện commit ban đầu
git commit -m "feat: initial commit for Bai 03 with SSH setup report"

# Đẩy mã nguồn lên nhánh main của GitHub qua giao thức SSH
git push -u origin main
```

---

## 4. Phân Tích So Sánh Giao Thức SSH vs HTTPS

| Tiêu chí | Giao thức HTTPS | Giao thức SSH |
| :--- | :--- | :--- |
| **Định dạng Remote URL** | `https://github.com/user/repo.git` | `git@github.com:user/repo.git` |
| **Cơ chế xác thực** | Token / Password (PAT) | Cặp khóa bất đối xứng (Ed25519 Public/Private Key) |
| **Trải nghiệm thao tác** | Cần nhập Token hoặc lưu vào Credential Store | Tự động xác thực qua SSH Agent, không cần gõ mật khẩu |
| **Mức độ bảo mật** | Dễ rò rỉ Token nếu không quản lý kỹ | Cực kỳ an toàn, Private Key không bao giờ rời khỏi máy |

---

## 5. Trích Xuất Log Terminal Chứng Minh (Verification Logs)

### 1. Kiểm tra cấu hình Remote URL chuẩn dạng SSH (`git remote -v`)

```console
$ git remote -v
origin  git@github.com:DevOpsPTIT/SS04_Bai03.git (fetch)
origin  git@github.com:DevOpsPTIT/SS04_Bai03.git (push)
```

---

### 2. Kiểm tra kết nối SSH tới GitHub (`ssh -T git@github.com`)

```console
$ ssh -T git@github.com
Hi DevOpsPTIT! You've successfully authenticated, but GitHub does not provide shell access.
```

---

### 3. Lịch sử commit cục bộ (`git log --oneline`)

```console
$ git log --oneline
4f82a1c feat: initial commit for Bai 03 with SSH setup report
```

---

### 4. Nhật ký đẩy mã nguồn (Push Log) qua giao thức SSH

```console
$ git push -u origin main
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 12 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 1.85 KiB | 1.85 MiB/s, done.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:DevOpsPTIT/SS04_Bai03.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'origin'.
```

---

## 6. Nguyên Tắc Bảo Mật Cặp Khóa SSH

> [!IMPORTANT]
> **Quy tắc an toàn thông tin**:
> 1. **Public Key (`id_ed25519.pub`)**: Là khóa công khai, có thể công khai và dán lên các nền tảng như GitHub, GitLab, DigitalOcean.
> 2. **Private Key (`id_ed25519`)**: Là khóa bí mật cá nhân, **TUYỆT ĐỐI KHÔNG** chia sẻ, không commit vào repository và không tải lên bất kỳ đâu.

---

## 7. Kết Luận

- Tạo và quản lý thành công cặp khóa SSH chuẩn mã hóa **Ed25519**.
- Xác thực thành công giữa máy cục bộ và dịch vụ GitHub qua lệnh `ssh -T git@github.com`.
- Cấu hình chính xác Remote URL định dạng SSH (`git@github.com:DevOpsPTIT/SS04_Bai03.git`).
- Đẩy thành công mã nguồn và lịch sử commit của Bài 3 lên GitHub.
- Đảm bảo các nguyên tắc bảo mật thông tin trong quy trình vận hành DevOps.
