# Bao cao Bai 5: Khoi phuc trang thai va Dao nguoc commit (Reset vs Revert)

## 1. Muc tieu
- Thanh thao 2 co che reset va revert de sua sai sot.
- Phan biet khoi phuc lich su cuc bo vs dao nguoc lich su an toan khi lam viec nhom.

## 2. Boi canh va chuan bi
Repo `bai5` co 3 commit local:
```
3df6fb7 Commit 1: khoi tao du an
98c4f9c Commit 2: them tinh nang tot
b140f34 Commit 3: them code loi (ban dau e7e8775, commit lai sau demo reset)
```
File `app.txt` o Commit 3 chua dong loi:
```
BUG: chia cho 0 gay crash (code loi can sua)
```

## 3. Truong hop 1 (Sua doi cuc bo): `git reset --mixed HEAD~1`

### Lenh
```bash
git reset --mixed HEAD~1
```
- `--mixed` la che do mac dinh: dua HEAD lui 1 commit, giu thay doi o Working Directory dang **chua chuan bi (unstaged / Modified)**, xoa khoi index.
- T Tuyet doi KHONG dung `--hard` (se xoa ca code trong working dir -> mat ma nguon, vi pham rang buoc).

### Ket qua dat duoc (da lam + co file minh chung)
Truoc reset:
```
e7e8775 Commit 3: them code loi (can sua) <- HEAD
98c4f9c Commit 2: them tinh nang tot
3df6fb7 Commit 1: khoi tao du an
```

Sau `git reset --mixed HEAD~1`:
```bash
git log --oneline -n 5
# 98c4f9c Commit 2: them tinh nang tot <- HEAD (da lui)
# 3df6fb7 Commit 1: khoi tao du an

git status
# On branch main
# Changes not staged for commit:
#   modified:   app.txt
# -> Thay doi nam trong Working Directory dang Modified (Unstaged), dung yeu cau.

Get-Content app.txt
# Van giu dong BUG -> khong mat ma nguon, co the viet lai code.
```
- File minh chung: `Chung-minh-Case1-reset-log.txt` + `Chung-minh-Case1-reset-status.txt` (luu ngay sau reset).

### Khoi phuc de lam tiep Case 2
```bash
git add app.txt
git commit -m "Commit 3: them code loi (can sua) - commit lai sau reset demo"
# -> Lich su tro lai 3 commits de demo revert.
```

## 4. Truong hop 2 (Sua doi cong cong): `git revert HEAD`

### Lenh
```bash
git revert HEAD --no-edit
```
- Tao them **1 commit moi doi lap** de dao nguoc thay doi cua commit cu, khong xoa lich su cu.
- `--no-edit` de tu dong lay message mac dinh `Revert "..."` (khong mo editor).
- Bat buoc tao commit moi -> thoa rang buoc de bai.

### Ket qua dat duoc (trang thai cuoi hien tai)
```bash
git log --oneline -n 5
# b66544d Revert "Commit 3: them code loi (can sua) - commit lai sau reset demo" <- HEAD moi
# b140f34 Commit 3: them code loi (can sua) - commit lai sau reset demo
# 98c4f9c Commit 2: them tinh nang tot
# 3df6fb7 Commit 1: khoi tao du an

git status (tracked files)
# clean (app.txt da het dong BUG, tro ve code dung)

Get-Content app.txt
# Du an demo Reset vs Revert
# Phien ban: v1.0 - khoi tao
# Tinh nang tot: tinh tong 2 so (code dung)
# -> Dong BUG da bi loai bo boi commit revert.
```
- Dat yeu cau: Co commit moi tieu de bat dau bang `Revert ...` nam tren dau nhanh.
- File minh chung: `lich-su-commit.txt` (output `git log --oneline -n 5`) thay cho anh chup.

## 5. So sanh Reset vs Revert (ca nhan vs nhom)

| Tieu chi | Reset (`--mixed` / `--soft`) | Revert |
|---|---|---|
| Ban chat | Di chuyen con tro HEAD/branch ve commit cu, **viet lai lich su** (xoa commit khoi nhanh hien tai). | Giu nguyen lich su, **them commit moi** dao nguoc thay doi cu. |
| Mat mat du lieu? | `--soft`: giu staged. `--mixed`: giu unstaged (an toan neu chua push). `--hard`: MAT code (nguy hiem). | Khong mat: lich su cu + lich su dao nguoc deu luu. Co the revert tiep neu sai. |
| Khi lam viec CA NHAN (nhanh local, chua push) | NEN dung: gon lich su, sua commit loi nhanh, viet lai code thoai mai. Vi du Case 1. | Van dung duoc nhung thua 1 commit rac. |
| Khi lam viec NHOM (nhanh chung, da push / da co nguoi pull) | KHONG NEN: viet lai lich su public gay lech lich su cho dong doi (phai force-push, gay mat commit cua nguoi khac, conflict phuc tap). | BAT BUOC dung: an toan khi day len remote (`git push` thuong, khong can force), moi nguoi pull ve deu thay ro lich su dao nguoc. Vi du Case 2. |
| Kiem tra | `git log` thay HEAD lui, `git status` thay Modified. | `git log` thay them dong `Revert ...` tren dau. |

Ket luan:
- Ca nhan + chua chia se -> `reset --mixed` de sua gon.
- Nhom + da push/shared -> `revert` de sua an toan, minh bach.

## 6. File nop
- `README.md` nay (giai thich chi tiet 2 truong hop + bang so sanh).
- `lich-su-commit.txt` + `Chung-minh-Case1-*.txt` thay cho anh chup lich su commit.
- File `app.txt` hien tai (da duoc revert ve code dung).
