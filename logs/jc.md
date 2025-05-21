# JC

## [Day 1] - [5/11]

### ✅ 任務紀錄
<!-- - 做了哪些事（安裝、跑 code、測試參數...） -->
- 成功完成 node1 的 setup

### 🧪 設定資訊
<!-- - 使用了哪些套件 / flag / 節點 -->
- 使用 docker image 存取 BMC console
- 開機時按 F12 導入 Ubuntu 安裝檔
- 其餘部分照著 Week4 投影片做

### 🐛 問題
<!-- - 遇到什麼問題？初步分析？ -->
- 硬碟選擇的部分不確定有沒有選到 SSD

### 🗂 Version
<!-- - 版本命名 + 狀態（成功 / 失敗 / 待驗證） -->

### 📁 Log / Output
<!-- - 放連結或文字區塊 -->

### 🖼 圖片（可選）
![](image/jc/node1_setup_success.png)
![](image/jc/node1_storage.png)

---

## [Day 2] - [5/12]

### ✅ 任務紀錄
<!-- - 做了哪些事（安裝、跑 code、測試參數...） -->
- 成功完成 node2 的 setup
- 設定 InfiniBand
    - ref: [比賽資訊 HackMD](https://hackmd.io/@J-Hsu/H1IaiS8gee?stext=755%3A455%3A0%3A1747803279%3A9b6vk1)
- 設定兩台機器 ssh 互通
- 設定 NFS 掛載

### 🧪 設定資訊
<!-- - 使用了哪些套件 / flag / 節點 -->
- node2 裝機流程與 node1 相同
- node1 以 apt 安裝 `nfs-kernel-server`
- node1 修改 `/etc/exports`
    - ref: [week4 slide p.35](https://docs.google.com/presentation/d/1J_uiu7OeLwa8M2NMKNnp1Ie8-SlwvbCAPLDGmpLASYc/edit?usp=sharing)
- node2 修改 `/etc/fstab`
- 修改 `/etc/hosts` 讓 ssh 時不用打 IP address
- 加入 node1 的 public key 到 node2 的 `~/.ssh/authorized_keys` 讓 node1 能夠連線到 node2
- 加入我的 public key 到 node1 的 `~/.ssh/authorized_keys` 達成免密碼登入

### 🐛 問題
<!-- - 遇到什麼問題？初步分析？ -->
- 安裝 node2 的 OS 時發現有已被使用的硬碟
- node2 OS 安裝完成後重開機沒有進入 Ubuntu
    - 調整啟動順序 解決

### 🗂 Version
<!-- - 版本命名 + 狀態（成功 / 失敗 / 待驗證） -->

### 📁 Log / Output
<!-- - 放連結或文字區塊 -->

### 🖼 圖片（可選）
![](image/jc/node2_storage.png)