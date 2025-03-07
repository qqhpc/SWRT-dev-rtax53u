include ../common.mak

AUTHLIB=libsw_hw_auth.so
TARGET=sw-hw-auth-app
TOOL=hw_auth

CFLAGS  += -Werror -fPIC -I.
CFLAGS	+= -I$(TOP)/shared -I$(SRCBASE)/include -I$(SRCBASE)/common/include
CFLAGS	+= $(if $(WLAN_ComponentIncPath),$(WLAN_ComponentIncPath),$(addprefix -I,$(wildcard $(SRCBASE)/shared/bcmwifi/include)))
CFLAGS	+= -s -O2

# BUILD_NAME
ifneq ($(findstring 4G-,$(BUILD_NAME)),)
TARGET_MODEL = RT$(subst -,,$(BUILD_NAME))
else ifneq ($(findstring DSL,$(BUILD_NAME)),)
TARGET_MODEL = $(subst -,_,$(BUILD_NAME))
else
TARGET_MODEL = $(subst -,,$(subst +,P,$(BUILD_NAME)))
endif
CFLAGS  += -D$(TARGET_MODEL)
CFLAGS  += -D$(SWRT_NAME)
CFLAGS += -DSWRT_VER_MAJOR_$(SWRT_VER_MAJOR)
CFLAGS  += -I$(TOP)/openssl/include

TARGET_LIBS = -L$(TOP)/openssl -lssl -lcrypto -ldl -lm
AUTHLIB_LIBS = -L$(TOP)/openssl -lssl -lcrypto -ldl -lm

LDFLAGS = -L.
LDFLAGS += -L$(TOP_PLATFORM)/nvram$(BCMEX)${EX7} ${EXTRA_NV_LDFLAGS} -lnvram
LDFLAGS += -L$(TOP)/shared -L$(INSTALLDIR)/shared/usr/lib -lshared
ifeq ($(RTCONFIG_BCM_502L07P2),y)
LDFLAGS += -L$(TOP_PLATFORM)/ethctl_lib -lethctl
endif
ifeq ($(HND_ROUTER),y)
CFLAGS  += -DCONFIG_BCMWL5
LDFLAGS += $(EXTRALDFLAGS)
endif

OBJS = sw_auth.o hw_auth.o

# $(TARGET) and $(TOOL) is for debug only, don't enable it
all : clean hw_auth.o sw_auth.o


$(AUTHLIB): $(OBJS)
	@echo "[sw-hw-auth] $(AUTHLIB)"
	@$(CC) $(OBJS) -shared -o $(AUTHLIB) $(CFLAGS) $(LDFLAGS) $(AUTHLIB_LIBS)

$(TARGET): app.o $(OBJS) 
	@echo "[sw-hw-auth] $(TARGET)"
	@$(CC) $(CFLAGS) $(LDFLAGS) $(TARGET_LIBS) -o $@ $^

$(TOOL) : main.o $(OBJS)
	@echo "[sw-hw-auth] $(TOOL)"
	@$(CC) $(CFLAGS) $(LDFLAGS) $(AUTHLIB_LIBS) -o $@ $^

install: all
	@echo "[sw-hw-auth] Installing..."
	-@install -d $(INSTALLDIR)/usr/sbin #debug
	-@install -m 755 $(TARGET) $(INSTALLDIR)/usr/sbin #debug
	-@install -d $(INSTALLDIR)/usr/sbin #debug
	-@install -m 755 $(TOOL) $(INSTALLDIR)/usr/sbin #debug

clean:
	rm -f *.o *.so $(TARGET) $(TOOL)

