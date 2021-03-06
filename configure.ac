AC_PREREQ(2.50)
AC_INIT([libnfs], [1.10.0], [ronniesahlberg@gmail.com])
AC_CONFIG_HEADERS([config.h])
AM_INIT_AUTOMAKE([-Wall foreign subdir-objects])
AC_CANONICAL_HOST
AC_CONFIG_MACRO_DIR([m4])
m4_pattern_allow([AM_PROG_AR])
AM_PROG_AR

# Work around stupid autoconf default cflags. pt 1
SAVE_CFLAGS="x${CFLAGS}"

AC_PROG_CC
AC_PROG_LIBTOOL

AM_PROG_CC_C_O
PKG_PROG_PKG_CONFIG

# Work around stupid autoconf default cflags. pt 2
if test "$SAVE_CFLAGS" = "x"; then
  CFLAGS=""
fi

# We always want 64 bit file offsets
CFLAGS="${CFLAGS} -D_FILE_OFFSET_BITS=64"

#option: utils
AC_ARG_ENABLE([utils],
              [AC_HELP_STRING([--enable-utils],
                              [Build util programs])],
	      [MAYBE_UTILS=""],
	      [MAYBE_UTILS="utils"])
AC_SUBST(MAYBE_UTILS)

#option: examples
AC_ARG_ENABLE([examples],
              [AC_HELP_STRING([--enable-examples],
                              [Build example programs])],
	      [ENABLE_EXAMPLES=$enableval],
	      [ENABLE_EXAMPLES="no"])

# We need popt to compile the examples
if test x$ENABLE_EXAMPLES = xyes; then
AC_MSG_CHECKING(whether libpopt is available)
ac_save_CFLAGS="$CFLAGS"
ac_save_LIBS="$LIBS"
CFLAGS="$CFLAGS $GLIB_CFLAGS"
LIBS="$GLIB_LIBS $LIBS -lpopt"
AC_TRY_RUN([
/*
 * Just see if we can compile/link with popt
 */
#include <popt.h>

int main(int argc, const char *argv[])
{
	struct poptOption popt_options[] = {
		POPT_TABLEEND
	};
	poptGetContext(argv[0], argc, argv, popt_options, POPT_CONTEXT_KEEP_FIRST);

	return 0;
}
], ac_cv_have_popt=yes, ac_cv_have_popt=no,
   [echo $ac_n "compile with POPT. Assuming OK... $ac_c"
    ac_cv_have_popt=yes])
CFLAGS="$ac_save_CFLAGS"
LIBS="$ac_save_LIBS"
if test "$ac_cv_have_popt" = yes ; then
  AC_MSG_RESULT(yes)
  MAYBE_EXAMPLES="examples"
else
  AC_MSG_RESULT(no)
  AC_MSG_NOTICE(You need libpopt to compile the sample libnfs clients.)
  AC_MSG_NOTICE(Only the library will be compiled and installed.)
fi
fi

AC_SUBST(MAYBE_EXAMPLES)

AC_ARG_ENABLE([werror], [AS_HELP_STRING([--disable-werror],
              [Disables building with -Werror by default])])

if test "$ac_cv_prog_gcc" = yes; then
   WARN_CFLAGS="-Wall -Wshadow -Wno-write-strings -Wstrict-prototypes -Wpointer-arith -Wcast-align -Wno-strict-aliasing"
   if test "x$enable_werror" != "xno"; then
       WARN_CFLAGS="$WARN_CFLAGS -Werror"
   fi
fi
AC_SUBST(WARN_CFLAGS)

LIBSOCKET=
SYS=

case $host in
  *solaris*)
    AC_CHECK_HEADERS([sys/filio.h])
    AC_CHECK_HEADERS([sys/sockio.h])
    AC_CHECK_LIB([socket], [main], , [AC_MSG_ERROR([Can not find required library])])
    AC_CHECK_LIB([nsl],    [main], , [AC_MSG_ERROR([Can not find required library])])
    ;;
  *mingw32* | *cygwin* | *wince* | *mingwce*)
    LIBSOCKET='-lws2_32'
    SYS=mingw32
    ;;
  *)
    ;;
esac
AM_CONDITIONAL(HAVE_WIN32,   test "${SYS}" = "mingw32")
AC_SUBST([LIBSOCKET])

# check for poll.h
dnl Check for poll.h
AC_CHECK_HEADERS([poll.h])

# check for unistd.h
dnl Check for unistd.h
AC_CHECK_HEADERS([unistd.h])

# check for netdb.h
dnl Check for netdb.h
AC_CHECK_HEADERS([netdb.h])

# check for utime.h
dnl Check for utime.h
AC_CHECK_HEADERS([utime.h])

# check for net/if.h
dnl Check for net/if.h
AC_CHECK_HEADERS([net/if.h])

# check for sys/time.h
dnl Check for sys/time.h
AC_CHECK_HEADERS([sys/time.h])

# check for sys/ioctl.h
dnl Check for sys/ioctl.h
AC_CHECK_HEADERS([sys/ioctl.h])

# check for sys/vfs.h
dnl Check for sys/vfs.h
AC_CHECK_HEADERS([sys/vfs.h])

# check for sys/statvfs.h
dnl Check for sys/statvfs.h
AC_CHECK_HEADERS([sys/statvfs.h])

# check for sys/socket.h
dnl Check for sys/socket.h
AC_CHECK_HEADERS([sys/socket.h])

# check for netinet/tcp.h
dnl Check for netinet/tcp.h
AC_CHECK_HEADERS([netinet/tcp.h])

# check for netinet/in.h
dnl Check for netinet/in.h
AC_CHECK_HEADERS([netinet/in.h])

# check for arpa/inet.h
dnl Check for arpa/inet.h
AC_CHECK_HEADERS([arpa/inet.h])

# check for SA_LEN
dnl Check if sockaddr data structure includes a "sa_len"
AC_CHECK_MEMBER([struct sockaddr.sa_len],
                [ AC_DEFINE(HAVE_SOCKADDR_LEN,1,[Whether sockaddr struct has sa_len]) ],
                [],
                [
#include <sys/types.h>
#include <sys/socket.h>
])

# check for sockaddr_storage
dnl Check if sockaddr structure includes a "ss_family"
AC_CHECK_MEMBER([struct sockaddr_storage.ss_family],
                [ AC_DEFINE(HAVE_SOCKADDR_STORAGE,1,[Whether we have sockaddr_Storage]) ],
                [],
                [
#include <sys/types.h>
#include <sys/socket.h>
])

AC_CHECK_MEMBERS([struct stat.st_mtim.tv_nsec])

# check where makedev is defined
AC_HEADER_MAJOR

#output
AC_CONFIG_FILES([Makefile]
                [doc/Makefile]
                [include/Makefile]
                [lib/Makefile]
                [mount/Makefile]
                [nfs/Makefile]
                [nlm/Makefile]
                [nsm/Makefile]
                [portmap/Makefile]
                [rquota/Makefile]
                [utils/Makefile]
                [examples/Makefile]
               )

AC_OUTPUT([libnfs.pc])
