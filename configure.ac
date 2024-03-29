#
# Making releases:
#   gtkglext_micro_version += 1;
#   gtkglext_interface_age += 1;
#   gtkglext_binary_age += 1;
# if any functions have been added, set gtkglext_interface_age to 0.
# if backwards compatibility has been broken,
# set gtkglext_binary_age and gtkglext_interface_age to 0.
#
m4_define([gtkglext_major_version], [1])
m4_define([gtkglext_minor_version], [2])
m4_define([gtkglext_micro_version], [0])
m4_define([gtkglext_interface_age], [0])
m4_define([gtkglext_binary_age],
          [m4_eval(100 * gtkglext_minor_version + gtkglext_micro_version)])
m4_define([gtkglext_version],
          [gtkglext_major_version.gtkglext_minor_version.gtkglext_micro_version])

# This is the X.Y used in -lgtkglext-FOO-X.Y
m4_define([gtkglext_api_major_version], [1])
m4_define([gtkglext_api_minor_version], [0])
m4_define([gtkglext_api_version],
          [gtkglext_api_major_version.gtkglext_api_minor_version])

# libtool version related macros
m4_define([gtkglext_lt_release],
          [gtkglext_major_version.gtkglext_minor_version])
m4_define([gtkglext_lt_current],
          [m4_eval(100 * gtkglext_minor_version + gtkglext_micro_version - gtkglext_interface_age)])
m4_define([gtkglext_lt_revision],
          [gtkglext_interface_age])
m4_define([gtkglext_lt_age],
          [m4_eval(gtkglext_binary_age - gtkglext_interface_age)])
m4_define([gtkglext_lt_current_minus_age],
          [m4_eval(gtkglext_lt_current - gtkglext_lt_age)])

# if the minor version number is odd, then we want debugging.  Otherwise
# we only want minimal debugging support.
m4_define([gtkglext_debug_default],
          [m4_if(m4_eval(gtkglext_minor_version % 2), [1], [yes], [minimum])])dnl

# Process this file with autoconf to produce a configure script.
AC_INIT([gtkglext], [gtkglext_version],
        [gtkglext-list@gnome.org])

AC_PREREQ([2.63])

AC_CONFIG_SRCDIR([gdk/gdkgl.h])
AC_CONFIG_MACRO_DIR([m4macros])

# Save this value here, since automake will set cflags later
cflags_set=${CFLAGS+set}

# Initialize automake stuff
AM_INIT_AUTOMAKE([1.11 no-define dist-bzip2 dist-zip dist-xz])

# Specify a configuration file
AC_CONFIG_HEADERS([config.h])

AC_CANONICAL_HOST

# GtkGLExt version
GTKGLEXT_MAJOR_VERSION=gtkglext_major_version
GTKGLEXT_MINOR_VERSION=gtkglext_minor_version
GTKGLEXT_MICRO_VERSION=gtkglext_micro_version
GTKGLEXT_INTERFACE_AGE=gtkglext_interface_age
GTKGLEXT_BINARY_AGE=gtkglext_binary_age
GTKGLEXT_VERSION=gtkglext_version
# This is the X.Y used in -lgtkglext-FOO-X.Y
GTKGLEXT_API_MAJOR_VERSION=gtkglext_api_major_version
GTKGLEXT_API_MINOR_VERSION=gtkglext_api_minor_version
GTKGLEXT_API_VERSION=gtkglext_api_version
API_MJ=$GTKGLEXT_API_MAJOR_VERSION
API_MI=$GTKGLEXT_API_MINOR_VERSION
AC_SUBST([GTKGLEXT_MAJOR_VERSION])
AC_SUBST([GTKGLEXT_MINOR_VERSION])
AC_SUBST([GTKGLEXT_MICRO_VERSION])
AC_SUBST([GTKGLEXT_INTERFACE_AGE])
AC_SUBST([GTKGLEXT_BINARY_AGE])
AC_SUBST([GTKGLEXT_VERSION])
AC_SUBST([GTKGLEXT_API_MAJOR_VERSION])
AC_SUBST([GTKGLEXT_API_MINOR_VERSION])
AC_SUBST([GTKGLEXT_API_VERSION])
AC_SUBST([API_MJ])
AC_SUBST([API_MI])

# libtool versioning
GTKGLEXT_RELEASE=gtkglext_lt_release
GTKGLEXT_CURRENT=gtkglext_lt_current
GTKGLEXT_REVISION=gtkglext_lt_revision
GTKGLEXT_AGE=gtkglext_lt_age
GTKGLEXT_CURRENT_MINUS_AGE=gtkglext_lt_current_minus_age
AC_SUBST([GTKGLEXT_RELEASE])
AC_SUBST([GTKGLEXT_CURRENT])
AC_SUBST([GTKGLEXT_REVISION])
AC_SUBST([GTKGLEXT_AGE])
AC_SUBST([GTKGLEXT_CURRENT_MINUS_AGE])

# Checks for programs.

dnl Initialize libtool
AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_OBJC
AM_DISABLE_STATIC
LT_PREREQ([2.2.6])
LT_INIT([win32-dll])
LT_LANG([C++])

AC_CHECK_PROG([ms_librarian], [lib.exe], [yes], [no])

AC_PROG_LN_S

AC_PATH_PROGS([PERL], [perl5 perl])

##################################################
# Checks for gtk-doc and docbook-tools
##################################################

GTK_DOC_CHECK

os_win32=no
MATH_LIB=-lm
AC_MSG_CHECKING([for native Win32])
case "$host_os" in
  *mingw*)
    os_win32=yes
    MATH_LIB=
    ;;
  *)
    ;;
esac
AC_MSG_RESULT([$os_win32])
AM_CONDITIONAL([OS_WIN32], [test "x$os_win32" = "xyes"])
AC_SUBST([MATH_LIB])

AM_CONDITIONAL([MS_LIB_AVAILABLE], [test "x$ms_librarian" = "xyes"])


# Platform-specific compiler options
case "$host_os" in
  irix5* | irix6*)
    # Suppress linker warnings
    if test "x$with_gnu_ld" = "xno"; then
      LDFLAGS="$LDFLAGS -Wl,-woff,84 -Wl,-woff,127"
    fi
    ;;
  *)
    ;;
esac

# Ensure MSVC-compatible struct packing convention is used when
# compiling for Win32 with gcc.
# What flag to depends on gcc version: gcc3 uses "-mms-bitfields", while
# gcc2 uses "-fnative-struct".
if test "x$os_win32" = "xyes"; then
  if test "x$GCC" = "xyes"; then
    msnative_struct=''
    AC_MSG_CHECKING([how to get MSVC-compatible struct packing])
    if test -z "$ac_cv_prog_CC"; then
      our_gcc="$CC"
    else
      our_gcc="$ac_cv_prog_CC"
    fi
    case `$our_gcc --version | sed -e 's,\..*,.,' -e q` in
      2.)
	if $our_gcc -v --help 2>/dev/null | grep fnative-struct >/dev/null; then
	  msnative_struct='-fnative-struct'
	fi
	;;
      *)
	if $our_gcc -v --help 2>/dev/null | grep ms-bitfields >/dev/null; then
	  msnative_struct='-mms-bitfields'
	fi
	;;
    esac
    if test x"$msnative_struct" = x ; then
      AC_MSG_RESULT([no way])
      AC_MSG_WARN([produced libraries might be incompatible with MSVC-compiled code])
    else
      CFLAGS="$CFLAGS $msnative_struct"
      AC_MSG_RESULT([${msnative_struct}])
    fi
  fi
fi

if test "x$os_win32" != "xyes"; then
    # libtool option to control which symbols are exported
    # right now, symbols starting with _ are not exported
    LIBTOOL_EXPORT_OPTIONS='-export-symbols-regex "^[[^_]].*"'
else
    # We currently use .def files on Windows
    LIBTOOL_EXPORT_OPTIONS=
fi
AC_SUBST([LIBTOOL_EXPORT_OPTIONS])

#
# We need to get the value of gdktarget before we run AX_CHECK_GLU so
# that we can set with_x appropriately.
#
AC_ARG_WITH([gdktarget],
            [AS_HELP_STRING([--with-gdktarget=@<:@x11/win32@:>@],
                            [select GDK target])],
            [gdktarget=$with_gdktarget],
            [gdktarget=`$PKG_CONFIG --variable=target gdk-2.0`])
AS_IF([test x$gdktarget = xwin32 -o x$gdktarget = xquartz],
      [with_x=no])

# Checks for headers and libraries.

AC_CHECK_HEADERS([inttypes.h stdlib.h string.h])

PKG_CHECK_MODULES([GTK], [gtk+-2.0 >= 2.20], ,
                  [AS_IF([test -z "${GTK_LIBS+x}"],
                         [AC_MSG_FAILURE([GTK+ 2.20 or newer is required.])])])
PKG_CHECK_MODULES([GDK], [gdk-2.0], ,
                  [AS_IF([test -z "${GDK_LIBS+x}"],
                         [AC_MSG_FAILURE([GDK is required.])])])
PKG_CHECK_MODULES([PANGOFT2], [pangoft2], ,
                  [AS_IF([test -z "${PANGOFT2_LIBS+x}"],
                         [AC_MSG_WARN([Pango Freetype support is required to build some of the examples.])])])

GLIB_MKENUMS="`$PKG_CONFIG --variable=glib_mkenums glib-2.0`"
AC_SUBST(GLIB_MKENUMS)

AX_CHECK_GL
AS_IF([test X$no_gl = Xyes],
      [AC_MSG_FAILURE([OpenGL is required.])])
AX_CHECK_GLU
# FIXME: AX_CHECK_GLU's behavior diverges from common autoconf conventions
AM_CONDITIONAL([GLU],[test "$no_glu" != yes])

# Checks for some types in gdk/GL/glxext.h

# GLXFBConfigSGIX
GDKGLEXT_NEED_GLXFBCONFIGSGIX_TYPEDEF=no
AC_CHECK_TYPES([GLXFBConfigSGIX], , ,
               [#include <GL/glx.h>])
if test "x$ac_cv_type_GLXFBConfigSGIX" = "xno" ; then
  GDKGLEXT_NEED_GLXFBCONFIGSGIX_TYPEDEF=yes
fi

# GLXFBConfigIDSGIX
GDKGLEXT_NEED_GLXFBCONFIGIDSGIX_TYPEDEF=no
AC_CHECK_TYPES([GLXFBConfigIDSGIX], , ,
               [#include <GL/glx.h>])
if test "x$ac_cv_type_GLXFBConfigIDSGIX" = "xno" ; then
  GDKGLEXT_NEED_GLXFBCONFIGIDSGIX_TYPEDEF=yes
fi

# GLXPbufferSGIX
GDKGLEXT_NEED_GLXPBUFFERSGIX_TYPEDEF=no
AC_CHECK_TYPES([GLXPbufferSGIX], , ,
               [#include <GL/glx.h>])
if test "x$ac_cv_type_GLXPbufferSGIX" = "xno" ; then
  GDKGLEXT_NEED_GLXPBUFFERSGIX_TYPEDEF=yes
fi

# GLXVideoSourceSGIX
GDKGLEXT_NEED_GLXVIDEOSOURCESGIX_TYPEDEF=no
AC_CHECK_TYPES([GLXVideoSourceSGIX], , ,
               [#include <GL/glx.h>])
if test "x$ac_cv_type_GLXVideoSourceSGIX" = "xno" ; then
  GDKGLEXT_NEED_GLXVIDEOSOURCESGIX_TYPEDEF=yes
fi

# __GLXextFuncPtr
GDKGLEXT_NEED_GLXEXTFUNCPTR_TYPEDEF=no
AC_CHECK_TYPES([__GLXextFuncPtr], , ,
               [#include <GL/glx.h>])
if test "x$ac_cv_type___GLXextFuncPtr" = "xno" ; then
  GDKGLEXT_NEED_GLXEXTFUNCPTR_TYPEDEF=yes
fi

# Checks for typedefs in gdk/GL/glext.h

# GLhalfNV
GDKGLEXT_NEED_GLHALFNV_TYPEDEF=no
AC_CHECK_TYPES([GLhalfNV], , ,
               [#include <GL/gl.h>])
if test "x$ac_cv_type_GLhalfNV" = "xno" ; then
  GDKGLEXT_NEED_GLHALFNV_TYPEDEF=yes
fi

# Checks for library functions.
save_LIBS="$LIBS"
LIBS="$LIBS $MATH_LIB"

AC_FUNC_MALLOC
AC_CHECK_FUNCS([memset sqrt strchr strrchr strstr])

LIBS="$save_LIBS"

case $gdktarget in
  x11|quartz|win32) ;;
  *) AC_MSG_ERROR([Invalid target for GDK: use x11, quartz or win32.]);;
esac
AC_SUBST([gdktarget])

gdkglext_targetlib=libgdkglext-$gdktarget-$GTKGLEXT_API_VERSION.la
gtkglext_targetlib=libgtkglext-$gdktarget-$GTKGLEXT_API_VERSION.la

AC_SUBST([gdkglext_targetlib])
AC_SUBST([gtkglext_targetlib])

##################################################
# Checks for Window System
##################################################

WINDOWING_CFLAGS=""
WINDOWING_LIBS=""

GDKGLEXT_WIN_CFLAGS=""
GDKGLEXT_WIN_LIBS=""

if test "x$gdktarget" = "xx11"; then
  if test "x$no_x" = "xyes" ; then
    AC_MSG_ERROR([X development libraries not found])
  fi

  XMU_LIBS=""
  if test "x$ac_cv_lib_Xmu_XmuLookupStandardColormap" = "xyes" ; then
    XMU_LIBS="-lXmu -lXt $X_PRE_LIBS"
  fi

  WINDOWING_CFLAGS="$X_CFLAGS"
  WINDOWING_LIBS="$X_LIBS $XMU_LIBS -lXext -lX11 $X_EXTRA_LIBS"

  GDKGLEXT_WIN_CFLAGS="$X_CFLAGS"
  GDKGLEXT_WIN_LIBS="$X_LIBS $XMU_LIBS"
fi

if test "x$gdktarget" = "xwin32"; then
  WINDOWING_LIBS="-lgdi32 -luser32 -lkernel32"
  GDKGLEXT_WIN_LIBS="-lgdi32 -luser32 -lkernel32"
fi

AC_SUBST([WINDOWING_CFLAGS])
AC_SUBST([WINDOWING_LIBS])

AC_SUBST([GDKGLEXT_WIN_CFLAGS])
AC_SUBST([GDKGLEXT_WIN_LIBS])

AM_CONDITIONAL([USE_X11], [test x$gdktarget = xx11])
AM_CONDITIONAL([USE_WIN32], [test x$gdktarget = xwin32])
AM_CONDITIONAL([TARGET_QUARTZ], [test x$gdktarget = xquartz])

# Debug option
AC_ARG_ENABLE([debug],
              AS_HELP_STRING([--enable-debug=@<:@no/minimum/yes@:>@],
                             [turn on debugging @<:@default=gtkglext_debug_default@:>@]), ,
              [enable_debug=gtkglext_debug_default])

changequote(,)dnl
if test "x$enable_debug" = "xyes"; then
  if test x$cflags_set != xset ; then
      case " $CFLAGS " in
      *[[\ \	]]-g[[\ \	]]*) ;;
      *) CFLAGS="$CFLAGS -g" ;;
      esac
  fi
  GTKGLEXT_DEBUG_FLAGS="-DG_ENABLE_DEBUG"
else
  if test "x$enable_debug" = "xno"; then
    GTKGLEXT_DEBUG_FLAGS="-DG_DISABLE_ASSERT -DG_DISABLE_CHECKS -DG_DISABLE_CAST_CHECKS"
  else
    GTKGLEXT_DEBUG_FLAGS="-DG_DISABLE_CAST_CHECKS"
  fi
fi
changequote([,])dnl

AC_SUBST([GTKGLEXT_DEBUG_FLAGS])

##################################################
# Output configuration header
##################################################

dnl this section will only be run if config.status is invoked with no
dnl arguments, or with "gdk/gdkglext-config.h" as an argument.
AC_CONFIG_COMMANDS([gdk/gdkglext-config.h],
[
  outfile=gdkglext-config.h-tmp
  cat > $outfile <<\_______EOF
/* gdkglext-config.h
 *
 * This is a generated file.  Please modify `configure.ac'
 */

#ifndef GDKGLEXT_CONFIG_H
#define GDKGLEXT_CONFIG_H

#ifdef __cplusplus
extern "C" {
#endif /* __cplusplus */

_______EOF

	cat >>$outfile <<_______EOF
$gdkglext_windowing
$gdkglext_multihead_support
_______EOF

	cat >>$outfile <<_______EOF
$gdkglext_need_glxfbconfigsgix_typedef
$gdkglext_need_glxfbconfigidsgix_typedef
$gdkglext_need_glxpbuffersgix_typedef
$gdkglext_need_glxvideosourcesgix_typedef
$gdkglext_need_glxextfuncptr_typedef
$gdkglext_need_glhalfnv_typedef
_______EOF

	cat >>$outfile <<_______EOF

#ifdef __cplusplus
}
#endif /* __cplusplus */

#endif /* GDKGLEXT_CONFIG_H */
_______EOF


  if cmp -s $outfile gdk/gdkglext-config.h; then
    AC_MSG_NOTICE([gdk/gdkglext-config.h is unchanged])
    rm -f $outfile
  else
    mv $outfile gdk/gdkglext-config.h
  fi

],[

if test "x$gdktarget" = "xx11" ; then
  gdkglext_windowing='
#define GDKGLEXT_WINDOWING_X11'
elif test "x$gdktarget" = "xwin32" ; then
  gdkglext_windowing='
#define GDKGLEXT_WINDOWING_WIN32'
elif test "x$gdktarget" = "xquartz" ; then
  gdkglext_windowing='
#define GDKGLEXT_WINDOWING_QUARTZ'
fi

if test "x$GDKGLEXT_NEED_GLXFBCONFIGSGIX_TYPEDEF" = "xyes"; then
  gdkglext_need_glxfbconfigsgix_typedef='
#define GDKGLEXT_NEED_GLXFBCONFIGSGIX_TYPEDEF'
fi

if test "x$GDKGLEXT_NEED_GLXFBCONFIGIDSGIX_TYPEDEF" = "xyes"; then
  gdkglext_need_glxfbconfigidsgix_typedef='
#define GDKGLEXT_NEED_GLXFBCONFIGIDSGIX_TYPEDEF'
fi

if test "x$GDKGLEXT_NEED_GLXPBUFFERSGIX_TYPEDEF" = "xyes"; then
  gdkglext_need_glxpbuffersgix_typedef='
#define GDKGLEXT_NEED_GLXPBUFFERSGIX_TYPEDEF'
fi

if test "x$GDKGLEXT_NEED_GLXVIDEOSOURCESGIX_TYPEDEF" = "xyes"; then
  gdkglext_need_glxvideosourcesgix_typedef='
#define GDKGLEXT_NEED_GLXVIDEOSOURCESGIX_TYPEDEF'
fi

if test "x$GDKGLEXT_NEED_GLXEXTFUNCPTR_TYPEDEF" = "xyes"; then
  gdkglext_need_glxextfuncptr_typedef='
#define GDKGLEXT_NEED_GLXEXTFUNCPTR_TYPEDEF'
fi

if test "x$GDKGLEXT_NEED_GLHALFNV_TYPEDEF" = "xyes"; then
  gdkglext_need_glhalfnv_typedef='
#define GDKGLEXT_NEED_GLHALFNV_TYPEDEF'
fi

])


##################################################
# Config files
##################################################

AC_CONFIG_FILES([
gdkglext.pc
gtkglext.pc
gdkglext-uninstalled.pc
gtkglext-uninstalled.pc
gtkglext.nsi
Makefile
gdk/Makefile
gdk/gdkglversion.h
gdk/x11/Makefile
gdk/win32/Makefile
gdk/quartz/Makefile
gtk/Makefile
gtk/gtkglversion.h
docs/Makefile
docs/reference/Makefile
docs/reference/gtkglext/Makefile
docs/reference/gtkglext/version.xml
examples/Makefile
])

AC_OUTPUT


##################################################
# Print configuration
##################################################

echo "
configuration:
	OpenGL CFLAGS:		$GL_CFLAGS
	OpenGL LIBS:		$GL_LIBS
	debug:			$enable_debug
"
