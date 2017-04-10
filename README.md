
[toc]

---

#<center>**AS 2.2+ 导入 HelloARNative 工程**</center>

---

#***TODO***

- [] 

---

#**Tips**

* Android 6.0+ 需要动态获取权限，这里只是官方例程导入，不含该部分（动态获取权限可以参考 [HelloARVideoNative](https://github.com/weichao66666/HelloARVideoNative "https://github.com/weichao66666/HelloARVideoNative")）。也可以通过手机的设置进行主动授权。

* 从 GitHub 下载代码后文件夹名称 HelloARNative-master 需要改为 HelloARNative。

---

#**准备工作**

##**下载并导入 HelloARNative 工程**

>官方下载地址：http://www.easyar.cn/view/download.html

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327114752.jpg)

第一个包含 jar、so、hpp。

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327114958.jpg)

第二个包含 HelloARNative 工程。

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327115244.jpg)

##**跳过更新版本**

导入时会提示更新版本，跳过即可。

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327112800.jpg)

##**配置 NDK**

>File -> Project Structure...

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327112849.jpg)

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327113216.jpg)

选择 NDK 目录，完成配置。

---

#**导入/创建文件**

##**目前的工程目录结构**

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327113621.jpg)

##**导入 EasyAR.jar**

>文件路径：EasyARSDK\package\Android\libs\EasyAR.jar

在 app 目录下新建 libs 文件夹，导入 EasyAR.jar。

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327120325.jpg)

##**导入 hpp、cc 文件**

>部分 hpp 文件所在文件夹的路径：EasyARSDK\package

* 在 main 目录下新建 cpp 文件夹，导入 include 文件夹。

* 剪切 jni 文件夹内的部分文件到 cpp 文件夹中。

* 删除 jni 目录。

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327120223.jpg)

##**导入 so 文件**

>文件路径：EasyARSDK\package\Android\libs\armeabi-v7a\libEasyAR.so

在 main 目录下新建 jniLibs 文件夹，导入 libEasyAR.so。

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327124312.jpg)

##**创建 CMakeLists.txt**

在 app 目录下新建 CMakeLists.txt。

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327124608.jpg)

##**导入/创建后的工程目录结构**

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/QQ截图20170327125058.jpg)

---

#**修改文件**

##**修改模块的 build.gradle**

>文件路径：HelloARNative\app\build.gradle

    apply plugin: 'com.android.application'
    
    android {
        compileSdkVersion 25
        buildToolsVersion "25.0.2"
    
        defaultConfig {
            applicationId "cn.easyar.samples.helloar"
            minSdkVersion 18
            targetSdkVersion 25
            versionCode 1
            versionName "1.0"
    
            externalNativeBuild {
                cmake {
                    cppFlags "-std=c++11 -frtti -fexceptions"
    //                abiFilters 'x86', 'x86_64', 'armeabi', 'armeabi-v7a', 'arm64-v8a', 'mips', 'mips64'
                    abiFilters 'armeabi-v7a'
                }
            }
    
            multiDexEnabled true
        }
    
        buildTypes {
            release {
                minifyEnabled false
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
        }
    
        externalNativeBuild {
            cmake {
                path "CMakeLists.txt"
            }
        }
    
        sourceSets {
            main {
                jniLibs.srcDirs = ['src/main/jniLibs']
            }
        }
    }
    
    dependencies {
        compile fileTree(include: ['*.jar'], dir: 'libs')
        compile 'com.android.support:appcompat-v7:25.3.0'
        compile 'com.android.support:multidex:1.0.1'
    }

##**修改 CMakeLists.txt**

>文件路径：HelloARNative\app\CMakeLists.txt

**工程路径`D:/android_studio_workspace/HelloARNative`需要根据自己的路径修改。**

    # CMake版本信息
    cmake_minimum_required(VERSION 3.4.1)
    
    # 支持-std=gnu++11
    set(CMAKE_VERBOSE_MAKEFILE on)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")
    
    # 工程路径
    set(pathToProject D:/android_studio_workspace/HelloARNative)
    
    # 配置加载native依赖
    include_directories(${pathToProject}/app/src/main/cpp/include)
    
    # 添加待编译的文件
    add_library(helloar SHARED ${pathToProject}/app/src/main/cpp/ar.hpp
                                ${pathToProject}/app/src/main/cpp/renderer.hpp
                                ${pathToProject}/app/src/main/cpp/ar.cc
                                ${pathToProject}/app/src/main/cpp/renderer.cc
                                ${pathToProject}/app/src/main/cpp/helloar.cc)
    
    # 动态方式加载
    add_library(lib_easyar SHARED IMPORTED)
    # 引入so文件
    set_target_properties(lib_easyar PROPERTIES IMPORTED_LOCATION ${pathToProject}/app/src/main/jniLibs/${ANDROID_ABI}/libEasyAR.so)
    
    target_link_libraries(helloar
                            lib_easyar
                            GLESv2
                            log)

##**修改 gradle-wrapper.properties**

>文件路径：HelloARNative\gradle\wrapper\gradle-wrapper.properties

    #Wed Oct 21 11:34:03 PDT 2015
    distributionBase=GRADLE_USER_HOME
    distributionPath=wrapper/dists
    zipStoreBase=GRADLE_USER_HOME
    zipStorePath=wrapper/dists
    distributionUrl=https\://services.gradle.org/distributions/gradle-3.4.1-all.zip

##**修改工程的 build.gradle**

>文件路径：HelloARNative\build.gradle

    // Top-level build file where you can add configuration options common to all sub-projects/modules.
    
    buildscript {
        repositories {
            jcenter()
        }
        dependencies {
            classpath 'com.android.tools.build:gradle:2.3.0'
    
            // NOTE: Do not place your application dependencies here; they belong
            // in the individual module build.gradle files
        }
    }
    
    allprojects {
        repositories {
            jcenter()
        }
    }
    
    task clean(type: Delete) {
        delete rootProject.buildDir
    }

##**修改 MainActivity.java**

>文件路径：HelloARNative\app\src\main\java\cn\easyar\samples\helloar\MainActivity.java

* 按照官方说明申请 key，修改 key。

* 修改导入 lib。

* 修改继承 Activity。

修改后文件内容：

    /**
    * Copyright (c) 2015-2016 VisionStar Information Technology (Shanghai) Co., Ltd. All Rights Reserved.
    * EasyAR is the registered trademark or trademark of VisionStar Information Technology (Shanghai) Co., Ltd in China
    * and other countries for the augmented reality technology developed by VisionStar Information Technology (Shanghai) Co., Ltd.
    */
    
    package cn.easyar.samples.helloar;
    
    import android.app.Activity;
    import android.content.res.Configuration;
    import android.os.Bundle;
    import android.view.Menu;
    import android.view.MenuItem;
    import android.view.ViewGroup;
    import android.view.WindowManager;
    
    import cn.easyar.engine.EasyAR;
    
    
    public class MainActivity extends Activity {
    
        /*
        * Steps to create the key for this sample:
        *  1. login www.easyar.com
        *  2. create app with
        *      Name: HelloAR
        *      Package Name: cn.easyar.samples.helloar
        *  3. find the created item in the list and show key
        *  4. set key string bellow
        */
        static String key = "E7RZi1nWIjB0LS1FGpRsPOn17suIdXo45rURPkAWk6iNONcNXGt48KzpOvC8oXnrmGNcwI6TzIS8Qp3X7hArcoiQEvYDGjUni6imf7ecd9908bcde0b43e3df598223a2e42A4oJCzpmymvzIeBtj1kEpQC0zec8gwF2luah3S5qXiFIxM9pFTCvgqAz7ywywxKVWZiW";
    
        static {
            System.loadLibrary("helloar");
        }
    
        public static native void nativeInitGL();
        public static native void nativeResizeGL(int w, int h);
        public static native void nativeRender();
        private native boolean nativeInit();
        private native void nativeDestory();
        private native void nativeRotationChange(boolean portrait);
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            getWindow().setFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON, WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
    
            EasyAR.initialize(this, key);
            nativeInit();
    
            GLView glView = new GLView(this);
            glView.setRenderer(new Renderer());
            glView.setZOrderMediaOverlay(true);
    
            ((ViewGroup) findViewById(R.id.preview)).addView(glView, new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
            nativeRotationChange(getWindowManager().getDefaultDisplay().getRotation() == android.view.Surface.ROTATION_0);
        }
    
        @Override
        public void onConfigurationChanged(Configuration config) {
            super.onConfigurationChanged(config);
            nativeRotationChange(getWindowManager().getDefaultDisplay().getRotation() == android.view.Surface.ROTATION_0);
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            nativeDestory();
        }
        @Override
        protected void onResume() {
            super.onResume();
            EasyAR.onResume();
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            EasyAR.onPause();
        }
    
        @Override
        public boolean onCreateOptionsMenu(Menu menu) {
            getMenuInflater().inflate(R.menu.menu_main, menu);
            return true;
        }
    
        @Override
        public boolean onOptionsItemSelected(MenuItem item) {
            int id = item.getItemId();
    
            if (id == R.id.action_settings) {
                return true;
            }
    
            return super.onOptionsItemSelected(item);
        }
    }

---

#**测试**

编译后成功运行。

![](https://github.com/weichao66666/HelloARNative/raw/master/README.md-images/2017-03-27-13-38-50.png)
