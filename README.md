# FileZilla Server – 在校內 (同網段) 架設 FTP 服務教學

本教學示範如何在 **Windows** 系統上，使用最新版 **FileZilla Server**（v1.x 之後）架設一個僅供校園或機構內網（同網段）使用的 **FTP** 服務。

---

## 目錄

1. [前言](#前言)  
2. [下載並安裝 FileZilla Server](#步驟-1下載並安裝-filezilla-server)  
3. [啟動並檢查 Server](#步驟-2啟動並檢查-filezilla-server)  
4. [建立使用者（自訂密碼）](#步驟-3在-filezilla-server-中建立使用者自訂密碼)  
5. [設定 Mount Points（共享資料夾）](#步驟-4設定-mount-points指定共享資料夾)  
6. [開放防火牆（TCP 21）](#步驟-5設定-windows-防火牆允許-tcp-21)  
7. [本機測試 (127.0.0.1)](#步驟-6本機測試-127001)  
8. [校內其他電腦測試](#步驟-7在同網段其他電腦測試連線)  
9. [常見問題與注意事項](#常見問題與注意事項)  
10. [結論](#結論)  

---

## 前言

在許多校園或機構的內網環境下，若只需要一個簡單、快速的檔案傳輸解決方案，FTP 是個選擇。為了方便管理與使用，本文示範如何使用 **FileZilla Server** 內建的使用者帳號與密碼來控管，不使用 Windows 系統帳號，降低許多權限問題與設定複雜度。

---

## 步驟 1：下載並安裝 FileZilla Server

1. **前往官網下載**  
   - 造訪 [FileZilla 官網](https://filezilla-project.org/download.php?type=server)，下載「FileZilla Server」(非 FileZilla Client)。

2. **執行安裝**  
   - 可接受預設安裝選項，無特別需求時相對簡單。  
   - 安裝結束後，系統會安裝：
     - *FileZilla Server service* (Windows 服務)  
     - *FileZilla Server Interface* (管理介面)  

---

## 步驟 2：啟動並檢查 FileZilla Server

1. **開啟管理介面**  
   - 執行「FileZilla Server Interface」，預設連線到 `localhost` (可能是 `127.0.0.1:14148`)。

2. **檢查監聽埠**  
   - 開啟命令提示字元(CMD)，輸入：
     ```bash
     netstat -ano | findstr :21
     ```
   - 若顯示 `0.0.0.0:21 LISTENING` 或 `[::]:21 LISTENING`，代表伺服器正在等待 FTP 連線 (TCP 21)。

---

## 步驟 3：在 FileZilla Server 中建立使用者（自訂密碼）

1. **進入 Users 設定頁**  
   - 在介面上方選單：  
     ```
     Server → Configure
     ```
   - 右側「Select a page」中選擇 **Rights management / Users**。

2. **建立新使用者並指定「Require a password to log in」**  
   - 在 *Available users* 下方按 **Add**。  
   - 輸入新帳號名稱（例如 `TestUser`），按 **OK**。  
   - 右側設定區會出現使用者的標籤（`General`, `Filters`, `Limits`）。進入 `General`：  
     - 勾選 **User is enabled**。  
     - 「Authentication」下拉選單選 **Require a password to log in**。  
       > **不要**選 **Use system credentials to log in**，否則會使用 Windows 帳號且更複雜。  
     - 於下方文字框（顯示「Leave empty to keep existing password」）輸入您想要的密碼（例如 `MySecret123`）。

---

## 步驟 4：設定 Mount Points（指定共享資料夾）

1. **在「Mount points」區塊**  
   - 按 **Add**。  
   - **Virtual path**：輸入 `"/"`，表示使用者登入後看到的根目錄。  
   - **Native path**：輸入您在 Windows 上實際想共享的資料夾路徑 (如 `D:\FTP_Folder`)。

2. **設定存取權限 (Access mode)**  
   - 勾選 `Read` + `Write` (若需要上傳)；或僅 `Read` (只下載)。  
   - 視需求可勾：
     - *Apply permissions to subdirectories*  
     - *Writable directory structure*  
     - *Create native directory if it does not exist* (謹慎使用)  

3. **按「OK」或「Apply」儲存**  
   - 「Mount points」區中，應顯示一行 `"/"` → `D:\FTP_Folder`。

---

## 步驟 5：設定 Windows 防火牆（允許 TCP 21）

1. **開啟「含進階安全性的 Windows Defender 防火牆」**  
   - 在開始功能表搜尋「Windows Defender Firewall with Advanced Security」。

2. **建立「輸入規則 (Inbound Rule)」**  
   - 左側點「輸入規則」，右側按「新增規則 (New Rule)」。  
   - 選「連接埠 (Port)」→ `TCP` → 輸入埠號 `21` → 動作選「允許 (Allow)」。  
   - 依需求勾選「Domain / Private / Public」，再輸入規則名稱（如 `Allow FTP 21`）。

3. **(若需要被動模式)**  
   - 在 *Protocols settings → FTP and FTP over TLS (FTPS)* 中設定被動連線埠範圍 (如 `50000-50100`)，並在防火牆對該埠段做相同允許設定。  
   - 若只在單純內網使用，主動 (Active) 模式多數情況下即可。

---

## 步驟 6：本機測試 (127.0.0.1)

1. **在同一台安裝了 FileZilla Server 的電腦上，安裝/打開 FileZilla Client**  
   - FileZilla Client 與 Server 是不同程式，如尚未安裝，請在 [FileZilla 官網](https://filezilla-project.org/)下載「Client」版。

2. **連線測試**  
   - 在上方快速連線欄輸入：  
     - Host: `127.0.0.1`  
     - Username: 您在 Server 建立的帳號 (例如 `TestUser`)  
     - Password: 先前設定的密碼 (例如 `MySecret123`)  
     - Port: `21`  
   - 按 **Quickconnect**。

3. **查看結果**  
   - 若能成功瀏覽到 `D:\FTP_Folder` 目錄，表示伺服器端設定正常。  
   - 若失敗，請檢查是否勾錯「Use system credentials」或防火牆干擾等。

---

## 步驟 7：在同網段其他電腦測試連線

1. **查詢伺服器電腦的內網 IP**  
   - 在伺服器 `CMD` 輸入 `ipconfig`，取得如 `192.168.x.x`、`10.x.x.x` 的 IPv4 位址。

2. **在客戶端電腦 (同網段) 使用 FileZilla Client**  
   - Host: `192.168.x.x`  
   - Username: `TestUser`  
   - Password: `MySecret123`  
   - Port: `21`  
   - Quickconnect。

3. **若無法連線**  
   - 先 `ping 192.168.x.x`，若 ping 不通，代表網路層面就隔離；  
   - 若能 ping 通但 FTP 不行，多半是防火牆或安全軟體阻擋了 TCP 21。

---

## 常見問題與注意事項

1. **「501 Couldn't open directory」或無法列出目錄**  
   - 請確認在 FileZilla Server → *Mount points* 中正確設定 `"/"` → `D:\FTP_Folder` 且勾選 `Read`/`Write`。  
   - Windows 檔案系統 NTFS ACL 權限同樣需允許存取。

2. **對外開放 (外網存取)**  
   - 需要在路由器或校園/公司防火牆設定 Port Forwarding (TCP 21 + 被動埠範圍)；  
   - 並確保您擁有對外 IP 或可用的 DNS 位址；  
   - 請與網管單位協調流程。

3. **加密連線 (FTPS / SFTP)**  
   - FTP 為明文傳輸，可能有安全風險。  
   - 可考慮 *FTP over TLS (FTPS)* 或使用 SSH (TCP 22) 下的 SFTP（若伺服器端支援）。

---

## 結論

1. **安裝 FileZilla Server，啟動並檢查 TCP 21**  
2. **建立使用者帳號，設定「Require a password」，關閉 Use system credentials**  
3. **指定 Mount Points (`"/"` → `D:\FTP_Folder`) 並賦予權限**  
4. **開放防火牆 (TCP 21)**  
5. **測試：本機 (127.0.0.1) → 同網段電腦 (用內網 IP)**  

透過以上步驟，您即可在校園或機構內網環境下，快速架設一個可用 **FTP** 服務的檔案分享系統。若日後有外網需求，請考慮安全性與防火牆設定。  
祝您設定順利！
