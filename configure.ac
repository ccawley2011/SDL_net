dnl Process this file with autoconf to produce a configure script.
AC_INIT
AC_CONFIG_MACRO_DIR([acinclude])
AC_CONFIG_SRCDIR([SDLnet.c])

dnl Set various version strings - taken gratefully from the GTk sources

# Making releases:
#   MICRO_VERSION += 1;
#   INTERFACE_AGE += 1;
#   BINARY_AGE += 1;
# if any functions have been added, set INTERFACE_AGE to 0.
# if backwards compatibility has been broken,
# set BINARY_AGE and INTERFACE_AGE to 0.

MAJOR_VERSION=2
MINOR_VERSION=0
MICRO_VERSION=1
INTERFACE_AGE=1
BINARY_AGE=1
VERSION=$MAJOR_VERSION.$MINOR_VERSION.$MICRO_VERSION

AC_SUBST(MAJOR_VERSION)
AC_SUBST(MINOR_VERSION)
AC_SUBST(MICRO_VERSION)
AC_SUBST(INTERFACE_AGE)
AC_SUBST(BINARY_AGE)
AC_SUBST(VERSION)

dnl libtool versioning
LT_INIT([win32-dll])

LT_RELEASE=$MAJOR_VERSION.$MINOR_VERSION
LT_CURRENT=`expr $MICRO_VERSION - $INTERFACE_AGE`
LT_REVISION=$INTERFACE_AGE
LT_AGE=`expr $BINARY_AGE - $INTERFACE_AGE`
LT_EXTRA="" dnl for OS2 dll name

AC_SUBST(LT_RELEASE)
AC_SUBST(LT_CURRENT)
AC_SUBST(LT_REVISION)
AC_SUBST(LT_AGE)
AC_SUBST(LT_EXTRA)

dnl Detect the canonical build and host environments
AC_CANONICAL_HOST

dnl Setup for automake
AM_INIT_AUTOMAKE(SDL2_net, $VERSION)

dnl Check for tools
AC_PROG_CC
AC_PROG_CXX
AC_PROG_INSTALL
AC_PROG_MAKE_SET
if test -z "$host_alias"; then
    hostaliaswindres=
else
    hostaliaswindres="$host_alias-windres"
fi
AC_CHECK_PROGS(WINDRES, [windres $hostaliaswindres $host_os-windres])

case "$host" in
    *-*-beos*)
        ac_default_prefix=/boot/develop/tools/gnupro
        ;;
    *-*-cygwin* | *-*-mingw*)
        if test "$build" != "$host"; then # cross-compiling
            # Default cross-compile location
            ac_default_prefix=/usr/local/cross-tools/$host
        else
            # Look for the location of the tools and install there
            if test "$BUILD_PREFIX" != ""; then
                ac_default_prefix=$BUILD_PREFIX
            fi
        fi
        if test x$WINDRES != x; then
            use_version_rc=true
        fi
        ;;
    *-*-os2*)
        # disable static builds on os/2
        enable_static=no
        # -DBUILD_SDL is needed for DECLSPEC
        CFLAGS="$CFLAGS -DBUILD_SDL"
        # OS/2 does not support a DLL name longer than 8 characters.
        LT_EXTRA="-os2dllname SDL2net"
        ;;
esac
AM_CONDITIONAL(USE_VERSION_RC, test x$use_version_rc = xtrue)

dnl Figure out which networking libraries to use
case "$host" in
    *-*-cygwin* | *-*-mingw*)
        INETLIB="-lws2_32 -liphlpapi"
        ;;
    sparc*-*-solaris*)
        INETLIB="-lsocket -lnsl"
        ;;
    *-*-qnx*)
        INETLIB="-lsocket"
        ;;
    *-*-beos*)
        INETLIB=""
        ;;
    *-*-haiku*)
        INETLIB="-lnetwork"
        ;;
    *)
        INETLIB=""
        ;;
esac
AC_SUBST(INETLIB)

dnl Check for SDL
SDL_VERSION=2.0.0
AC_SUBST(SDL_VERSION)
AM_PATH_SDL2($SDL_VERSION,
            :,
	    AC_MSG_ERROR([*** SDL version $SDL_VERSION not found!])
)
CFLAGS="$CFLAGS $SDL_CFLAGS"
LIBS="$LIBS $SDL_LIBS"

dnl Check for GUI library for the chat client
have_GUI=no
AC_ARG_ENABLE(gui,
[AS_HELP_STRING([--enable-gui],[Try to build the GUI test programs [default=yes]])],
              , enable_gui=yes)
if test x$enable_gui = xyes; then
    AC_LANG_PUSH(C++)
    AC_CHECK_LIB(GUI, GUI_Create, have_GUI=yes)
    AC_LANG_POP(C++)
    if test x$have_GUI != xyes; then
        AC_MSG_WARN([*** Can't find the GUI library - not building test programs
The GUI library can be found at:
http://www.libsdl.org/projects/GUIlib/
])
    fi
fi
AM_CONDITIONAL(HAVE_GUI_LIB, test x$have_GUI = xyes)

dnl C++ flags are the same as the C flags
CXXFLAGS="$CXXFLAGS $CFLAGS"

AC_SUBST([WINDRES])

# Finally create all the generated files
AC_CONFIG_FILES([
Makefile SDL2_net.spec SDL2_net.pc
])
AC_OUTPUT
