PREFIX?=/usr/local
LIBS=-lsodium
CFLAGS=-Wall -fPIC -O2 -g $(INC) #-DTRACE -DNORANDOM
LDFLAGS=-g $(LIBS)
CC=gcc
SOEXT=so

SODIUM_NEWER_THAN_1_0_18 := $(shell pkgconf --atleast-version=1.0.19 libsodium; echo $$?)

all: bin libsphinx.so tests
bin: bin/challenge bin/respond bin/derive bin/2pass

win: CC=x86_64-w64-mingw32-gcc
win: INC=-Iwin/libsodium-win64/include/sodium -Iwin/libsodium-win64/include
win: LIBS=-L. -Lwin/libsodium-win64/lib/ -Wl,-Bstatic -lsodium -Wl,-Bdynamic
win: SOEXT=dll
win: EXT=.exe
win: MAKETARGET=win
win: win/libsodium-win64 exe libsphinx.$(SOEXT) tests$(EXT)
exe: bin/challenge$(EXT) bin/respond$(EXT) bin/derive$(EXT)

android: INC=-I$(SODIUM) -I$(SODIUM)/sodium
android: LIBS=-Wl,-Bstatic -Wl,-Bdynamic -lsodium -L.
android: EXTRA_OBJECTS=jni.o
android: jni.o libsphinx.so

tests$(EXT): tests/sphinx$(EXT)

bin/challenge$(EXT): bin/challenge.c
	$(CC) $(CFLAGS) -o bin/challenge$(EXT) bin/challenge.c $(LDFLAGS)

bin/respond$(EXT): bin/respond.c
	$(CC) $(CFLAGS) -o bin/respond$(EXT) bin/respond.c $(LDFLAGS)

bin/derive$(EXT): bin/derive.c
	$(CC) $(CFLAGS) -o bin/derive$(EXT) bin/derive.c $(LDFLAGS)

bin/2pass$(EXT): bin/2pass.c
	$(CC) $(CFLAGS) -o bin/2pass$(EXT) bin/2pass.c $(LDFLAGS)

libsphinx.$(SOEXT): common.o sphinx.o $(EXTRA_OBJECTS)
	$(CC) -shared -fpic $(CFLAGS) -o libsphinx.$(SOEXT) common.o sphinx.o $(EXTRA_OBJECTS) $(LDFLAGS)

tests/sphinx$(EXT): tests/test.c libsphinx.$(SOEXT)
	$(CC) $(CFLAGS) -o tests/sphinx$(EXT) tests/test.c -L. -lsphinx $(LDFLAGS)

win/libsodium-win64:
	@echo 'win/libsodium-win64 not found.'
	@echo 'download and unpack latest libsodium-*-mingw.tar.gz and unpack into win/'
	@echo 'https://download.libsodium.org/libsodium/releases/'
	@false

install: $(PREFIX)/lib/libsphinx.$(SOEXT) $(PREFIX)/include/sphinx.h

$(PREFIX)/lib/libsphinx.$(SOEXT): libsphinx.$(SOEXT)
	cp $< $@

$(PREFIX)/include/sphinx.h: sphinx.h
	cp $< $@

%.o: %.c
	$(CC) $(CFLAGS) -o $@ -c $<

clean:
	rm -f bin/sphinx bin/challenge bin/respond bin/derive libsphinx.so
	rm -f tests/sphinx tests/sphinx.exe *.o
	rm -f bin/sphinx.exe bin/challenge.exe bin/respond.exe bin/derive.exe libsphinx.dll

.PHONY: bin clean install
