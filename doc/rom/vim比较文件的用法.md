## 使用vim -d比较两个文件的内容

#### 1. 使用vim的比较模式打开两个文件

	vim -d file1 file2
    # 或者
    vimdiff file1 file2
#### 2. 如果已经打开一个文件,然后需要对比另外一个文件

	:vert diffsplit file2
    
#### 3. 调整窗口位置
	
    ctrl+W L
    ctrl+W H
    ctrl+W J
    ctrl+W K
    