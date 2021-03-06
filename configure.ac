dnl If not 1, append datestamp to the version number
m4_define(empathy_released, 0)

m4_define([empathy_major_version], [2])
m4_define([empathy_minor_version], [30])
m4_define([empathy_micro_version], [1])
m4_define([empathy_nano_version], [0])

dnl Display the nano_version only if it's not '0'
m4_define([empathy_base_version],
          [empathy_major_version.empathy_minor_version.empathy_micro_version])
m4_define([empathy_full_version],
          [m4_if(empathy_nano_version, 0, [empathy_base_version],
              [empathy_base_version].[empathy_nano_version])])

m4_define(empathy_maybe_datestamp,
          m4_esyscmd([if test x]empathy_released[ != x1; then date +.%Y%m%d | tr -d '\n\r'; fi]))

m4_define(empathy_version, empathy_full_version[]empathy_maybe_datestamp)


AC_INIT(Empathy, empathy_version, http://bugzilla.gnome.org/browse.cgi?product=empathy)


AC_PREREQ(2.59)
AC_COPYRIGHT([
  Copyright (C) 2003-2007 Imendio AB
  Copyright (C) 2007-2008 Collabora Ltd.
])

# Minimal version required
GLIB_REQUIRED=2.22.0
GTK_REQUIRED=2.18.0
GCONF_REQUIRED=1.2.0
TELEPATHY_GLIB_REQUIRED=0.11.1
TELEPATHY_LOGGER=0.1.1
ENCHANT_REQUIRED=1.2.0
ISO_CODES_REQUIRED=0.35
LIBNOTIFY_REQUIRED=0.4.4
LIBCANBERRA_GTK_REQUIRED=0.4
LIBCHAMPLAIN_REQUIRED=0.4
LIBCHAMPLAIN_GTK_REQUIRED=0.4
CLUTTER_GTK_REQUIRED=0.10
GEOCLUE_REQUIRED=0.11
WEBKIT_REQUIRED=1.1.15
KEYRING_REQUIRED=2.22
NETWORK_MANAGER_REQUIRED=0.7.0
NAUTILUS_SENDTO_REQUIRED=2.28.1

# Use --enable-maintainer-mode to disabled deprecated symbols
GNOME_MAINTAINER_MODE_DEFINES

# Warning if GLib/GDK/GTK headers are included
AC_DEFINE(G_DISABLE_SINGLE_INCLUDES, [], [Disable single includes for GLib])
AC_DEFINE(GDK_PIXBUF_DISABLE_SINGLE_INCLUDES, [], [Disable single includes for GDK pixbuf])
AC_DEFINE(GTK_DISABLE_SINGLE_INCLUDES, [], [Disable single includes for GTK])

AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_SRCDIR([configure.ac])
AC_CONFIG_AUX_DIR(.)

GNOME_COMMON_INIT

AM_INIT_AUTOMAKE(1.9 dist-bzip2 no-define -Wno-portability)

# Support silent build rules, requires at least automake-1.11. Disable
# by either passing --disable-silent-rules to configure or passing V=1
# to make
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

AC_ISC_POSIX
AC_PROG_CC
AC_HEADER_STDC
AM_PROG_LIBTOOL
AM_PROG_MKDIR_P
AM_PATH_GLIB_2_0
AC_PATH_XTRA
IT_PROG_INTLTOOL([0.35.0])
GNOME_DOC_INIT([0.17.3])
IDT_COMPILE_WARNINGS
AC_PATH_PROG(DBUS_BINDING_TOOL, dbus-binding-tool)
AC_PATH_PROG(GCONFTOOL, gconftool-2)
AM_GCONF_SOURCE_2
GLIB_GENMARSHAL=`$PKG_CONFIG glib-2.0 --variable=glib_genmarshal`
AC_SUBST(GLIB_GENMARSHAL)
CONTROL_CENTER_EXTENSIONDIR=`$PKG_CONFIG --variable=extensiondir libgnome-control-center-extension`
AC_SUBST(CONTROL_CENTER_EXTENSIONDIR)

AC_CHECK_PROGS([XSLTPROC], [xsltproc])
if test -z "$XSLTPROC"; then
  AC_MSG_ERROR([xsltproc (from libxslt) is required])
fi
AM_PATH_PYTHON([2.3])

EMPATHY_ARG_VALGRIND


# -----------------------------------------------------------
# Error flags
# -----------------------------------------------------------
AS_COMPILER_FLAG(-Wall, ERROR_CFLAGS="-Wall", ERROR_CFLAGS="")
AS_COMPILER_FLAG(-Werror, werror=yes, werror=no)

AC_ARG_ENABLE(Werror,
  AC_HELP_STRING([--disable-Werror],[compile without -Werror (normally enabled in development builds)]),
    werror=$enableval, :)

AS_COMPILER_FLAG(-Wextra, wextra=yes, wextra=no)
AS_COMPILER_FLAG(-Wno-missing-field-initializers,
        wno_missing_field_initializers=yes,
        wno_missing_field_initializers=no)
AS_COMPILER_FLAG(-Wno-unused-parameter,
        wno_unused_parameter=yes,
        wno_unused_parameter=no)

ifelse(empathy_released, 1, [],
    [
        if test x$werror = xyes; then
            ERROR_CFLAGS="$ERROR_CFLAGS -Werror"
        fi
        if test x$wextra = xyes -a \
            x$wno_missing_field_initializers = xyes -a \
            x$wno_unused_parameter = xyes; then
            ERROR_CFLAGS="$ERROR_CFLAGS -Wextra -Wno-missing-field-initializers -Wno-unused-parameter"
        fi
    ])

AS_COMPILER_FLAG(-Wdeclaration-after-statement, ERROR_CFLAGS="$ERROR_CFLAGS -Wdeclaration-after-statement")
AS_COMPILER_FLAG(-Wshadow, ERROR_CFLAGS="$ERROR_CFLAGS -Wshadow")
AS_COMPILER_FLAG(-Wmissing-prototypes, ERROR_CFLAGS="$ERROR_CFLAGS -Wmissing-prototypes")
AS_COMPILER_FLAG(-Wmissing-declarations, ERROR_CFLAGS="$ERROR_CFLAGS -Wmissing-declarations")
# Disabled because some GTK+ headers (like gtkitemfactory.h) are bugged :(
#AS_COMPILER_FLAG(-Wstrict-prototypes, ERROR_CFLAGS="$ERROR_CFLAGS -Wstrict-prototypes")

AC_SUBST(ERROR_CFLAGS)

# -----------------------------------------------------------
# Enable TPL
# -----------------------------------------------------------

# It needs to be defined before PKG_CHECK_MODULES calls
AC_ARG_ENABLE(tpl,
  AC_HELP_STRING([--enable-tpl],[enable telepathy-logger code and disable the
    empathy logger]), enable_tpl=$enableval, enable_tpl=no )
AM_CONDITIONAL(ENABLE_TPL, test "x$enable_tpl" = "xyes")

if test x${enable_tpl} = xyes; then
  AC_DEFINE(ENABLE_TPL, [], [Enable TPL code])
fi

if test "x$enable_tpl" = "xyes"; then
  PKG_CHECK_MODULES(TPL,
  [
     telepathy-logger = $TELEPATHY_LOGGER
  ])
  AC_SUBST(TPL_CFLAGS)
  AC_SUBST(TPL_LIBS)
fi


# -----------------------------------------------------------
# Pkg-Config dependency checks
# -----------------------------------------------------------

PKG_CHECK_MODULES(LIBEMPATHY,
[
   glib-2.0 >= $GLIB_REQUIRED
   gobject-2.0
   gio-2.0 >= $GLIB_REQUIRED
   gio-unix-2.0 >= $GLIB_REQUIRED
   libxml-2.0
   telepathy-glib >= $TELEPATHY_GLIB_REQUIRED
   telepathy-farsight
   farsight2-0.10
   gstreamer-0.10
])

PKG_CHECK_MODULES(LIBEMPATHYGTK,
[
   glib-2.0 >= $GLIB_REQUIRED
   gobject-2.0
   gio-2.0 >= $GLIB_REQUIRED
   gconf-2.0 >= $GCONF_REQUIRED
   x11
   gtk+-2.0 >= $GTK_REQUIRED
   libcanberra-gtk >= $LIBCANBERRA_GTK_REQUIRED
   telepathy-glib >= $TELEPATHY_GLIB_REQUIRED
   farsight2-0.10
   gstreamer-0.10
   gstreamer-interfaces-0.10
])

PKG_CHECK_MODULES(EMPATHY,
[
   glib-2.0 >= $GLIB_REQUIRED
   gobject-2.0
   gio-2.0 >= $GLIB_REQUIRED
   gdk-x11-2.0
   gtk+-2.0 >= $GTK_REQUIRED
   libebook-1.2
   dbus-glib-1
   telepathy-glib >= $TELEPATHY_GLIB_REQUIRED
   telepathy-farsight
   farsight2-0.10
   gstreamer-0.10
   unique-1.0
   gnome-keyring-1 >= $KEYRING_REQUIRED
])

PKG_CHECK_MODULES(LIBEMPATHY_ACCOUNTS_PANEL,
[
   glib-2.0 >= $GLIB_REQUIRED
   gobject-2.0
   gio-2.0 >= $GLIB_REQUIRED
   gdk-x11-2.0
   gtk+-2.0 >= $GTK_REQUIRED
   libebook-1.2
   dbus-glib-1
   telepathy-glib >= $TELEPATHY_GLIB_REQUIRED
   unique-1.0
   gnome-keyring-1 >= $KEYRING_REQUIRED
])

PKG_CHECK_MODULES(LIBNOTIFY, libnotify >= $LIBNOTIFY_REQUIRED)

# -----------------------------------------------------------
# Enable debug
# -----------------------------------------------------------

AC_ARG_ENABLE(debug,
  AC_HELP_STRING([--disable-debug],[compile without debug code]),
    enable_debug=$enableval, enable_debug=yes )

if test x$enable_debug = xyes; then
  AC_DEFINE(ENABLE_DEBUG, [], [Enable debug code])
fi

# -----------------------------------------------------------
# Language Support
# -----------------------------------------------------------

GETTEXT_PACKAGE=empathy
AC_SUBST(GETTEXT_PACKAGE)
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE,"$GETTEXT_PACKAGE",[Gettext package name])

AM_GLIB_GNU_GETTEXT

# -----------------------------------------------------------
# Connectivity integration
# -----------------------------------------------------------
AC_ARG_WITH(connectivity,
              AS_HELP_STRING([--with-connectivity=@<:@nm/connman/auto/no@:>@],
                             [build with connectivity support]), ,
                             with_connectivity=auto)

if test "x$with_connectivity" = "xno"; then
   have_nm=no
   have_connman=no

elif test "x$with_connectivity" = "xconnman"; then

   PKG_CHECK_MODULES(CONNMAN,
   [
      dbus-glib-1
   ], have_connman="yes", have_connman="no")

   if test "x$have_connman" = "xyes"; then
      AC_DEFINE(HAVE_CONNMAN, 1, [Define if you have connman dependencies])
      have_nm=no
   fi

else

   PKG_CHECK_MODULES(NETWORK_MANAGER,
   [
      libnm_glib >= $NETWORK_MANAGER_REQUIRED
   ], have_nm="yes", have_nm="no")

   if test "x$have_nm" = "xyes"; then
      AC_DEFINE(HAVE_NM, 1, [Define if you have libnm-glib])
      have_connman=no
   else
      PKG_CHECK_MODULES(NETWORK_MANAGER,
      [
          libnm-glib >= $NETWORK_MANAGER_REQUIRED
      ], have_nm="yes", have_nm="no")

      if test "x$have_nm" = "xyes"; then
      AC_DEFINE(HAVE_NM, 1, [Define if you have libnm-glib])
      have_connman=no
      fi
   fi
fi

if test "x$with_connectivity" = "xconnman" -a "x$have_connman" != "xyes"; then
   AC_MSG_ERROR([Couldn't find connman dependencies.])
fi

if test "x$with_connectivity" = "xnm" -a "x$have_nm" != "xyes"; then
   AC_MSG_ERROR([Couldn't find libnm-glib dependencies.])
fi

AM_CONDITIONAL(HAVE_NM, test "x$have_nm" = "xyes")
AM_CONDITIONAL(HAVE_CONNMAN, test "x$have_connman" = "xyes")

# -----------------------------------------------------------
# Webkit
# -----------------------------------------------------------
AC_ARG_ENABLE(webkit,
              AS_HELP_STRING([--enable-webkit=@<:@no/yes/auto@:>@],
                             [build with webkit support]), ,
                             enable_webkit=auto)

if test "x$enable_webkit" != "xno"; then
   PKG_CHECK_MODULES(WEBKIT,
   [
      webkit-1.0 >= $WEBKIT_REQUIRED
   ], have_webkit="yes", have_webkit="no")

   if test "x$have_webkit" = "xyes"; then
      AC_DEFINE(HAVE_WEBKIT, 1, [Define if you have libwebkitgtk])
   fi
else
   have_webkit=no
fi

if test "x$enable_webkit" = "xyes" -a "x$have_webkit" != "xyes"; then
   AC_MSG_ERROR([Couldn't find webkit dependencies.])
fi
AM_CONDITIONAL(HAVE_WEBKIT, test "x$have_webkit" = "xyes")

# -----------------------------------------------------------
# spellchecking checks: enchant and iso-codes
# -----------------------------------------------------------
AC_ARG_ENABLE(spell,
              AS_HELP_STRING([--enable-spell=@<:@no/yes/auto@:>@],
                             [Enable spell checking]), ,
                             enable_spell=auto)

if test "x$enable_spell" != "xno"; then
   PKG_CHECK_MODULES(ENCHANT,
   [
      enchant >= $ENCHANT_REQUIRED,
      iso-codes >= $ISO_CODES_REQUIRED
   ], have_enchant="yes", have_enchant="no")

   if test "x$have_enchant" = "xyes"; then
      AC_MSG_CHECKING([whether iso-codes has iso-639 and iso-3166 domains])
      if $PKG_CONFIG --variable=domains iso-codes | grep 639 > /dev/null && \
         $PKG_CONFIG --variable=domains iso-codes | grep 3166 > /dev/null ; then
            AC_DEFINE_UNQUOTED(ISO_CODES_PREFIX, ["`$PKG_CONFIG --variable=prefix iso-codes`"], [ISO codes prefix])
            AC_DEFINE(HAVE_ENCHANT, [], [Define if you have libenchant])
            result=yes
      else
            result=no
            have_enchant="no"
      fi
      AC_MSG_RESULT([$result])
   fi
else
   have_enchant=no
fi

if test "x$enable_spell" = "xyes" -a "x$have_enchant" != "xyes"; then
   AC_MSG_ERROR([Couldn't find spell dependencies.])
fi

AM_CONDITIONAL(HAVE_ENCHANT, test "x$have_enchant" = "xyes")

# -----------------------------------------------------------
# Map view checks: libchamplain
# -----------------------------------------------------------
AC_ARG_ENABLE(map,
              AS_HELP_STRING([--enable-map=@<:@no/yes/auto@:>@],
                             [Enable map view]), ,
                             enable_map=auto)

if test "x$enable_map" != "xno"; then
   PKG_CHECK_MODULES(LIBCHAMPLAIN,
   [
      champlain-0.4 >= $LIBCHAMPLAIN_REQUIRED,
      champlain-gtk-0.4 >= $LIBCHAMPLAIN_GTK_REQUIRED
      clutter-gtk-0.10 >= $CLUTTER_GTK_REQUIRED
   ], have_libchamplain="yes", have_libchamplain="no")

   if test "x$have_libchamplain" = "xyes"; then
      AC_DEFINE(HAVE_LIBCHAMPLAIN, 1, [Define if you have libchamplain])
   fi
else
   have_libchamplain=no
fi

if test "x$enable_map" = "xyes" -a "x$have_libchamplain" != "xyes"; then
   AC_MSG_ERROR([Couldn't find map view dependencies.])
fi

AM_CONDITIONAL(HAVE_LIBCHAMPLAIN, test "x$have_libchamplain" = "xyes")

# -----------------------------------------------------------
# location checks: geoclue
# -----------------------------------------------------------
AC_ARG_ENABLE(location,
              AS_HELP_STRING([--enable-location=@<:@no/yes/auto@:>@],
                             [Enable location awareness]), ,
                             enable_location=auto)

if test "x$enable_location" != "xno"; then
   PKG_CHECK_MODULES(GEOCLUE,
   [
      geoclue >= $GEOCLUE_REQUIRED
   ], have_geoclue="yes", have_geoclue="no")

   if test "x$have_geoclue" = "xyes"; then
      AC_DEFINE(HAVE_GEOCLUE, 1, [Define if you have geoclue])
   fi
else
   have_geoclue="no"
fi

if test "x$enable_location" = "xyes" -a "x$have_geoclue" != "xyes"; then
   AC_MSG_ERROR([Couldn't find location dependencies.])
fi

AM_CONDITIONAL(HAVE_GEOCLUE, test "x$have_geoclue" = "xyes")
AC_SUBST(GEOCLUE_CFLAGS)
AC_SUBST(GEOCLUE_LIBS)

# -----------------------------------------------------------
# meego widgets support
# -----------------------------------------------------------
AC_ARG_ENABLE(meego,
              AS_HELP_STRING([--enable-meego=@<:no/yes@:>@],
                             [Enable meego widgets]), ,
                             enable_meego=no)

if test "x$enable_meego" != "xno"; then
    PKG_CHECK_MODULES(MEEGO,
    [
       mx-gtk-1.0
    ], have_meego="yes", have_meego="no")

    if test "x$have_meego" = "xyes"; then
       AC_DEFINE(HAVE_MEEGO, 1, [Define if you have meego])
       chain_enable_favourite_contacts="yes"
    fi
else
   have_meego="no"
fi

if test "x$enable_meego" = "xyes" -a "x$have_meego" != "xyes"; then
   AC_MSG_ERROR([Couldn't find meego dependencies.])
fi

AM_CONDITIONAL(HAVE_MEEGO, test "x$have_meego" = "xyes")
AC_SUBST(MEEGO_CFLAGS)
AC_SUBST(MEEGO_LIBS)

# -----------------------------------------------------------
# favourite contacts support
# -----------------------------------------------------------
AC_ARG_ENABLE(favourite_contacts,
              AS_HELP_STRING([--enable-favourite-contacts=@<:no/yes@:>@],
                             [Enable favourite contacts]), ,
                             enable_favourite_contacts=no)

if test "x$enable_favourite_contacts" != "xno" -o "x$chain_enable_favourite_contacts" == "xyes"; then
    PKG_CHECK_MODULES(TELEPATHY_LOGGER,
    [
       telepathy-logger
    ], have_telepathy_logger="yes", have_telepathy_logger="no")

    if test "x$have_telepathy_logger" = "xyes"; then
       have_favourite_contacts="yes"
       AC_DEFINE(HAVE_FAVOURITE_CONTACTS, 1, [Define if you want favourite contacts support])
    fi
else
   have_favourite_contacts="no"
fi

if test "x$enable_favourite_contacts" = "xyes" -a "x$have_favourite_contacts" != "xyes"; then
   AC_MSG_ERROR([Couldn't find favourite contacts dependencies.])
fi

AM_CONDITIONAL(HAVE_FAVOURITE_CONTACTS, test "x$have_telepathy_logger" = "xyes")

# -----------------------------------------------------------
# nautilus-sendto
# -----------------------------------------------------------
AC_ARG_ENABLE(nautilus-sendto,
              AS_HELP_STRING([--enable-nautilus-sendto=@<:@no/yes/auto@:>@],
                             [build nautilus-sendto plugin]), ,
                             enable_nautilus_sendto=auto)

if test "x$enable_nautilus_sendto" != "xno"; then
   PKG_CHECK_MODULES(NST,
   [
      nautilus-sendto >= $NAUTILUS_SENDTO_REQUIRED
   ], have_nst="yes", have_nst="no")
else
   have_nst=no
fi

if test "x$enable_nautilus_sendto" = "xyes" -a "x$have_nst" != "xyes"; then
   AC_MSG_ERROR([Couldn't find nautilus-sendto dependencies.])
fi

AM_CONDITIONAL(HAVE_NST, test "x$have_nst" = "xyes")

# -----------------------------------------------------------
# new, single-window control center
# -----------------------------------------------------------
AC_ARG_ENABLE(control_center_embedding,
              AS_HELP_STRING([--enable-control-center-embedding=@<:@no/yes/auto@:>@],
                             [Enable support for single-window control center]),
                             , enable_control_center_embedding=auto)

if test "x$enable_control_center_embedding" != "xno"; then
   PKG_CHECK_MODULES(CONTROL_CENTER_EMBEDDING,
   [
      libgnome-control-center-extension
   ], have_control_center_embedding="yes", have_control_center_embedding="no")

   if test "x$have_control_center_embedding" = "xyes"; then
      AC_DEFINE(HAVE_CONTROL_CENTER_EMBEDDING, 1, [Define if you have the single-window control center])
   fi
else
   have_control_center_embedding="no"
fi

if test "x$enable_control_center_embedding" = "xyes" -a "x$have_control_center_embedding" != "xyes"; then
   AC_MSG_ERROR([Couldn't find single-window control center dependencies.])
fi

AM_CONDITIONAL(HAVE_CONTROL_CENTER_EMBEDDING, test "x$have_control_center_embedding" = "xyes")
AC_SUBST(CONTROL_CENTER_EMBEDDING_CFLAGS)
AC_SUBST(CONTROL_CENTER_EMBEDDING_LIBS)

# -----------------------------------------------------------
# Coding style checks
# -----------------------------------------------------------
AC_ARG_ENABLE(coding-style-checks,
  AC_HELP_STRING([--disable-coding-style-checks],
                 [don't check coding style using grep]),
    [ENABLE_CODING_STYLE_CHECKS=$enableval], [ENABLE_CODING_STYLE_CHECKS=yes])

AC_SUBST([ENABLE_CODING_STYLE_CHECKS])
# -----------------------------------------------------------

AC_OUTPUT([
   Makefile
   data/Makefile
   data/empathy.desktop.in
   data/empathy-accounts.desktop.in
   data/icons/Makefile
   extensions/Makefile
   po/Makefile.in
   libempathy/Makefile
   libempathy-gtk/Makefile
   src/Makefile
   nautilus-sendto-plugin/Makefile
   help/Makefile
   tests/Makefile
   tests/interactive/Makefile
   tests/xml/Makefile
   tools/Makefile
])

echo "
Configure summary:

	Compiler....................:  ${CC}
	Compiler Flags..............:  ${CFLAGS} ${ERROR_CFLAGS}
	Prefix......................:  ${prefix}
	Coding style checks.........:  ${ENABLE_CODING_STYLE_CHECKS}

    Features:
	Spell checking (enchant)....:  ${have_enchant}
	Display maps (libchamplain).:  ${have_libchamplain}
	Location awareness (Geoclue):  ${have_geoclue}
	Adium themes (Webkit).......:  ${have_webkit}
	Meego widgets ..............:  ${have_meego}
	Favourite contacts .........:  ${have_favourite_contacts}
	Control center embedding....:  ${have_control_center_embedding}

    Logging:
        Telepathy Logger............:  ${enable_tpl}

    Connectivity:
	NetworkManager integration..:  ${have_nm}
	ConnMan integration.........:  ${have_connman}

    Extras:
	Nautilus-sendto plugin......:  ${have_nst}
"
