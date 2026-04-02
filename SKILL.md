---
name: github-repo-manager
description: Quản lý GitHub repositories bằng gh CLI - liệt kê, xem chi tiết, tìm kiếm, xóa, tạo, đổi tên, đổi visibility, fork, archive repos. Sử dụng khi user nói "list repos", "delete repo", "create repo", "xóa repo", "liệt kê repo", "quản lý repo github", "my repos", "repo của tôi".
version: 1.2.0
author: Thanh Danh
license: MIT
source: https://github.com/dt418/github-repo-manager
tags:
  - github
  - cli
  - repo-management
platforms:
  - opencode
  - claude
  - cursor
  - codex
  - windsurf
  - copilot
tools:
  - Bash
  - Read
---

# GitHub Repo Manager

Skill này giúp quản lý GitHub repositories thông qua GitHub CLI (gh). Bao gồm các thao tác cơ bản như xem danh sách, tạo mới, xóa, tìm kiếm, và cập nhật repo properties.

## When to Use

- Khi user muốn xem danh sách repositories của họ hoặc người khác
- Khi cần tạo repository mới trên GitHub
- Khi cần xóa một repository (cẩn thận - không thể khôi phục)
- Khi cần tìm kiếm repositories theo từ khóa hoặc owner
- Khi cần thay đổi visibility (public/private) của repo
- Khi cần fork một repo về tài khoản của mình
- Khi cần đổi tên hoặc archive repository

## Prerequisites

Kiểm tra authentication trước khi sử dụng:
```bash
gh auth status
```

Nếu chưa login, chạy:
```bash
gh auth login
```

## Commands

Dưới đây là các command cơ bản và nâng cao để quản lý GitHub repositories.

### 1. List Repositories - Liệt kê Repositories

Liệt kê tất cả repositories của một user:
```bash
gh repo list <username> --limit 100
```

Liệt kê với JSON để dễ parse:
```bash
gh repo list <username> --limit 100 --json name,visibility,description,createdAt,updatedAt
```

Lọc theo visibility:
```bash
gh repo list <username> --visibility public
gh repo list <username> --visibility private
```

### 2. View Repository Details - Xem Chi Tiết Repo

Xem thông tin chi tiết của một repo:
```bash
gh repo view <owner>/<repo>
```

Xem chi tiết dạng JSON:
```bash
gh repo view <owner>/<repo> --json name,description,visibility,defaultBranch,url,createdAt,updatedAt,pushedAt
```

### 3. Search Repositories - Tìm Kiếm Repositories

Tìm kiếm repo theo owner:
```bash
gh search repos --owner <username> --limit 20
```

Tìm theo từ khóa:
```bash
gh search repos "<keyword>" --owner <username> --limit 20
```

### 4. Delete Repository - Xóa Repository

**CẨN THẬN**: Xóa vĩnh viễn, không thể khôi phục!
```bash
gh repo delete <owner>/<repo> --confirm
```

### 5. Create Repository - Tạo Repository Mới

Tạo repo mới (empty):
```bash
gh repo create <repo-name> --public
gh repo create <repo-name> --private
gh repo create <repo-name> --public --clone
```

Tạo repo từ thư mục hiện tại (đẩy code lên GitHub):
```bash
gh repo create <repo-name> --source . --public
```

### 6. Rename Repository - Đổi Tên Repo

Đổi tên repository (chạy trong thư mục repo hoặc chỉ định owner/repo):
```bash
gh repo rename <new-name>
```

### 7. Change Visibility - Đổi Visibility

Đổi giữa public và private:
```bash
gh repo edit <owner>/<repo> --visibility public
gh repo edit <owner>/<repo> --visibility private
```

### 8. Fork Repository - Fork Repo

Fork một repo về tài khoản của mình:
```bash
gh repo fork <owner>/<repo>
```

### 9. Archive Repository - Lưu Trữ Repo

Archive repository (không thể push nhưng vẫn có thể clone):
```bash
gh repo edit <owner>/<repo> --archive
```

## Common Workflows - Các Workflow Thường Dùng

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

### Tìm repo cũ không dùng (trên 1 năm)
```bash
gh repo list dt418 --limit 100 --json name,createdAt,updatedAt | jq '.[] | select(.updatedAt < "2025-01-01")'
```

### Kiểm tra repo có phải là fork không
```bash
gh repo view dt418/repo-name --json isFork
```

## Output Format - Định Dạng Output

Mặc định gh CLI output ra terminal. Sử dụng jq để parse:

```bash
# Lấy danh sách tên repo
gh repo list dt418 --json name | jq '.[].name'

# Lọc repo private
gh repo list dt418 --json name,visibility | jq '.[] | select(.visibility == "PRIVATE")'

# Đếm số repo
gh repo list dt418 --json name | jq 'length'

# Lấy description của repo
gh repo list dt418 --json name,description | jq '.[] | select(.description != null)'
```

## Tips - Mẹo

- Luôn dùng `--confirm` khi xóa để tránh prompt interactive
- Sử dụng `gh api` để call GitHub API trực tiếp khi cần thao tác nâng cao
- Chạy `gh help <command>` để xem thêm các options
- Kiểm tra `gh auth status` trước khi thực hiện thao tác
- Với repo không thuộc về mình, cần có quyền admin để thay đổi