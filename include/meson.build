version_split = meson.project_version().split('.')
major = version_split[0].to_int()
minor = version_split[1].to_int()
patch = version_split[2].to_int()
if version_split.length() == 4
    subpatch = version_split[3].to_int()
else
    subpatch = 0
endif

release = major * 10000000 + minor * 100000 + patch * 1000 + subpatch

dri_dep = dependency('dri', required: build_dri2 or build_dri3)

conf_data = configuration_data()
conf_data.set('_DIX_CONFIG_H_', '1')

conf_data.set('HAVE_TYPEOF', cc.compiles('''
    int foo(int bar) { typeof(bar) baz = 1; return baz; }
''',
    name: 'typeof()'))

conf_data.set('MONOTONIC_CLOCK', cc.compiles('''
    #define _POSIX_C_SOURCE 200112L
    #include <time.h>
    #include <unistd.h>
    #ifndef CLOCK_MONOTONIC
    #error CLOCK_MONOTONIC not defined
    #endif
''',
    name: 'CLOCK_MONOTONIC'))

#conf_data.set('XSERVER_DTRACE', '1') # XXX

if host_machine.endian() == 'little'
    conf_data.set('X_BYTE_ORDER', 'X_LITTLE_ENDIAN')
else
    conf_data.set('X_BYTE_ORDER', 'X_BIG_ENDIAN')
endif

glx_align64 = []
if cc.sizeof('unsigned long') == 8
   conf_data.set('_XSERVER64', '1')
   glx_align64 = '-D__GLX_ALIGN64'
endif

conf_data.set('_GNU_SOURCE', '1')
# XXX: NO_LOCAL_CLIENT_CRED

# autoconf checks for /dev/xf86 here, but the test should be based on
# the target, not the build system.  Could we get rid of this and just
# ifdef for openbsd?
conf_data.set('HAS_APERTURE_DRV', host_machine.system() == 'openbsd')

# XXX: USE_ALPHA_PIO and other bsd bits
# XXX: FALLBACK_INPUT_DRIVER
# XXX: BUNDLE_ID_PREFIX
# XXX: HAVE_LIBDISPATCH
conf_data.set_quoted('OSNAME', 'Linux') # XXX
conf_data.set('HAVE_INPUTTHREAD', '1') # XXX
conf_data.set('HAVE_PTHREAD_SETNAME_NP_WITHOUT_TID', '1') # XXX
conf_data.set('HAVE_LIBBSD', libbsd_dep.found())
# XXX: HAVE_SYSTEMD_DAEMON
# XXX: HAVE_LIBUDEV
conf_data.set('CONFIG_UDEV', build_udev)
conf_data.set('CONFIG_UDEV_KMS', build_udev)
conf_data.set('HAVE_DBUS', build_dbus)
conf_data.set('CONFIG_HAL', build_hal)
conf_data.set('SYSTEMD_LOGIND', build_systemd_logind)
conf_data.set('NEED_DBUS', build_systemd_logind or build_hal)
conf_data.set('CONFIG_WSCONS', host_machine.system() == 'openbsd')

# XXX: SHMDIR is weird in autoconf, probing the build system for
# various tmp directories.  Could we replace it with C code at runtime
# that just uses whatever directory works?
conf_data.set_quoted('SHMDIR', '/tmp')

conf_data.set('HAVE_XSHMFENCE', xshmfence_dep.found())
conf_data.set('WITH_LIBDRM', libdrm_dep.found())
conf_data.set('GLAMOR_HAS_EGL_QUERY_DMABUF',
              epoxy_dep.found() and epoxy_dep.version().version_compare('>= 1.4.4'))
conf_data.set('GLXEXT', build_glx)
conf_data.set('GLAMOR', build_glamor)
conf_data.set('GLAMOR_HAS_GBM', gbm_dep.found())
conf_data.set('GLAMOR_HAS_GBM_LINEAR',
              build_glamor and gbm_dep.found() and gbm_dep.version().version_compare('>= 10.6'))
conf_data.set('GBM_BO_WITH_MODIFIERS',
              build_glamor and gbm_dep.found() and gbm_dep.version().version_compare('>= 17.1'))
conf_data.set('GLAMOR_HAS_GBM_MAP',
              build_glamor and cc.has_function('gbm_bo_map', dependencies : gbm_dep) ? '1' : false)
conf_data.set('GLAMOR_HAS_GL_FENCE',
              build_glamor and cc.has_function('glFenceSync', dependencies : dependency('glesv2', required : false)) ? '1' : false)

conf_data.set('GLES_USE_HIGHP', get_option('gles-highp') ? '1' : false)

conf_data.set('MODESETTING_WITH_RGA', build_modesetting and rga_dep.found() ? '1' : false)

conf_data.set_quoted('SERVER_MISC_CONFIG_PATH', serverconfigdir)
conf_data.set_quoted('PROJECTROOT', get_option('prefix'))
conf_data.set_quoted('SYSCONFDIR', join_paths(get_option('prefix'), get_option('sysconfdir')))
conf_data.set_quoted('SUID_WRAPPER_DIR', join_paths(get_option('prefix'), get_option('libexecdir')))
conf_data.set_quoted('COMPILEDDEFAULTFONTPATH', default_font_path)

conf_data.set('XORG_VERSION_CURRENT', release)

conf_data.set('HASXDMAUTH', has_xdm_auth)
conf_data.set('SECURE_RPC', get_option('secure-rpc'))

conf_data.set('HAVE_DLFCN_H', cc.has_header('dlfcn.h'))
conf_data.set('HAVE_EXECINFO_H', cc.has_header('execinfo.h'))
conf_data.set('HAVE_FCNTL_H', cc.has_header('fcntl.h'))
conf_data.set('HAVE_FNMATCH_H', cc.has_header('fnmatch.h'))
conf_data.set('HAVE_LINUX_AGPGART_H', cc.has_header('linux/agpgart.h'))
conf_data.set('HAVE_STDLIB_H', cc.has_header('stdlib.h'))
conf_data.set('HAVE_STRING_H', cc.has_header('string.h'))
conf_data.set('HAVE_STRINGS_H', cc.has_header('strings.h'))
conf_data.set('HAVE_STROPTS_H', cc.has_header('stropts.h'))
conf_data.set('HAVE_SYS_AGPGART_H', cc.has_header('sys/agpgart.h'))
conf_data.set('HAVE_SYS_AGPIO_H', cc.has_header('sys/agpio.h'))
conf_data.set('HAVE_SYS_UTSNAME_H', cc.has_header('sys/utsname.h'))
conf_data.set('HAVE_SYS_SYSMACROS_H', cc.has_header('sys/sysmacros.h'))
conf_data.set('HAVE_UNISTD_H', cc.has_header('unistd.h'))

conf_data.set('HAVE_ARC4RANDOM_BUF', cc.has_function('arc4random_buf', dependencies: libbsd_dep))
conf_data.set('HAVE_BACKTRACE', cc.has_function('backtrace'))
conf_data.set('HAVE_CBRT', cc.has_function('cbrt'))
conf_data.set('HAVE_EPOLL_CREATE1', cc.has_function('epoll_create1'))
conf_data.set('HAVE_GETUID', cc.has_function('getuid'))
conf_data.set('HAVE_GETEUID', cc.has_function('geteuid'))
conf_data.set('HAVE_ISSETUGID', cc.has_function('issetugid'))
conf_data.set('HAVE_GETIFADDRS', cc.has_function('getifaddrs'))
conf_data.set('HAVE_GETPEEREID', cc.has_function('getpeereid'))
conf_data.set('HAVE_GETPEERUCRED', cc.has_function('getpeerucred'))
conf_data.set('HAVE_GETPROGNAME', cc.has_function('getprogname'))
conf_data.set('HAVE_GETZONEID', cc.has_function('getzoneid'))
conf_data.set('HAVE_MKOSTEMP', cc.has_function('mkostemp'))
conf_data.set('HAVE_MMAP', cc.has_function('mmap'))
conf_data.set('HAVE_POLL', cc.has_function('poll'))
conf_data.set('HAVE_POLLSET_CREATE', cc.has_function('pollset_create'))
conf_data.set('HAVE_POSIX_FALLOCATE', cc.has_function('posix_fallocate'))
conf_data.set('HAVE_PORT_CREATE', cc.has_function('port_create'))
conf_data.set('HAVE_REALLOCARRAY', cc.has_function('reallocarray', dependencies: libbsd_dep))
conf_data.set('HAVE_SETEUID', cc.has_function('seteuid'))
conf_data.set('HAVE_SETITIMER', cc.has_function('setitimer'))
conf_data.set('HAVE_SHMCTL64', cc.has_function('shmctl64'))
conf_data.set('HAVE_SIGACTION', cc.has_function('sigaction'))
conf_data.set('HAVE_STRCASECMP', cc.has_function('strcasecmp'))
conf_data.set('HAVE_STRCASESTR', cc.has_function('strcasestr'))
conf_data.set('HAVE_STRLCAT', cc.has_function('strlcat', dependencies: libbsd_dep))
conf_data.set('HAVE_STRLCPY', cc.has_function('strlcpy', dependencies: libbsd_dep))
conf_data.set('HAVE_STRNCASECMP', cc.has_function('strncasecmp'))
conf_data.set('HAVE_STRNDUP', cc.has_function('strndup'))
conf_data.set('HAVE_TIMINGSAFE_MEMCMP', cc.has_function('timingsafe_memcmp'))
conf_data.set('HAVE_VASPRINTF', cc.has_function('vasprintf'))
conf_data.set('HAVE_VSNPRINTF', cc.has_function('vsnprintf'))
conf_data.set('HAVE_WALKCONTEXT', cc.has_function('walkcontext'))

conf_data.set('BUSFAULT', conf_data.get('HAVE_SIGACTION'))

# Don't let X dependencies typedef 'pointer'
conf_data.set('_XTYPEDEF_POINTER', '1')
conf_data.set('_XITYPEDEF_POINTER', '1')

conf_data.set('LISTEN_TCP', get_option('listen_tcp'))
conf_data.set('LISTEN_UNIX', get_option('listen_unix'))
conf_data.set('LISTEN_LOCAL', get_option('listen_local'))
# XXX: Configurable?
conf_data.set('XTRANS_SEND_FDS', '1')

conf_data.set('TCPCONN', '1')
conf_data.set('UNIXCONN', '1')
conf_data.set('IPv6', build_ipv6)

conf_data.set('CLIENTIDS', '1') # XXX

conf_data.set('BIGREQS', '1')
conf_data.set('COMPOSITE', '1')
conf_data.set('DAMAGE', '1')
conf_data.set('DBE', '1')
conf_data.set('DGA', build_dga)
conf_data.set('DPMSExtension', build_dpms)
conf_data.set('DRI2', build_dri2)
conf_data.set('DRI3', build_dri3)
conf_data.set_quoted('DRI_DRIVER_PATH', dri_dep.get_pkgconfig_variable('dridriverdir'))
conf_data.set('HAS_SHM', build_mitshm)
conf_data.set('MITSHM', build_mitshm)
conf_data.set('PANORAMIX', build_xinerama)
conf_data.set('PRESENT', '1')
conf_data.set('RANDR', '1')
conf_data.set('RES', build_res)
conf_data.set('RENDER', '1')
conf_data.set('SCREENSAVER', build_screensaver)
conf_data.set('SHAPE', '1')
conf_data.set('XACE', build_xace)
conf_data.set('XCMISC', '1')
conf_data.set('XCSECURITY', build_xsecurity)
conf_data.set('XDMCP', xdmcp_dep.found())
conf_data.set('XF86BIGFONT', build_xf86bigfont)
conf_data.set('XF86DRI', build_dri1)
conf_data.set('XF86VIDMODE', build_xf86vidmode)
conf_data.set('XFIXES', '1')
conf_data.set('XFreeXDGA', build_dga)
conf_data.set('XINERAMA', build_xinerama)
conf_data.set('XINPUT', '1')
conf_data.set('XRECORD', '1')
conf_data.set('XSELINUX', build_xselinux)
conf_data.set('XSYNC', '1')
conf_data.set('XTEST', '1')
conf_data.set('XV', build_xv)
conf_data.set('XvExtension', build_xv)
conf_data.set('XvMCExtension', build_xvmc)

conf_data.set('HAVE_SHA1_IN_LIBNETTLE', '1') # XXX

conf_data.set('HAVE_APM', build_apm or build_acpi)
conf_data.set('HAVE_ACPI', build_acpi)

enable_debugging = get_option('buildtype') == 'debug'
conf_data.set('DEBUG', enable_debugging)

conf_data.set_quoted('XVENDORNAME', get_option('vendor_name'))
conf_data.set_quoted('XVENDORNAMESHORT', get_option('vendor_name_short'))
conf_data.set_quoted('__VENDORDWEBSUPPORT__', get_option('vendor_web'))
conf_data.set_quoted('OSVENDOR', get_option('os_vendor'))
conf_data.set_quoted('BUILDERADDR', get_option('builder_addr'))
conf_data.set_quoted('BUILDERSTRING', get_option('builder_string'))

#
# for xorg-server.h only
#
defines_svr4 = '''#if !defined(SVR4) && !defined(__svr4__) && !defined(__SVR4)
#error "I am not SVR4"
#endif
'''

# BSD specifics
supports_pccons = false
supports_pcvt = false
supports_syscons = false
supports_wscons = false
csrg_based = false

if host_machine.system() == 'freebsd' or host_machine.system() == 'dragonflybsd'
  supports_pccons = true
  supports_pcvt = true
  supports_syscons = true
  csrg_based = true
endif

if host_machine.system() == 'kfreebsd'
  supports_pccons = true
  supports_pcvt = true
  supports_syscons = true
endif

if host_machine.system() == 'netbsd'
  supports_pccons = true
  supports_pcvt = true
  supports_wscons = true
  csrg_based = true
endif

if host_machine.system() == 'openbsd'
  supports_pcvt = true
  supports_wscons = true
  csrg_based = true
endif

conf_data.set('SVR4', cc.compiles(defines_svr4))
conf_data.set_quoted('XKB_DFLT_RULES', get_option('xkb_default_rules'))
conf_data.set('XORGSERVER', build_xorg)
conf_data.set_quoted('XCONFIGFILE', 'xorg.conf')
conf_data.set_quoted('__XSERVERNAME__', 'Xorg')
conf_data.set('WITH_VGAHW', build_vgahw)
conf_data.set('CSRG_BASED', csrg_based)
conf_data.set('PCCONS_SUPPORT', supports_pccons)
conf_data.set('PCVT_SUPPORT', supports_pcvt)
conf_data.set('SYSCONS_SUPPORT', supports_syscons)
conf_data.set('WSCONS_SUPPORT', supports_wscons)
conf_data.set('XSERVER_LIBPCIACCESS', get_option('pciaccess'))
conf_data.set('XSERVER_PLATFORM_BUS', build_udev)

configure_file(output : 'dix-config.h',
               configuration : conf_data)

configure_file(output : 'xorg-server.h',
               input : 'xorg-server.h.meson.in',
               configuration : conf_data,
               install_dir: xorgsdkdir)

version_data = configuration_data()
version_data.set('VENDOR_RELEASE', '@0@'.format(release))
version_data.set_quoted('VENDOR_NAME', get_option('vendor_name'))
version_data.set_quoted('VENDOR_NAME_SHORT', get_option('vendor_name_short'))
version_data.set_quoted('VENDOR_WEB', get_option('vendor_web'))
version_data.set_quoted('VENDOR_MAN_VERSION', 'Version @0@.@1@.@2@'.format(major, minor, patch))
configure_file(output : 'version-config.h',
               configuration : version_data)

xkb_data = configuration_data()

xkb_data.set_quoted('XKB_BIN_DIRECTORY', xkb_bin_dir)
xkb_data.set_quoted('XKB_BASE_DIRECTORY', xkb_dir)
xkb_data.set_quoted('XKB_DFLT_RULES', get_option('xkb_default_rules'))
xkb_data.set_quoted('XKB_DFLT_MODEL', get_option('xkb_default_model'))
xkb_data.set_quoted('XKB_DFLT_LAYOUT', get_option('xkb_default_layout'))
xkb_data.set_quoted('XKB_DFLT_VARIANT', get_option('xkb_default_variant'))
xkb_data.set_quoted('XKB_DFLT_OPTIONS', get_option('xkb_default_options'))
xkb_data.set_quoted('XKM_OUTPUT_DIR', xkb_output_dir)

configure_file(output : 'xkb-config.h',
               configuration : xkb_data)

xorg_data = configuration_data()

xorg_data.set_quoted('XORG_BIN_DIRECTORY', get_option('bindir'))
xorg_data.set('XORG_VERSION_CURRENT', release)
xorg_data.set_quoted('XF86CONFIGFILE', 'xorg.conf')
xorg_data.set_quoted('XCONFIGFILE', 'xorg.conf')
xorg_data.set_quoted('XCONFIGDIR', 'xorg.conf.d')
xorg_data.set_quoted('DEFAULT_XDG_DATA_HOME', '.local/share')
xorg_data.set_quoted('DEFAULT_XDG_DATA_HOME_LOGDIR', 'xorg')
xorg_data.set_quoted('DEFAULT_LOGDIR', log_dir)
xorg_data.set_quoted('DEFAULT_LOGPREFIX', 'Xorg.')
xorg_data.set_quoted('FALLBACK_INPUT_DRIVER', 'libinput')
xorg_data.set_quoted('DEFAULT_MODULE_PATH', join_paths(get_option('prefix'), module_dir))
xorg_data.set_quoted('DEFAULT_LIBRARY_PATH', join_paths(get_option('prefix'), get_option('libdir')))
xorg_data.set_quoted('__XSERVERNAME__', 'Xorg')
xorg_data.set('XSERVER_LIBPCIACCESS', get_option('pciaccess'))
xorg_data.set_quoted('PCI_TXT_IDS_PATH', '')
xorg_data.set('XSERVER_PLATFORM_BUS', build_udev)
xorg_data.set('WSCONS_SUPPORT', host_machine.system() == 'netbsd' or host_machine.system() == 'openbsd')
xorg_data.set('XF86PM', build_apm or build_acpi)

if host_machine.system() == 'freebsd' or host_machine.system() == 'dragonflybsd'
    if host_machine.cpu_family() == 'x86' or host_machine.cpu_family() == 'x86_64'
        xorg_data.set('USE_DEV_IO', true)
    endif
    # XXX: Add link to libio on alpha
elif host_machine.system() == 'netbsd'
    # XXX: USE_ALPHA_PIO
    # XXX: Add link to libi386
    if host_machine.cpu_family() == 'x86' or host_machine.cpu_family() == 'x86_64'
        xorg_data.set('USE_I386_IOPL', true)
    endif
elif host_machine.system() == 'openbsd'
    # XXX: Add link to libi386, libamd64
    if host_machine.cpu_family() == 'x86'
        xorg_data.set('USE_I386_IOPL', true)
    endif
    if host_machine.cpu_family() == 'x86_64'
        xorg_data.set('USE_AMD64_IOPL', true)
    endif
endif

configure_file(output : 'xorg-config.h',
               input : 'xorg-config.h.meson.in',
               configuration : xorg_data)

xwin_data = configuration_data()
xwin_data.set_quoted('DEFAULT_LOGDIR', log_dir)
xwin_data.set('HAS_WINSOCK', host_machine.system() == 'windows', description: 'Use Windows sockets')
xwin_data.set('HAS_DEVWINDOWS', host_machine.system() == 'cygwin', description: 'Has /dev/windows for signaling new win32 messages')
xwin_data.set('RELOCATE_PROJECTROOT', host_machine.system() == 'windows', description: 'Make paths relative to the xserver installation location')
# XXX: these three are all the same as DEBUG so we should just change to that
xwin_data.set10('CYGDEBUG', enable_debugging)
xwin_data.set10('CYGWINDOWING_DEBUG',enable_debugging)
xwin_data.set10('CYGMULTIWINDOW_DEBUG', enable_debugging)

configure_file(output : 'xwin-config.h',
               input : 'xwin-config.h.meson.in',
               configuration : xwin_data)

xwayland_data = configuration_data()
xwayland_data.set('XWL_HAS_GLAMOR', build_glamor and (gbm_dep.found() or build_eglstream))
xwayland_data.set('XWL_HAS_EGLSTREAM', build_eglstream)

configure_file(output : 'xwayland-config.h',
               input : 'xwayland-config.h.meson.in',
               configuration : xwayland_data)

if build_xorg
    install_data(
        [
            'XIstubs.h',
            'Xprintf.h',
            'callback.h',
            'client.h',
            'closestr.h',
            'closure.h',
            'colormap.h',
            'colormapst.h',
            'hotplug.h',
            'cursor.h',
            'cursorstr.h',
            'dix.h',
            'dixaccess.h',
            'dixevents.h',
            'dixfont.h',
            'dixfontstr.h',
            'dixgrabs.h',
            'dixstruct.h',
            'events.h',
            'exevents.h',
            'extension.h',
            'extinit.h',
            'extnsionst.h',
            'gc.h',
            'gcstruct.h',
            'globals.h',
            'glx_extinit.h',
            'glxvndabi.h',
            'input.h',
            'inputstr.h',
            'list.h',
            'misc.h',
            'miscstruct.h',
            'opaque.h',
            'nonsdk_extinit.h',
            'optionstr.h',
            'os.h',
            'pixmap.h',
            'pixmapstr.h',
            'privates.h',
            'property.h',
            'propertyst.h',
            'ptrveloc.h',
            'region.h',
            'regionstr.h',
            'registry.h',
            'resource.h',
            'rgb.h',
            'screenint.h',
            'scrnintstr.h',
            'selection.h',
            'servermd.h',
            'site.h',
            'validate.h',
            'displaymode.h',
            'window.h',
            'windowstr.h',
            'xkbfile.h',
            'xkbsrv.h',
            'xkbstr.h',
            'xkbrules.h',
            'Xprintf.h',
            'xserver_poll.h',
            'xserver-properties.h',
        ],
        install_dir: xorgsdkdir,
    )
endif
