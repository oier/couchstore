# libcouchstore
# Copyright (C) 2010, 2011 Couchbase, Inc
# All rights reserved.
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.
#
AC_PREREQ(2.59)
m4_include([m4/version.m4])
AC_INIT(libcouchstore, VERSION_NUMBER, support@couchbase.com)
AC_CONFIG_SRCDIR([src/couch_db.cc])
AC_CONFIG_AUX_DIR(config)
AC_USE_SYSTEM_EXTENSIONS
AM_INIT_AUTOMAKE(subdir-objects)
AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_CXX
AC_DISABLE_STATIC
AC_PROG_LIBTOOL
AM_CONFIG_HEADER([config.h])
AC_CONFIG_MACRO_DIR([m4])

# the malloc tests seems to be broken for cross compilation.. ignore them
ac_cv_func_malloc_0_nonnull=yes
ac_cv_func_realloc_0_nonnull=yes

COUCHBASE_GENERIC_COMPILER(c99)

PANDORA_HAVE_LIBLUA

AC_SEARCH_LIBS(floor, m)
AC_SEARCH_LIBS(pow, m)
AC_SEARCH_LIBS(fabs, m)

AC_PATH_PROG([ERL], [erl])
AS_IF([test x${ERL} = x], [
    AC_MSG_ERROR([Could not find the erl executable. Is Erlang installed?])
    ])

ERL_INTERFACE_DIR_INCLUDE="-I`${ERL} -eval 'io:put_chars(code:lib_dir(erl_interface) ++ "/include"), erlang:halt().' -noshell -noinput`"
AC_SUBST(ERL_INTERFACE_DIR_INCLUDE)
ERL_INTERFACE_DIR_LIB="-L`${ERL} -eval 'io:put_chars(code:lib_dir(erl_interface) ++ "/lib"), erlang:halt().' -noshell -noinput`"
AC_SUBST(ERL_INTERFACE_DIR_LIB)


AC_ARG_WITH([docs],
  [AS_HELP_STRING([--with-docs],
    [Generate documentation (yes|no) @<:@default=yes@:>@])],
  [with_docs=$withval],
  [with_docs=yes])

AC_PATH_PROG(POD2MAN, pod2man, missing)
AS_IF([test "$with_docs" = "yes" -a "$POD2MAN" = "missing" ],
      [ AC_MSG_ERROR([pod2man is required for building docs])])
AM_CONDITIONAL(BUILD_DOCS, test "$with_docs" = "yes")

AC_CHECK_HEADERS_ONCE([snappy-c.h])

dnl Check that we're able to find a usable libsnappy
AC_CACHE_CHECK([for libsnappy], [ac_cv_have_libsnappy],
  [ saved_libs="$LIBS"
    LIBS=-lsnappy
    AC_TRY_LINK([
      #include <snappy-c.h>
      #include <stddef.h>
            ],[
      size_t sizetest = snappy_max_compressed_length(10);
            ],[
      ac_cv_have_libsnappy="yes"
            ], [
      ac_cv_have_libsnappy="no"
      ])
    LIBS="$saved_libs"
  ])

AS_IF([test "x$ac_cv_header_snappy_c_h" != "xyes"],
      [AC_MSG_ERROR(Failed to locate snappy-c.h)])
AS_IF([test "x${ac_cv_have_libsnappy}" != "xyes"],
      [AC_MSG_ERROR(Failed to locate libsnappy)])
#
# To ensure that we don't break applications in mysterious ways we need
# to version our library. For a detailed description, check out:
# http://www.gnu.org/software/libtool/manual/libtool.html#Libtool-versioning
#
LIBCOUCHSTORE_API_CURRENT=1
LIBCOUCHSTORE_API_REVISION=0
LIBCOUCHSTORE_API_AGE=0
AC_SUBST(LIBCOUCHSTORE_API_CURRENT)
AC_SUBST(LIBCOUCHSTORE_API_REVISION)
AC_SUBST(LIBCOUCHSTORE_API_AGE)
dnl ----------------------------------------------------------------------------

AC_CONFIG_FILES(Makefile libcouchstore.pc)
AC_OUTPUT
AS_IF(test "$with_docs" = "no",
  [
    echo "*****"
    echo "*"
    echo "*  WARNING: You are not generating any documentation."
    echo "*           Please don't ship libcouchstore to an end user"
    echo "*           without documentation..."
    echo "*"
    echo "*****"
  ])
