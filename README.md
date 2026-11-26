# 公共服务关系库的分期划转与引用完整性

本项目的本地资料保存各方使用的原始关系、接入操作及外部观察。记录里的人员、机构和凭据均为合成。各资料目录独立使用。

## 原始关系及接入约定

机构 active_from、active_until 与 relations 等所有业务区间均左闭右开。直属关系 child 对 parent，关系链终止于 orgs 中 root=1 的机构。承办方是沿有效链从网点起第一个在该业务日拥有同类事项资格的机构。既有 permits 的 started 是受理业务时刻，expires 是可继续办理的右端点。preserve=finish 表示允许在已有凭据原期限内完成同一事项，不授权受理新事项；preserve=none 表示原资格不再有效时应暂停并明确说明。
timeline 的 changes 是同一计划内声明的关系更新，可为区间替换或新增。新建凭据必须依据受理当日关系。已发布计划可以通过 withdraw 请求更正，历史事件仍应可查询。

catalog.json 列出各数据库文件的角色、源表及运行限额。timeline.json 中的 at 是操作或观察到达的顺序，同一时刻按数组内的位置排列。args 是相应动作携带的字段，各记录里的其他位点使用该记录的业务含义。SQL 文件中只有开始联调前的行和表定义。

## 装载资料

Python 3.11.2 自带 SQLite 3.40.1。以下命令从合成资料生成独立数据库，目标文件须尚未存在。可用 sqlite3 打开生成的文件并读取源表。

~~~sh
python3 load_sample.py fixtures/two-safe-plans-form-a-cycle main-local.db --role main
~~~

执行 python3 -m unittest -v 可核对各份资料的 SQL 是否可装载、基础表行数，以及普通 SQLite 读写连接能否并行打开。
