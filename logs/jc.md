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

---

## [Day 3] - [5/13]

### ✅ 任務紀錄
<!-- - 做了哪些事（安裝、跑 code、測試參數...） -->
- 安裝 Gromacs
- 嘗試執行 benchMEM

### 🧪 設定資訊
<!-- - 使用了哪些套件 / flag / 節點 -->
- Gromacs 安裝參考 week12 投影片 p.14

### 🐛 問題
<!-- - 遇到什麼問題？初步分析？ -->
- benchMEM 什麼參數都沒動，跑出的數據與助教 baseline 相差甚遠 (好很多)
- 雖然有在 `.bashrc` 中 source Gromacs 的設定 script，甚至直接 export `gmx_mpi` 的 path，但用到 `gmx_mpi` 時，node2 會找不到這個指令
    - 改用 `mpirun -np ... /opt/gromacs-2025.1-mpi/bin/gmx_mpi ...` 直接指定執行檔的絕對路徑才能成功執行

### 🗂 Version
<!-- - 版本命名 + 狀態（成功 / 失敗 / 待驗證） -->
- gromacs_benchMEM_default (stat: 待驗證)

### 📁 Log / Output
<!-- - 放連結或文字區塊 -->
![](image/jc/benchMEM_v1.png)

### 🖼 圖片（可選）

---

## [Day 4] - [5/19]

### ✅ 任務紀錄
<!-- - 做了哪些事（安裝、跑 code、測試參數...） -->
- 重新嘗試執行 benchMEM，數據和助教 baseline 相近許多
- 執行 benchRIB，數據和助教 baseline 相近
- 檢查 hyperthreading 是否開啟
- 參考網路資料嘗試優化 gromacs performance
- 嘗試以 `gmx_mpi tune_pme` 工具找出最佳的 `-npme` 參數

### 🧪 設定資訊
<!-- - 使用了哪些套件 / flag / 節點 -->

### 🐛 問題
<!-- - 遇到什麼問題？初步分析？ -->
- 不知道造成 performance 與上次執行結果不同的原因
- 用以下指令執行 `gmx_mpi tune_pme` 
    ```bash
    mpirun -np 24 --host node1:12,node2:12 \ 
    /opt/gromacs-2025.1-mpi/bin/gmx_mpi tune_pme \ 
    -mdrun /opt/gromacs-2025.1-mpi/bin/gmx_mpigmx_mpi \ 
    -s benchMEM.tpr`
    ```
    會輸出
    ```
    Fatal error:
    Cannot execute mdrun. Please check benchtest.log for problems!
    ```
    並在產生的 `benchtest.log` 中寫入
    ```
    mpirun: Error: unknown option "-np"
    ```
- 改用以下指令執行
    ```bash
    /opt/gromacs-2025.1-mpi/bin/gmx_mpi tune_pme \
    -mdrun /opt/gromacs-2025.1-mpi/bin/gmx_mpi \
    -np 24 --host node1:12,node2:12\
    -ntmpi 24 \
    -ntomp 1 \
    -s benchMEM.tpr
    ```
    結果輸出另一錯誤訊息: 
    ```
    Fatal error:
    Can't run multi-threaded MPI simulation yet!
    ```

### 🗂 Version
<!-- - 版本命名 + 狀態（成功 / 失敗 / 待驗證） -->
- gromacs_optim_v1 (stat: 失敗)

### 📁 Log / Output
<!-- - 放連結或文字區塊 -->
- benchMEM baseline
    ![](image/jc/benchMEM_v2.png)
- benchRIB baseline
    ![](image/jc/benchRIB_v1.png)

### 🖼 圖片（可選）

---

## [Day 5] - [5/20]

### ✅ 任務紀錄
<!-- - 做了哪些事（安裝、跑 code、測試參數...） -->
- mpi 突然因為不明原因無法正常執行，嘗試修復

### 🧪 設定資訊
<!-- - 使用了哪些套件 / flag / 節點 -->
- 刪除原先以安裝檔安裝的 OpenMPI (`/opt/openmpi...`)
- 重新嘗試以安裝檔手動安裝 OpenMPI，同樣錯誤仍然會出現

### 🐛 問題
<!-- - 遇到什麼問題？初步分析？ -->
- 透過手動安裝的 OpenMPI 仍會出現錯誤
- 其他組員嘗試以 spack 安裝 OpenMPI，雖然可在單一 node 執行，但因為以 `mpirun` 使用其他 node 時不會自動 load openmpi ，導致另一 node 無法執行

### 🗂 Version
<!-- - 版本命名 + 狀態（成功 / 失敗 / 待驗證） -->

### 📁 Log / Output
<!-- - 放連結或文字區塊 -->
![](image/jc/mpi_error.png)

### 🖼 圖片（可選）