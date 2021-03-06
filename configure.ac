dnl Process this file with autoconf to produce a configure script
AC_INIT([osmo-pcap],
	m4_esyscmd([./git-version-gen .tarball-version]),
	[openbsc-devel@lists.openbsc.org])

AM_INIT_AUTOMAKE([dist-bzip2])
AC_CONFIG_TESTDIR(tests)

dnl kernel style compile messages
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

dnl checks for programs
AC_PROG_MAKE_SET
AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_RANLIB

dnl checks for header files
AC_HEADER_STDC

AC_ARG_ENABLE([external_tests],
		AC_HELP_STRING([--enable-external-tests],
				[Include the VTY tests in make check [default=no]]),
		[enable_ext_tests="$enableval"],[enable_ext_tests="no"])
if test "x$enable_ext_tests" = "xyes" ; then
	AM_PATH_PYTHON
	AC_CHECK_PROG(OSMOTESTEXT_CHECK,osmotestvty.py,yes)
	 if test "x$OSMOTESTEXT_CHECK" != "xyes" ; then
		AC_MSG_ERROR([Please install osmocom-python to run the VTY tests.])
	fi
fi
AC_MSG_CHECKING([whether to enable VTY tests])
AC_MSG_RESULT([$enable_ext_tests])
AM_CONDITIONAL(ENABLE_EXT_TESTS, test "x$enable_ext_tests" = "xyes")

dnl Checks for typedefs, structures and compiler characteristics
AC_ARG_WITH([pcap-config],
	AC_HELP_STRING([--with-pcap-config=FILE], [Use given pcap-config]),
	[pcapcfg="$withval" ],
	[AC_PATH_PROG([pcapcfg], [pcap-config], [])])
if test x$pcapcfg = "x"; then
	AC_MSG_ERROR([pcap-config can not be found])
fi

dnl CentOS 5 and Debian 6.0 do not ship with a pcap-config and we somehow
dnl need to support these versions. Once we drop support for these two distros
dnl the below can be simplified again.
AC_ARG_VAR([PCAP_LIBS], [PCAP library files])
AC_ARG_VAR([PCAP_CFLAGS], [PCAP C compiler flags])

if test "x$ac_cv_env_PCAP_LIBS_set" != "xset"; then
	PCAP_LIBS=`$pcapcfg --libs`
fi

if test "x$ac_cv_env_PCAP_CFLAGS_set" != "xset"; then
	PCAP_CFLAGS=`$pcapcfg --cflags`
fi
AC_SUBST([PCAP_LIBS])
AC_SUBST([PCAP_CFLAGS])

dnl checks for libraries
PKG_CHECK_MODULES(LIBOSMOCORE, libosmocore >= 0.3.2)
PKG_CHECK_MODULES(LIBOSMOGSM, libosmogsm >= 0.3.2)
PKG_CHECK_MODULES(LIBOSMOVTY, libosmovty >= 0.3.0)
PKG_CHECK_MODULES(LIBZMQ, libzmq >= 3.2.2)
PKG_CHECK_MODULES(LIBGNUTLS, gnutls)


# Coverage build taken from WebKit's configure.in
AC_MSG_CHECKING([whether to enable code coverage support])
AC_ARG_ENABLE(coverage,
              AC_HELP_STRING([--enable-coverage],
                             [enable code coverage support [default=no]]),
              [],[enable_coverage="no"])
AC_MSG_RESULT([$enable_coverage])
if test "$enable_coverage" = "yes"; then
   COVERAGE_CFLAGS="-ftest-coverage -fprofile-arcs"
   COVERAGE_LDFLAGS="-ftest-coverage -fprofile-arcs"
   AC_SUBST([COVERAGE_CFLAGS])
   AC_SUBST([COVERAGE_LDFLAGS])
fi

AM_CONFIG_HEADER(osmopcapconfig.h)

dnl Generate the output
AC_OUTPUT(
    include/Makefile
    include/osmo-pcap/Makefile
    src/Makefile
    contrib/Makefile
    tests/Makefile
    Makefile)
