## Tip2 遇到多个构造器参数时要考虑用构造器
> 如果类的构造器或者静态工厂中有多个参数,要考虑使用Builder模式.这比传统的构造器相比更加易于阅读和编写,并且比较安全.

#### 传统的多参数类写法
##### 多参构造器
如果一个类有多个参数,就要重写构造器为多参构造器.如果参数很多就要多次重写,实例化时也比较难以阅读,并且比较难编写.

##### JavaBean模式
再这种模式写,调用无参构造器来实例化对象,并使用setter方法给对象设置属性.这种模式可读性是比较强,但是这种模式有严重的缺点,因为构造过程被分到几个不同的调用中,再构造过程中JavaBean可能处于不一致的状态.

##### Build模式
这里不直接生成想要的对象,而是利用所有的必要参数生成一个builder对象,然后对象调用方法获取和设置参数值.

	package EffectiveJava.tip2;

    public class BuildTest {
        private final int servingSize;
        private final int servings;
        private final int calories;
        private final int fat;

        private BuildTest(Builder builder){
            this.servingSize = builder.servingSize;
            this.servings = builder.servings;
            this.calories = builder.calories;
            this.fat = builder.fat;
        }

        @Override
        public String toString() {
            return servingSize + "," + servings + "," + calories + "," + fat;
        }

        public static class Builder{
            private final int servingSize;
            private final int servings;

            private int calories = 0;
            private int fat = 0;

            public Builder(int servingSize, int servings){
                this.servingSize = servingSize;
                this.servings = servings;
            }

            public Builder calories(int val){
                this.calories = val;
                return this;
            }

            public Builder fat(int val){
                this.fat = val;
                return this;
            }

            public BuildTest build(){
                return new BuildTest(this);
            }
        }
    }
    
Builder像个构造器一样可以对参数强加约束条件.

第一种方法是如果违反任何约束调教,build方法就因该抛出IllegalStateException,异常信息应该显示违反了哪个约束条件.

第二种方法使用多个setter方法对某个约束条件必须持有的参数进行检查,如果条件没有得到满足,setter方法就会抛出IllegalArgumentExection.

可以配合泛型使用.只要一个泛型,就能满足所有的builder,无论他们在构建哪种类型的对象.
	
    public interface Builder<T>{
    	public T build();
    }
    
带有Builder实例的方法通常利用有限的通配符类型来约束构建器的类型参数.

	Tree buildTree(Builder<? extends Node> nodeBuilder){...}
    
    