
### 基本彈性集區限制
|  |  |
| --- |:---:|
| 每個集區的最大 eDTU |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| 每個集區的儲存體上限 (GB)* |&nbsp;&nbsp;&nbsp;&nbsp;10 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;20 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;39 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;73 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;117 |
| 每個集區的資料庫數目上限 |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| 每個集區的 OLTP 記憶體內部儲存體上限 (GB) |N/A |
| 每個集區的並行背景工作數上限 |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| 每集區的並行登入數上限 |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| 每集區並行工作階段數上限 |4800 &nbsp;9600 &nbsp; 19200 &nbsp; 28800 &nbsp; 28800 |
| 每個資料庫的 eDTU 上限* |5 |
| 每個資料庫的 eDTU 下限* |0,5 |
| 每個資料庫的儲存體上限 (GB)** |2 |
| 還原時間點 |過去 7 天內任何一點 |
| 災害復原 |主動式異地複寫 |
|  | |

* 只要選取的集區 DTU 大小至少與每個資料庫的 eDTU 上限一樣大，每個資料庫的 eDTU 上限和下限就可能設定為任何所列的值

** 彈性資料庫會共用集區儲存體，所以資料庫儲存體的大小會限制為以下兩者中較小的一個：剩餘的集區儲存體或每個資料庫儲存體的上限

### 標準彈性集區限制
|  |  |
| --- |:---:|
| 每個集區的最大 eDTU |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| 每個集區的儲存體上限 (GB)* |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| 每個集區的資料庫數目上限 |&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| 每個集區的 OLTP 記憶體內部儲存體上限 (GB) |N/A |
| 每個集區的並行背景工作數上限 |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| 每集區的並行登入數上限 |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| 每集區並行工作階段數上限 |4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| 每個資料庫的 eDTU 上限* |10、20、50、100 |
| 每個資料庫的 eDTU 下限* |0、10、20、50、100 |
| 每個資料庫的儲存體上限 (GB)** |250 |
| 還原時間點 |過去 35 天內任何一點 |
| 災害復原 |主動式異地複寫 |
|  | |

* 只要選取的集區 DTU 大小至少與每個資料庫的 eDTU 上限一樣大，每個資料庫的 eDTU 上限和下限就可能設定為任何所列的值

** 彈性資料庫會共用集區儲存體，所以資料庫儲存體的大小會限制為以下兩者中較小的一個：剩餘的集區儲存體或每個資料庫儲存體的上限

### 高階彈性集區限制
|  |  |
| --- |:---:|
| 每個集區的最大 eDTU |125 &nbsp;&nbsp;&nbsp; 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 1000 &nbsp;&nbsp;&nbsp; &nbsp;1500 |
| 每個集區的儲存體上限 (GB)* |250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 750 |
| 每個集區的資料庫數目上限 |50 |
| 每個集區的 OLTP 記憶體內部儲存體上限 (GB) |N/A |
| 每個集區的並行背景工作數上限 |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| 每集區的並行登入數上限 |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| 每集區並行工作階段數上限 |4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| 每個資料庫的 eDTU 上限* |125、250、500、1000 |
| 每個資料庫的 eDTU 下限* |0、125、250、500、1000 |
| 每個資料庫的儲存體上限 (GB)** |500 |
| 還原時間點 |過去 35 天內任何一點 |
| 災害復原 |主動式異地複寫 |
|  | |

* 只要選取的集區 DTU 大小至少與每個資料庫的 eDTU 上限一樣大，每個資料庫的 eDTU 上限和下限就可能設定為任何所列的值

** 彈性資料庫會共用集區儲存體，所以資料庫儲存體的大小會限制為以下兩者中較小的一個：剩餘的集區儲存體或每個資料庫儲存體的上限

<!---HONumber=AcomDC_0914_2016-->