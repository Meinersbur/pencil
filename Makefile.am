# @configure_input@

ACLOCAL_AMFLAGS = -I m4
#AUTOMAKE_OPTIONS = dejagnu

#EXTRA_DEJAGNU_SITE_CONFIG = testsuite/site.exp
#DEJATOOL = optimizer 
#linker
#RUNTESTFLAGS = --debug
#override RUNTESTDEFAULTFLAGS = --tool $$tool --srcdir $$abs_srcdir/testsuite
#srcdir = ${abs_srcdir}

#ALL=optimizer linker
#SUBDIRS = testsuite
DOCS = pencil optimizations

SCALADOC=@SCALADOC@

export FILE_CHECK_TOOL=@FILE_CHECK_TOOL@
export G_PREFIX=${PREFIX}/@prefix@

JAR_ROOT=${G_PREFIX}/share/pencil
LIB_ROOT=${G_PREFIX}/lib/
BIN_ROOT=${G_PREFIX}/bin/
ANT=@ANT@
abs_srcdir=@abs_srcdir@
abs_builddir=@abs_builddir@
SCALAC_CLASSPATH=@SCALAC_CLASSPATH@
SCALALIB_CLASSPATH=@SCALALIB_CLASSPATH@
SCALAREFLECT_CLASSPATH=@SCALAREFLECT_CLASSPATH@
ANTLR3_CLASSPATH=@ANTLR3_CLASSPATH@
ANT_ANTLR3_CLASSPATH=@ANT_ANTLR3_CLASSPATH@
export CLASSPATH:=@CLASSPATH@:${SCALAC_CLASSPATH}${SCALALIB_CLASSPATH}${SCALAREFLECT_CLASSPATH}${ANTLR3_CLASSPATH}${ANT_ANTLR3_CLASSPATH}


#all-local: ${ALL}

# These are phony (forced execution) because we do not catch all dependendencies; ant should handle them anyway
.PHONY: pencil.jar antcommon linker.jar optimizer.jar

pencil.jar: 
	${ANT} -f "${abs_srcdir}/build.xml" "-Dbuilddir=${abs_builddir}" dist-lib

antcommon:
	${ANT} -f "${abs_srcdir}/build.xml" "-Dbuilddir=${abs_builddir}" generate-versioninfo run-antlr
	
optimizer.jar: pencil.jar antcommon
	${ANT} -f "${abs_srcdir}/build.xml" "-Dbuilddir=${abs_builddir}" dist-optimizer

linker.jar: pencil.jar antcommon
	${ANT} -f "${abs_srcdir}/build.xml" "-Dbuilddir=${abs_builddir}" dist-linker


.PHONY: optimizer linker

linker: linker.jar

optimizer: optimizer.jar

#check:${ALL:%=%-check}

#%-check:
#	mkdir -p "${abs_builddir}/testsuite"
#	cd "${abs_builddir}/testsuite" && runtest --srcdir "${abs_srcdir}/testsuite" --tool $*


clean-local: docs-clean packages-clean
	${ANT} -f "${abs_srcdir}/build.xml" "-Dbuilddir=${abs_builddir}" clean



docs: ${DOCS:%=%-docs}

pencil-docs:
	mkdir -p "${abs_builddir}/docs/pencil/html"
	$(SCALADOC) -d "${abs_builddir}/docs/pencil/html" `find "${abs_srcdir}/src/scala/com/arm/carp/pencil" -name '*.scala'` `find "${abs_builddir}/gen/scala" -name '*.scala'`

optimizations-docs:
	echo ${CLASSPATH}
	mkdir -p "${abs_builddir}/docs/optimizations/html"
	$(SCALADOC) -d "${abs_builddir}/docs/optimizations/html" `find "${abs_srcdir}/src/scala/com/arm/carp/pencil" -name '*.scala'` `find "${abs_srcdir}/src/scala/com/arm/carp/apps/optimizer/passes" -name '*.scala'` `find "${abs_builddir}/gen/scala" -name '*.scala'`

docs-clean: ${DOCS:%=%-docs-clean}

pencil-docs-clean:
	rm -rf "${abs_builddir}/docs/pencil/*"

optimizations-docs-clean:
	rm -rf "${abs_builddir}/docs/optimizations/*"

	
.PHONY: packages
packages:
	fakeroot ./create-packages.sh

packages-clean:
	rm -f *.deb
	rm -f *.rpm

bin_SCRIPTS = pencil-linker pencil-optimizer
pkgdata_DATA = linker.jar optimizer.jar pencil.jar

check: check-optimizer check-linker

.PHONY: check-optimizer check-linker

testsuite:
	mkdir -p "${abs_builddir}/testsuite"

testsuite/code: testsuite
	mkdir -p "${abs_builddir}/testsuite/code"
	
testsuite/code/pencil_pre.h: Makefile testsuite/code
	echo "#include <pencil.h>" | cpp -P -D__PENCIL__ @PENCIL_UTIL_CPPARGS@ > $@

testsuite/code/pencil_post.h: Makefile testsuite/code
	if test "x@PENCIL_UTIL_SRCDIR@"="x"; then                              \
		echo "#include <@PENCIL_UTIL_SRCDIR@/include/pencil.h>" > $@;  \
	else                                                                   \
		echo "#include <pencil.h>" > $@;                               \
	fi; 

check-optimizer: testsuite testsuite/code/pencil_pre.h testsuite testsuite/code/pencil_post.h optimizer
	runtest --tool optimizer --srdir "${abs_srcdir}/testsuite" --objdir "${abs_builddir}/testsuite" --outdir "${abs_builddir}"  --debug 

check-linker: testsuite testsuite/code/pencil_pre.h testsuite testsuite/code/pencil_post.h linker
	runtest --tool linker --srdir "${abs_srcdir}/testsuite" --objdir "${abs_builddir}/testsuite" --outdir "${abs_builddir}"