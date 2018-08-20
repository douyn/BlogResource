## ContentProvider回顾
给不同呢的应用提供内容访问。以一个或者多个表的形式将数据呈现给外部应用，行表示提供程序收集的某种数据类型的实例，行中的每列表示实例收集的每条数据


contentprovider用uri标示每条要操作的数据。

格式为:

	content://authority/path
    // authority整个提供程序的符号名称
    // path指向表的名称或路径
    

使用contentprovider的步骤：
1. 设计数据库
2. 设计contentprovider子类，复写他的方法
3. 设计authority和uri
4. 使用contentresolver查询数据库