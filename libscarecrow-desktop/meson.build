libargs = [
  '-DG_LOG_DOMAIN="ScarecrowDesktop"',
  '-DGDK_DISABLE_DEPRECATION_WARNINGS',
  '-DGTK_DISABLE_DEPRECATION_WARNINGS',
  '-DGNOMELOCALEDIR="@0@"'.format(localedir),
  '-DLIBLOCALEDIR="@0@"'.format(liblocaledir),
  '-DISO_CODES_PREFIX="@0@"'.format(iso_codes_prefix),
  '-DFONTCONFIG_CACHE_PATH="@0@"'.format(fontconfig_cache_path),
  '-DXKB_BASE="@0@"'.format(xkb_base)
]

introspection_sources = [
  'scarecrow-desktop-thumbnail.c',
  'scarecrow-bg.c',
  'scarecrow-bg-slide-show.c',
  'scarecrow-bg-crossfade.c',
  'scarecrow-rr.c',
  'scarecrow-rr-config.c',
  'scarecrow-rr-output-info.c',
  'scarecrow-systemd.c',
  'scarecrow-pnp-ids.c',
  'scarecrow-wall-clock.c',
  'scarecrow-xkb-info.c',
  'scarecrow-idle-monitor.c',
  'scarecrow-languages.c'
]

dbus_xrandr_built_sources = gnome.gdbus_codegen('meta-dbus-xrandr',
  'xrandr.xml',
  namespace: 'MetaDBus',
  interface_prefix: 'io.github.scarecrow_de.Vater'
)

dbus_idle_built_sources = gnome.gdbus_codegen('meta-dbus-idle-monitor',
  'idle-monitor.xml',
  namespace: 'MetaDBus',
  interface_prefix: 'io.github.scarecrow_de.Vater',
  object_manager: true
)

libscarecrow_desktop_sources = [
  introspection_sources,
  dbus_xrandr_built_sources,
  dbus_idle_built_sources,
  'scarecrow-datetime-source.h',
  'scarecrow-datetime-source.c',
  'scarecrow-rr-private.h',
  'default-input-sources.h',
  'meta-xrandr-shared.h',
  'scarecrow-desktop-thumbnail-script.c',
  'scarecrow-desktop-thumbnail-script.h'
]

libscarecrow_desktop_headers = [
  'scarecrow-bg.h',
  'scarecrow-bg-crossfade.h',
  'scarecrow-bg-slide-show.h',
  'scarecrow-desktop-thumbnail.h',
  'scarecrow-rr.h',
  'scarecrow-rr-config.h',
  'scarecrow-systemd.h',
  'scarecrow-pnp-ids.h',
  'scarecrow-wall-clock.h',
  'scarecrow-xkb-info.h',
  'scarecrow-idle-monitor.h',
  'scarecrow-languages.h'
]

install_headers(libscarecrow_desktop_headers,
  subdir: 'scarecrow-desktop-3.0/libscarecrow-desktop'
)

gnome_desktop_deps = [
  config_h,
  gdk_pixbuf_dep,
  gtk_dep,
  glib_dep,
  gio_dep,
  gio_unix_dep,
  libsystemd_dep,
  schemas_dep,
  xkb_config_dep,
  iso_codes_dep,
  udev_dep,
  seccomp_dep
]

ldflags = []
symbol_map = '-Wl,--version-script=@0@'.format(meson.current_source_dir() / 'symbol.map')
if cc.has_link_argument(symbol_map)
  ldflags += symbol_map
endif

libscarecrow_desktop = library('scarecrow-desktop-3',
  libscarecrow_desktop_sources,
  dependencies: [gnome_desktop_deps, m_dep, rt_dep],
  soversion: soversion,
  version: libversion,
  c_args: libargs,
  link_args: ldflags,
  install: true
)

libscarecrow_desktop_dep = declare_dependency(
  dependencies: gnome_desktop_deps,
  link_with: libscarecrow_desktop
)

gnome.generate_gir(libscarecrow_desktop,
  sources: introspection_sources + libscarecrow_desktop_headers,
  export_packages: 'scarecrow-desktop-3.0',
  namespace: 'ScarecrowDesktop',
  nsversion: '3.0',
  includes: ['GObject-2.0', 'Gtk-3.0', 'GDesktopEnums-3.0'],
  extra_args: '-DGNOME_DESKTOP_USE_UNSTABLE_API',
  identifier_prefix: ['Gnome', 'Scarecrow'],
  symbol_prefix: ['gnome', 'scarecrow'],
  install: true
)

desktop_pc = pkg.generate(
  libraries: libscarecrow_desktop,
  requires: ['gtk+-3.0', 'scarecrow-gsettings-desktop-schemas'],
  version: meson.project_version(),
  name: 'scarecrow-desktop-3.0',
  filebase: 'scarecrow-desktop-3.0',
  description: 'Utility library for loading .desktop files',
  subdirs: 'scarecrow-desktop-3.0'
)

if get_option('debug_tools')
  executable('scarecrow-rr-debug',
    sources: 'scarecrow-rr-debug.c',
    dependencies: libscarecrow_desktop_dep,
    install_dir: libexecdir,
    install: true
  )
endif

test_programs = [
  'test-xkb-info',
  'test-pnp-ids',
  'test-wall-clock',
  'test-desktop-thumbnail',
  'test-idle-monitor',
  'test-languages'
]

foreach program : test_programs
  executable(program, program + '.c',
    dependencies: libscarecrow_desktop_dep
  )
endforeach
