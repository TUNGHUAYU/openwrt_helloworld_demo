# OpenWrt HelloWorld Demo
>  Please see https://openwrt.org/docs/guide-developer/helloworld/start



## Work Flow




### Step1. Install OpenWrt framework

```bash
# Clone OpenWrt repository to local folder called source 
$ git clone https://git.openwrt.org/openwrt/openwrt.git source

# Select the stable version of OpenWrt
$ cd source 
$ git checkout v17.01.2
$ make distclean

# Update feeds and Install feeds
$ ./scripts/feeds update -a
$ ./scripts/feeds install -a

# Adjust .config that you want
$ make menuconfig

# Build toolchain
$ make toolchain/install
```



### Step2. Create Package feed

```bash
# create nested folder for package feed
# create repository "mypackages"
# create category "examples"
# create entry "helloworld"
$ mkdir -p mypackages/examples/helloworld

# create OpenWrt package manifest
$ touch mypackages/examples/helloworld/Makefile

```



mypackages/examples/helloworld/Makefile

```bash
include $(TOPDIR)/rules.mk

# Name, version and release number
# The name and version of your package are used to define the variable to point to the build directory of your package: $(PKG_BUILD_DIR)
PKG_NAME:=helloworld
PKG_VERSION:=1.0
PKG_RELEASE:=1

# Source settings (i.e. where to find the source codes)
# This is a custom variable, used below
SOURCE_DIR:=/home/buildbot/helloworld

include $(INCLUDE_DIR)/package.mk

# Package definition; instructs on how and where our package will appear in the overall configuration menu ('make menuconfig')
define Package/helloworld
  SECTION:=examples
  CATEGORY:=Examples
  TITLE:=Hello, World!
endef

# Package description; a more verbose description on what our package does
define Package/helloworld/description
  A simple "Hello, world!" -application.
endef

# Package preparation instructions; create the build directory and copy the source code. 
# The last command is necessary to ensure our preparation instructions remain compatible with the patching system.
define Build/Prepare
        mkdir -p $(PKG_BUILD_DIR)
        cp $(SOURCE_DIR)/* $(PKG_BUILD_DIR)
        $(Build/Patch)
endef

# Package build instructions; invoke the target-specific compiler to first compile the source file, and then to link the file into the final executable
define Build/Compile
        $(TARGET_CC) $(TARGET_CFLAGS) -o $(PKG_BUILD_DIR)/helloworld.o -c $(PKG_BUILD_DIR)/helloworld.c
        $(TARGET_CC) $(TARGET_LDFLAGS) -o $(PKG_BUILD_DIR)/$1 $(PKG_BUILD_DIR)/helloworld.o
endef

# Package install instructions; create a directory inside the package to hold our executable, and then copy the executable we built previously into the folder
define Package/helloworld/install
        $(INSTALL_DIR) $(1)/usr/bin
        $(INSTALL_BIN) $(PKG_BUILD_DIR)/helloworld $(1)/usr/bin
endef

# This command is always the last, it uses the definitions and variables we give above in order to get the job done
$(eval $(call BuildPackage,helloworld))
```



### Step3. Create simple application

```bash
$ mkdir helloworld
$ touch helloworld/hellworld.c
```



helloworld/helloworld.c

```c
#include <stdio.h>
     
int main(void)
{
    printf("\nHello, world!\n\n");
    return 0;
}
```



### Step4. Setup feeds.conf

``` bash
$ touch source/feeds.conf
```



feeds.conf

```tex
src-link mypackages /home/buildbot/mypackages
```

> please change the path with your own



### Step 5. Update feeds and Install to package

```bash
$ cd source/
./script/feeds update mypackages
./script/install -a -p mypackages
```



### Step 6. Building the package

```bash
$ make package/feeds/mypackages/example/helloworld/{clean,compile} V=s
```





## Appendix



```tex
.
├── helloworld
│   └── helloworld.c
├── mypackages
│   └── examples
│       └── helloworld
│           └── Makefile
└── readme.md

4 directories, 3 files
```





