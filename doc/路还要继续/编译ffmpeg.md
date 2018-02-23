## FFMPEG编译流程
#### 环境
- Ubuntu-16.04
- NDK-r15c
- ffmpeg-2.6.9

#### 编写shell脚本

	#!/bin/bash
    make clean
    export NDK=/home/wuhuannan/Android/Sdk/ndk-bundle
    export SYSROOT=$NDK/platforms/android-14/arch-arm/
    export TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64
    export CPU=arm
    export PREFIX=$(pwd)/android/$CPU
    export ADDI_CFLAGS="-marm"

    ./configure --target-os=linux \
    --prefix=$PREFIX --arch=arm \
    --disable-doc \
    --enable-shared \
    --disable-static \
    --disable-yasm \
    --disable-symver \
    --enable-gpl \
    --disable-ffmpeg \
    --disable-ffplay \
    --disable-ffprobe \
    --disable-ffserver \
    --disable-doc \
    --disable-symver \
    --cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
    --enable-cross-compile \
    --sysroot=$SYSROOT \
    --extra-cflags="-Os -fpic $ADDI_CFLAGS" \
    --extra-ldflags="$ADDI_LDFLAGS" \
    $ADDITIONAL_CONFIGURE_FLAG
    make clean
    make
    make install
    
 #### 修改config文件
     #注释的部分
    #SLIBNAME_WITH_MAJOR='$(SLIBNAME).$(LIBMAJOR)'
    #LIB_INSTALL_EXTRA_CMD='$$(RANLIB) "$(LIBDIR)/$(LIBNAME)"'
    #SLIB_INSTALL_NAME='$(SLIBNAME_WITH_VERSION)'
    #SLIB_INSTALL_LINKS='$(SLIBNAME_WITH_MAJOR) $(SLIBNAME)'

    #自己写的部分
    SLIBNAME_WITH_MAJOR='$(SLIBPREF)$(FULLNAME)-$(LIBMAJOR)$(SLIBSUF)'
    LIB_INSTALL_EXTRA_CMD='$$(RANLIB)"$(LIBDIR)/$(LIBNAME)"'
    SLIB_INSTALL_NAME='$(SLIBNAME_WITH_MAJOR)'
    SLIB_INSTALL_LINKS='$(SLIBNAME)'
    
#### 给脚本设置权限，并运行脚本
