### 项目添加jni源码之后运行报错

log as below

Android Studio:Your project contains C++ files but it is not using a supported native build system.

solution 

app/build.gradle

	android {
    	buildTypes {
        	sourceSets {
            	main {
                	jni.srcDirs = [] // add this line
                }
            }
        }
    }
