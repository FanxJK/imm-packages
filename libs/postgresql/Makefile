# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=postgresql
PKG_VERSION:=16.3
PKG_RELEASE:=1
PKG_MAINTAINER:=Daniel Golle <daniel@makrotopia.org>
PKG_LICENSE:=PostgreSQL
PKG_CPE_ID:=cpe:/a:postgresql:postgresql

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=\
	https://ftp.postgresql.org/pub/source/v$(PKG_VERSION) \
	http://ftp.postgresql.org/pub/source/v$(PKG_VERSION) \
	ftp://ftp.postgresql.org/pub/source/v$(PKG_VERSION)

PKG_HASH:=331963d5d3dc4caf4216a049fa40b66d6bcb8c730615859411b9518764e60585

PKG_BUILD_FLAGS:=no-mips16
PKG_BUILD_DEPENDS:=perl/host postgresql/host
PKG_INSTALL:=1

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

define Package/libpq
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+BUILD_NLS:icu +libpthread
  TITLE:=PostgreSQL client library
  URL:=http://www.postgresql.org/
  SUBMENU:=Database
endef

define Package/libpq/description
PostgreSQL client library.
endef

define Package/pgsql-cli
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libncursesw +libpq +libreadline +librt +zlib
  TITLE:=Command Line Interface (CLI) to PostgreSQL databases
  URL:=http://www.postgresql.org/
  SUBMENU:=Database
endef

define Package/pgsql-cli/description
Command Line Interface (CLI) to PostgreSQL databases.
endef

define Package/pgsql-cli-extra
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libncursesw +libpq +libreadline +librt +zlib
  TITLE:=Command Line extras for PostgreSQL databases
  URL:=http://www.postgresql.org/
  SUBMENU:=Database
endef

define Package/pgsql-cli-extra/description
Command Line extras for PostgreSQL databases.
endef

define Package/pgsql-server
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+pgsql-cli +blockd
  TITLE:=PostgreSQL databases Server
  URL:=http://www.postgresql.org/
  SUBMENU:=Database
  USERID:=postgres=5432:postgres=5432
endef

define Package/pgsql-server/description
PostgreSQL databases Server.
endef

PGSQL_SERVER_BIN := \
	pg_amcheck \
	pg_archivecleanup \
	pg_basebackup \
	pg_checksums \
	pg_controldata \
	pg_ctl \
	pg_dump \
	pg_dumpall \
	pg_isready \
	pg_recvlogical \
	pg_receivewal \
	pg_resetwal \
	pg_restore \
	pg_rewind \
	pg_upgrade \
	pg_verifybackup \
	pg_waldump \
	postgres \
	initdb

PGSQL_CLI_EXTRA_BIN := \
	clusterdb	\
	createdb	\
	createuser	\
	dropdb		\
	dropuser	\
	oid2name	\
	pgbench		\
	reindexdb	\
	vacuumdb	\
	vacuumlo

FAKE_CONFIGURE_ARGS := \
			--disable-nls \
			--disable-rpath \
			--without-bonjour \
			--without-gssapi \
			--without-icu \
			--without-ldap \
			--without-openssl \
			--without-pam \
			--without-perl \
			--without-python \
			--without-readline \
			--without-tcl \
			--without-systemd \
			--with-zlib="yes" \
			--enable-depend

HOST_CONFIGURE_ARGS += \
			--disable-nls \
			--disable-rpath \
			--without-bonjour \
			--without-gssapi \
			--without-icu \
			--without-ldap \
			--without-openssl \
			--without-pam \
			--without-perl \
			--without-python \
			--without-readline \
			--without-tcl \
			--without-systemd \
			--with-zlib="yes" \
			--enable-depend

HOST_CFLAGS += -std=gnu99

MESON_ARGS += \
		-Dnls=$(if $(CONFIG_BUILD_NLS),en,dis)abled \
		-Dicu=$(if $(CONFIG_BUILD_NLS),en,dis)abled \
		-Ddocs=disabled \
		-Ddocs_pdf=disabled \
		-Drpath=false \
		-Dbonjour=disabled \
		-Dbsd_auth=disabled \
		-Dgssapi=disabled \
		-Dldap=disabled \
		-Dlibxml=disabled \
		-Dlibxslt=disabled \
		-Dssl=none \
		-Dpam=disabled \
		-Dplperl=disabled \
		-Dplpython=disabled \
		-Dpltcl=disabled \
		-Dsystemd=disabled \
		-Dzlib=enabled \
		-Dspinlocks=$(if $(CONFIG_arc),true,false)

define Host/Configure
	$(call Host/Configure/Default)
endef

# Need a native zic and pg_config for build
define Host/Compile
	+$(HOST_MAKE_VARS) MAKELEVEL=0 $(MAKE) -C $(HOST_BUILD_DIR)/src/bin/pg_config CC="$(HOSTCC)"
	+$(HOST_MAKE_VARS) MAKELEVEL=0 $(MAKE) -C $(HOST_BUILD_DIR)/src/timezone CC="$(HOSTCC)"
endef

define Host/Install
	$(INSTALL_DIR) $(STAGING_DIR_HOSTPKG)/lib/
	$(INSTALL_BIN) $(HOST_BUILD_DIR)/src/common/libpgcommon.a $(STAGING_DIR_HOSTPKG)/lib/
	$(INSTALL_BIN) $(HOST_BUILD_DIR)/src/port/libpgport.a $(STAGING_DIR_HOSTPKG)/lib/
	$(INSTALL_BIN) $(HOST_BUILD_DIR)/src/bin/pg_config/pg_config $(STAGING_DIR_HOSTPKG)/lib/
	$(INSTALL_DIR) $(STAGING_DIR_HOSTPKG)/bin/
	$(INSTALL_BIN) $(HOST_BUILD_DIR)/src/timezone/zic $(STAGING_DIR_HOSTPKG)/bin/
endef

define Package/libpq/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libpq.so.* $(1)/usr/lib/
endef

define Package/pgsql-cli/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/psql $(1)/usr/bin/
endef

define Package/pgsql-cli-extra/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(foreach bin,$(PGSQL_CLI_EXTRA_BIN),$(PKG_INSTALL_DIR)/usr/bin/$(bin)) $(1)/usr/bin/
endef

define Package/pgsql-server/conffiles
/etc/config/postgresql
endef

define Package/pgsql-server/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(foreach bin,$(PGSQL_SERVER_BIN),$(PKG_INSTALL_DIR)/usr/bin/$(bin)) $(1)/usr/bin/

	ln -sf postgres $(1)/usr/bin/postmaster

	$(INSTALL_DIR) $(1)/usr/share/postgresql
	$(CP) $(PKG_INSTALL_DIR)/usr/share/postgresql/* \
		$(1)/usr/share/postgresql

	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/postgresql \
		$(1)/usr/lib/

	$(INSTALL_DIR) $(1)/lib/functions
	$(INSTALL_BIN) ./files/postgresql.sh $(1)/lib/functions/

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) ./files/postgresql.config $(1)/etc/config/postgresql

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/postgresql.init $(1)/etc/init.d/postgresql
endef

define Build/Configure
	$(call Build/Configure/Default,$(FAKE_CONFIGURE_ARGS))
	make -C $(PKG_BUILD_DIR) maintainer-clean
	$(call Build/Configure/Meson)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(STAGING_DIR_HOSTPKG)/lib/pg_config $(1)/usr/bin
	$(INSTALL_DIR) $(1)/host/bin/
	$(LN) $(STAGING_DIR)/usr/bin/pg_config $(1)/host/bin
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/libpq $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/include/libpq-fe.h $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/include/pg_config.h $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/include/pg_config_manual.h $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/include/postgres_ext.h $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/include/pg_config_ext.h $(1)/usr/include/
	$(CP) $(PKG_INSTALL_DIR)/usr/include/postgresql $(1)/usr/include/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libpq.{a,so*} $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/libpq.pc $(1)/usr/lib/pkgconfig/
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,libpq))
$(eval $(call BuildPackage,pgsql-cli))
$(eval $(call BuildPackage,pgsql-cli-extra))
$(eval $(call BuildPackage,pgsql-server))
