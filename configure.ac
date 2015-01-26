# Copyright (c) 2013-2014, ARM Limited
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.

AC_PREREQ([2.69])
AC_INIT([pencil], [0.01], [carp@arm.com])
AC_CONFIG_MACRO_DIR([m4])

dnl Automake
AC_CONFIG_AUX_DIR([build-aux])
AM_INIT_AUTOMAKE([-Wall -Werror foreign])
AM_MAINTAINER_MODE([enable])

dnl Programs
AC_PROG_SED
AX_PROG_JAVAC
AX_PROG_SCALA
AC_PATH_PROG(ANT, ant)
AC_PATH_PROG(SCALADOC, scaladoc)

abs_builddir=`pwd` # ${ac_abs_builddir} 
abs_srcdir=$(readlink -m "${srcdir}")

dnl Paths to required jars
AX_XTRA_CLASSPATH(SCALAC_CLASSPATH,scala.tools.ant.Scalac,scala-compiler.jar,[],[AC_MSG_ERROR([Could not find scala-compiler.jar, add it to CLASSPATH])])
AC_SUBST(SCALAC_CLASSPATH)

AX_XTRA_CLASSPATH(SCALALIB_CLASSPATH,scala.App,scala-library.jar,[],[AC_MSG_ERROR([Could not find scala-library.jar, add it to CLASSPATH])])
AC_SUBST(SCALALIB_CLASSPATH)

AX_XTRA_CLASSPATH(SCALAREFLECT_CLASSPATH,scala.reflect.api.BuildUtils,scala-reflect.jar,[],[AC_MSG_ERROR([Could not find scala-reflect.jar, add it to CLASSPATH])])
AC_SUBST(SCALAREFLECT_CLASSPATH)

AX_XTRA_CLASSPATH(ANTLR3_CLASSPATH,org.antlr.Tool,antlr3.jar,[],[AC_MSG_ERROR([Could not find antlr3.jar, add it to CLASSPATH])])
AC_SUBST(ANTLR3_CLASSPATH)

AX_XTRA_CLASSPATH(ANT_ANTLR3_CLASSPATH,org.apache.tools.ant.antlr.ANTLR3,ant-antlr3.jar,[],[AC_MSG_ERROR([Could not find ant-antlr3.jar, add it to CLASSPATH])])
AC_SUBST(ANT_ANTLR3_CLASSPATH)

AC_SUBST(CLASSPATH)


dnl FileCheck installation
AC_SUBST(FILE_CHECK_TOOL)
AC_ARG_WITH([filecheck-home],
            [AS_HELP_STRING([--with-filecheck-home=path],[Directory containing FileCheck])],
            [FILE_CHECK_HOME="$with_filecheck_home"],[])
AC_CHECK_PROG([filecheck_home_found], ["FileCheck"], [yes], [no], ["$FILE_CHECK_HOME:$PATH"])
if test "x$filecheck_home_found" != "xyes"; then
  if test "x$FILE_CHECK_HOME" != "x"; then
      AC_MSG_ERROR([could not find FileCheck executable])
  else
      AC_MSG_WARN([FileCheck is disabled (use --with-filecheck-home=path to enable)])
      FILE_CHECK_TOOL=
  fi
else
  FILE_CHECK_TOOL=`PATH=$PATH:$FILE_CHECK_HOME which FileCheck`
fi


AC_CHECK_HEADERS([pencil.h], [HAVE_PENCIL_H=yes], [HAVE_PENCIL_H=no])

dnl pencil runtime
AC_SUBST(PENCIL_UTIL_BUILDDIR)
AC_SUBST(PENCIL_UTIL_SRCDIR)
AX_SUBMODULE(pencil-util,no|build|bundled|system,bundled)
case "$with_pencil_util" in
no)
	#TODO: disable check
	;;
bundled)
	PENCIL_UTIL_BUILDDIR="${abs_builddir}/pencil-util"
	PENCIL_UTIL_SRCDIR="${abs_srcdir}/pencil-util"
	;;
build)
	PENCIL_UTIL_BUILDDIR=`echo @abs_builddir@ | $with_pencil_util_builddir/config.status --file=-`
	PENCIL_UTIL_SRCDIR=`echo @abs_srcdir@ | $with_pencil_util_builddir/config.status --file=-`
	;;
system)
	if test "x{HAS_PENCIL_H}"="xne"; then
		AC_MSG_ERROR([pencil.h not found])
	fi
	;;
esac

AC_SUBST(PENCIL_UTIL_CPPARGS)
PENCIL_UTIL_CPPARGS=""
AC_SUBST(PENCIL_H_PATH)
PENCIL_H_PATH="pencil.h"
case "$with_pencil_util" in
bundled|build)
	PENCIL_UTIL_CPPARGS="-I${PENCIL_UTIL_SRCDIR}/include"
	PENCIL_H_PATH="${PENCIL_UTIL_SRCDIR}/include/pencil.h"
	;;
esac


dnl Generate output
AC_CONFIG_FILES([pencil-linker], [chmod +x pencil-linker])
AC_CONFIG_FILES([pencil-optimizer], [chmod +x pencil-optimizer])
AC_CONFIG_FILES([Makefile])
AC_CONFIG_FILES([testsuite/code/carp-common.h])

if test x"$with_pencil_util" = xbundled; then
	AC_CONFIG_SUBDIRS(pencil-util)
fi

AC_OUTPUT
