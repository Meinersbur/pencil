# @configure_input@

ACLOCAL_AMFLAGS = -I m4

ALL=optimizer linker
SUBDIRS = @PENCIL_UTIL_SUBDIR@
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

CLEANFILES = dbg.log linker.log linker.sum optimizer.log optimizer.sum \
 testsuite/code/pencil_pre.h testsuite/code/pencil_post.h

EXTRA_DIST = README.txt LICENCE.txt src res example build.xml autogen.sh create-packages.sh testsuite grammar/pencil.g etc/README example/Example.scala Makefile README.txt



all-local: ${ALL}

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

clean-local: docs-clean packages-clean
	${ANT} -f "${abs_srcdir}/build.xml" "-Dbuilddir=${abs_builddir}" clean
	rm -f testsuite/code/pencil/simple/*.pencil.*
	
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
packages: linker optimizer
	fakeroot ./create-packages.sh

packages-clean:
	rm -f *.deb
	rm -f *.rpm

bin_SCRIPTS = pencil-linker pencil-optimizer
pkgdata_DATA = linker.jar optimizer.jar pencil.jar


.PHONY: check-optimizer check-linker

if HAS_PENCIL_UTIL

check-local: check-optimizer check-linker

testsuite/code/pencil_pre.h: Makefile testsuite/code
	mkdir -p "${abs_builddir}/testsuite/code"
	echo "#include <pencil.h>" | cpp -P -D__PENCIL__ @PENCIL_UTIL_CPPARGS@ > $@

check-optimizer: testsuite/code/pencil_pre.h optimizer
	mkdir -p "${abs_builddir}/testsuite/code/pencil/simple"
	runtest --tool optimizer --srdir "${abs_srcdir}/testsuite" --objdir "${abs_builddir}/testsuite" --outdir "${abs_builddir}" --debug

check-linker: testsuite/code/pencil_pre.h linker
	mkdir -p "${abs_builddir}/testsuite/code/pencil/simple"
	runtest --tool linker --srdir "${abs_srcdir}/testsuite" --objdir "${abs_builddir}/testsuite" --outdir "${abs_builddir}" --debug

endif
