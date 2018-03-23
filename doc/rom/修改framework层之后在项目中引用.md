## 使用framework.jar
1. 编译源码之后，在/out/target/common/obj/JAVA_LIBRARIES/framework_intermediates/,中找到classes.jar，复制到项目目录下的libs目录中，并右键add as library
2. 在module层build.gradle中，将这个lib的引用方式改为provided
3. 在project层的build.gradle中，找到allproject节点，添加

		gradle.projectsEvaluated {
            tasks.withType(JavaCompile) {
            //            options.compilerArgs.add('-Xbootclasspath/p:app\\libs\\frameworks.jar')
                        options.compilerArgs << '-Xbootclasspath/p:app/libs/frameworks.jar'
            }
   	 }