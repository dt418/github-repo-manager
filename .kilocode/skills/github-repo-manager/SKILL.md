---
name: github-repo-manager
description: Use when user wants to manage GitHub repositories - list, view, search, delete, create, or update repos. Trigger on phrases like "list repos", "delete repo", "create repo", "xóa repo", "liệt kê repo", "quản lý repo", "github repo", "my repos", "repo của tôi".
---

# GitHub Repo Manager

Quản lý GitHub repositories với gh CLI.

## Prerequisites

Kiểm tra auth trước:
```bash
gh auth status
```

## Commands

### 1. List Repositories

Liệt kê tất cả repo:
```bash
gh repo list <username> --limit 100
```

Với JSON để parse dễ hơn:
```bash
gh repo list <username> --limit 100 --json name,visibility,description,createdAt,updatedAt
```

Lọc theo visibility:
```bash
gh repo list <username> --visibility public
gh repo list <username> --visibility private
```

### 2. View Repository Details

Xem chi tiết một repo:
```bash
gh repo view <owner>/<repo>
```

Xem JSON:
```bash
gh repo view <owner>/<repo> --json name,description,visibility,defaultBranch,url,createdAt,updatedAt,pushedAt
```

### 3. Search Repositories

Tìm kiếm repo:
```bash
gh search repos --owner <username> --limit 20
```

Tìm theo từ khóa:
```bash
gh search repos "<keyword>" --owner <username> --limit 20
```

### 4. Delete Repository

Xóa một repo (cần confirm):
```bash
gh repo delete <owner>/<repo> --confirm
```

**Cẩn thận**: Không thể khôi phục sau khi xóa!

### 5. Create Repository

Tạo repo mới:
```bash
gh repo create <repo-name> --public
gh repo create <repo-name> --private
gh repo create <repo-name> --public --clone
```

Tạo repo từ thư mục hiện tại:
```bash
gh repo create <repo-name> --source . --public
```

### 6. Rename Repository

Đổi tên repo:
```bash
gh repo rename <new-name>
```
Chạy trong thư mục repo hoặc chỉ định `<owner>/<repo>`.

### 7. Change Visibility

Đổi visibility (public/private):
```bash
gh repo edit <owner>/<repo> --visibility public
gh repo edit <owner>/<repo> --visibility private
```

### 8. Fork Repository

Fork một repo:
```bash
gh repo fork <owner>/<repo>
```

### 9. Archive Repository

Lưu trữ repo (không thể push):
```bash
gh repo edit <owner>/<repo> --archive
```

## Common Workflows

### Xem tất cả repo của user
```bash
gh repo list dt418 --json name,visibility,description,createdAt
```

### Xóa nhiều repo cùng lúc
```bash
# Xóa từng repo
gh repo delete dt418/repo1 --confirm
gh repo delete dt418/repo2 --confirm
```

### Tìm repo cũ không dùng
```bash
gh repo list dt418 --limit 100 --json name,createdAt,updatedAt | jq '.[] | select(.updatedAt < "2025-01-01")'
```

### Kiểm tra repo có fork không
```bash
gh repo view dt418/repo-name --json isFork
```

## Output Format

Mặc định gh CLI output ra terminal. Để parse bằng jq:
```bash
# Lấy danh sách tên repo
gh repo list dt418 --json name | jq '.[].name'

# Lọc repo private
gh repo list dt418 --json name,visibility | jq '.[] | select(.visibility == "PRIVATE")'

# Đếm số repo
gh repo list dt418 --json name | jq 'length'
```

## Tips

- Dùng `--confirm` khi xóa để không bị hỏi interactive
- Dùng `gh api` để call GitHub API trực tiếp nếu cần
- Check `gh help <command>` để xem thêm options