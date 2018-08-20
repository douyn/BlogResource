## Tip3 用私有构造器或者枚举类型强化Singleton属性


#### 传统单例模式
单例模式常见有两种写法:懒汉式和恶汉式.
##### 懒汉式
懒汉式就是在需要的时候实例化.
##### 恶汉式
恶汉式就是在创建类的时候就实例化好.

#### 单元素枚举类型
	
    public enum Elvis{
    	INSTANCE;
        public void leaveTheBuilding(){}
    }
    
    不太懂