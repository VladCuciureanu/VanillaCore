SVN_VERSION = 71480

CC = clang++-devel

INCDIR =
LIBDIR =
BINDIR = ..
ROBJDIR = release
DOBJDIR = debug
SRV_DIR = /home/game
$(shell if [ ! -d lib ]; then mkdir lib; fi)
$(shell if [ ! -d $(DOBJDIR) ]; then mkdir $(DOBJDIR); fi)
$(shell if [ ! -d $(ROBJDIR) ]; then mkdir $(ROBJDIR); fi)

# Standard Setting
LIBS = -pthread -lm -lmd -llua -llualib
# Removed -fno-rtti 
CFLAGS = -Wall -D_THREAD_SAFE -pipe -msse2 -mssse3 -m32 -stdlib=libc++ -std=c++14

#debug
CFLAGS_DBG = $(CFLAGS)
CLINKFLAGS_DBG = $(CFLAGS_DBG) -Wl,-rpath,/usr/local/lib32/metin2
CFLAGS_DBG += -g3 -O0 -ggdb -DNDEBUG -fexceptions
#CFLAGS_DBG += -fsanitize=address -fno-omit-frame-pointer -fno-optimize-sibling-calls
#CLINKFLAGS_DBG += -fstandalone-debug -fsanitize=address -fno-omit-frame-pointer -fno-optimize-sibling-calls

#release
CFLAGS_REL = $(CFLAGS)
CLINKFLAGS_REL = $(CFLAGS_REL) -Wl,-rpath,/usr/local/lib32/metin2
CFLAGS_REL += -Ofast -g0 -DNDEBUG -fexceptions
#CLINKFLAGS_REL += -flto

# DevIL
LIBS += -lIL -ljpeg

LIBDIR += -L/usr/local/lib/mysql
LIBS += -lmysqlclient -lz

# Miscellaneous external libraries
#INCDIR += -I../../../External/cryptopp
#LIBDIR += -L../../../External/cryptopp
LIBS += -lcryptopp -lgtest

# openssl
INCDIR += -I/usr/include
LIBS += -lssl
LIBS += /usr/lib/libssl.a

# Project Library
INCDIR += -I../../../internal/liblua/include -I../../../internal
INCDIR += -I/usr/local/include
LIBDIR += -L../../../internal/libthecore/lib -L../../../internal/libpoly/lib -L../../../internal/libsql/lib -L../../../internal/libgame/lib -L../../../internal/liblua/lib
LIBDIR += -L/usr/local/lib
LIBS += -lthecore -lpoly -lsql -lgame -llzo2
USE_STACKTRACE = 0
ifeq ($(USE_STACKTRACE), 1)
LIBS += /usr/local/lib/libexecinfo.a
endif

DTARGET  = $(BINDIR)/game_r$(SVN_VERSION)_debug
RTARGET = $(BINDIR)/game_r$(SVN_VERSION)_release

CPPFILES := $(wildcard *.cpp)
OBJ_DBG_FILES := $(addprefix debug/,$(notdir $(CPPFILES:.cpp=.o)))
OBJ_REL_FILES := $(addprefix release/,$(notdir $(CPPFILES:.cpp=.o)))

DCPPOBJS	= $(CPPFILE:%.cpp=$(DOBJDIR)/%.o)
RCPPOBJS	= $(CPPFILE:%.cpp=$(ROBJDIR)/%.o)

default: $(DTARGET) $(RTARGET)

debug: $(DTARGET)

release: $(RTARGET)

$(DOBJDIR)/%.o: %.cpp
	@echo $(CC) $(CFLAGS_DBG) $(INCDIR) -D__SVN_VERSION__=\"$(SVN_VERSION)\" -c $< -o $@
	@$(CC) $(CFLAGS_DBG) $(INCDIR) -D__SVN_VERSION__=\"$(SVN_VERSION)\" -c $< -o $@
$(ROBJDIR)/%.o: %.cpp
	@echo $(CC) $(CFLAGS_REL) $(INCDIR) -D__SVN_VERSION__=\"$(SVN_VERSION)\" -c $< -o $@
	@$(CC) $(CFLAGS_REL) $(INCDIR) -D__SVN_VERSION__=\"$(SVN_VERSION)\" -c $< -o $@

limit_time:
	@echo update limit time
	@python update_limit_time.py

$(DTARGET): $(OBJ_DBG_FILES)
	@echo $(CC) $(CLINKFLAGS_DBG) $(LIBDIR) $(LIBS) -o $(DTARGET)
	@$(CC) $(CLINKFLAGS_DBG) $(LIBDIR) $(OBJ_DBG_FILES) $(LIBS) -o $(DTARGET)

$(RTARGET): $(OBJ_REL_FILES)
	@echo $(CC) $(CLINKFLAGS_REL) $(LIBDIR) $(LIBS) -o $(RTARGET)
	@$(CC) $(CLINKFLAGS_REL) $(LIBDIR) $(OBJ_REL_FILES) $(LIBS) -o $(RTARGET)


clean:
	@rm -f debug/*
	@rm -f release/*
	@rm -f $(BINDIR)/game_r* $(BINDIR)/conv

tag:
	ctags *.cpp *.h *.c

dep:
	makedepend -f Depend $(INCDIR) -I/usr/local/include/c++/v1 -I/usr/include/c++/v1 -prelease/ $(CPPFILE) 2> /dev/null > Depend

install_dbg: $(DTARGET)
	rm -rf $(SRV_DIR)/share/game
	cp $(DTARGET) $(SRV_DIR)/share/game

install_rel: $(RTARGET)
	rm -rf $(SRV_DIR)/share/game
	cp $(RTARGET) $(SRV_DIR)/share/game


sinclude Depend
