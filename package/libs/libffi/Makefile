#
# Copyright (C) 2026 chstandard
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# libffi 已从 immortalwrt/packages feed 中移除，但 feed 里的 python3(>=3.14)、
# glib2、ruby、tvheadend、wayland、lua-ffi、micropython、node-ffi-napi、
# python-cffi、python-cryptography、python-pycares、python-pynacl 等包
# 仍然引用 libffi / libffi/host，形成悬空依赖（构建时报
# "WARNING: ... has a dependency on 'libffi', which does not exist"）。
# 其中 python3-ctypes 的 DEPENDS:=+python3-light +libffi 会直接导致
# python3 目标版编译失败（Modules/_ctypes/_ctypes.c: ffi.h: No such file）。
# 本包恢复 libffi，使上述依赖链重新闭合。
#
# 注意：libffi 3.4.3 的 src/tramp.c 调用了 closures.c 中的
# open_temp_exec_file()，但没有任何头文件声明该函数，启用静态 exec
# trampoline（默认开启）时编译必然报
# "implicit declaration of function 'open_temp_exec_file'"（GCC 14 视为错误）。
# 静态 trampoline 仅是可选的闭包优化，python3-ctypes 等使用者不需要，
# 故按历史 OpenWrt 官方包的做法加 --disable-exec-static-tramp 绕过。
#

include $(TOPDIR)/rules.mk

PKG_NAME:=libffi
PKG_VERSION:=3.4.3
PKG_RELEASE:=3

# 必须声明 PKG_INSTALL，否则 include/package.mk 第 397 行
#   Build/Install=$(if $(PKG_INSTALL),$(call Build/Install/Default,))
# 会把 Build/Install 置空——make install DESTDIR=ipkg-install 根本不执行，
# 导致 ipkg-install/usr/include 为空，Build/InstallDev 拷头文件时
# 报 "cp: cannot stat .../ipkg-install/usr/include/*.h"。
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://sourceware.org/pub/libffi
PKG_HASH:=4416dd92b6ae8fcb5b10421e711c4d3cb31203d77521a77d85d0102311e6c3b8

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:libffi:libffi

include $(INCLUDE_DIR)/package.mk

define Package/libffi
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=Foreign Function Interface library
  URL:=https://sourceware.org/libffi/
endef

define Package/libffi/description
  The libffi library provides a portable, high level programming interface
  to various calling conventions, allowing a program to call functions
  defined in another language at runtime.
endef

TARGET_CFLAGS += $(FPIC)

CONFIGURE_ARGS += \
	--disable-builddir \
	--disable-docs \
	--disable-static \
	--disable-exec-static-tramp

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/*.h $(1)/usr/include/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libffi.so* $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/libffi.pc $(1)/usr/lib/pkgconfig/
endef

define Package/libffi/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libffi.so* $(1)/usr/lib/
endef

$(eval $(call BuildPackage,libffi))
