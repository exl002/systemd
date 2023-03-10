# SPDX-License-Identifier: LGPL-2.1-or-later

sd_journal_sources = files(
        'sd-journal/audit-type.c',
        'sd-journal/catalog.c',
        'sd-journal/journal-file.c',
        'sd-journal/journal-send.c',
        'sd-journal/journal-vacuum.c',
        'sd-journal/journal-verify.c',
        'sd-journal/lookup3.c',
        'sd-journal/mmap-cache.c',
        'sd-journal/sd-journal.c',
)

if conf.get('HAVE_GCRYPT') == 1
        sd_journal_sources += files(
                'sd-journal/fsprg.c',
                'sd-journal/journal-authenticate.c',
        )
endif

audit_type_includes = [config_h,
                       missing_audit_h,
                       'linux/audit.h']
if conf.get('HAVE_AUDIT') == 1
        audit_type_includes += 'libaudit.h'
endif

generate_audit_type_list = find_program('sd-journal/generate-audit_type-list.sh')
audit_type_list_txt = custom_target(
        'audit_type-list.txt',
        output : 'audit_type-list.txt',
        command : [generate_audit_type_list, cpp] + audit_type_includes,
        capture : true)

audit_type_to_name = custom_target(
        'audit_type-to-name.h',
        input : ['sd-journal/audit_type-to-name.awk', audit_type_list_txt],
        output : 'audit_type-to-name.h',
        command : [awk, '-f', '@INPUT0@', '@INPUT1@'],
        capture : true)

sd_journal_sources += [audit_type_to_name]

############################################################

id128_sources = files(
        'sd-id128/id128-util.c',
        'sd-id128/sd-id128.c',
)

############################################################

sd_daemon_sources = files('sd-daemon/sd-daemon.c')

############################################################

sd_event_sources = files(
        'sd-event/event-util.c',
        'sd-event/sd-event.c',
)

############################################################

sd_login_sources = files('sd-login/sd-login.c')

############################################################

libsystemd_sources = files(
        'sd-bus/bus-common-errors.c',
        'sd-bus/bus-container.c',
        'sd-bus/bus-control.c',
        'sd-bus/bus-convenience.c',
        'sd-bus/bus-creds.c',
        'sd-bus/bus-dump.c',
        'sd-bus/bus-error.c',
        'sd-bus/bus-internal.c',
        'sd-bus/bus-introspect.c',
        'sd-bus/bus-kernel.c',
        'sd-bus/bus-match.c',
        'sd-bus/bus-message.c',
        'sd-bus/bus-objects.c',
        'sd-bus/bus-signature.c',
        'sd-bus/bus-slot.c',
        'sd-bus/bus-socket.c',
        'sd-bus/bus-track.c',
        'sd-bus/bus-type.c',
        'sd-bus/sd-bus.c',
        'sd-device/device-enumerator.c',
        'sd-device/device-filter.c',
        'sd-device/device-monitor.c',
        'sd-device/device-private.c',
        'sd-device/device-util.c',
        'sd-device/sd-device.c',
        'sd-hwdb/sd-hwdb.c',
        'sd-netlink/netlink-genl.c',
        'sd-netlink/netlink-message-nfnl.c',
        'sd-netlink/netlink-message-rtnl.c',
        'sd-netlink/netlink-message.c',
        'sd-netlink/netlink-slot.c',
        'sd-netlink/netlink-socket.c',
        'sd-netlink/netlink-types-genl.c',
        'sd-netlink/netlink-types-nfnl.c',
        'sd-netlink/netlink-types-rtnl.c',
        'sd-netlink/netlink-types.c',
        'sd-netlink/netlink-util.c',
        'sd-netlink/sd-netlink.c',
        'sd-network/network-util.c',
        'sd-network/sd-network.c',
        'sd-path/sd-path.c',
        'sd-resolve/sd-resolve.c',
) + sd_journal_sources + id128_sources + sd_daemon_sources + sd_event_sources + sd_login_sources


libsystemd_c_args = ['-fvisibility=default']

libsystemd_static = static_library(
        'systemd_static',
        libsystemd_sources,
        include_directories : libsystemd_includes,
        c_args : libsystemd_c_args,
        link_with : [libbasic,
                     libbasic_compress],
        dependencies : [threads,
                        librt],
        build_by_default : false)

libsystemd_sym = files('libsystemd.sym')
libsystemd_sym_path = meson.current_source_dir() / 'libsystemd.sym'

static_libsystemd = get_option('static-libsystemd')
static_libsystemd_pic = static_libsystemd == 'true' or static_libsystemd == 'pic'

libsystemd_pc = custom_target(
        'libsystemd.pc',
        input : 'libsystemd.pc.in',
        output : 'libsystemd.pc',
        command : [jinja2_cmdline, '@INPUT@', '@OUTPUT@'],
        install : pkgconfiglibdir != 'no',
        install_tag : 'devel',
        install_dir : pkgconfiglibdir)

############################################################

tests += [
        [files('sd-journal/test-journal-file.c')],

        [files('sd-journal/test-journal-send.c')],

        [files('sd-journal/test-journal-match.c')],

        [files('sd-journal/test-journal-enum.c'),
         [], [], [], '', 'timeout=360'],

        [files('sd-journal/test-journal-init.c')],

        [files('sd-journal/test-mmap-cache.c')],

        [files('sd-journal/test-catalog.c')],

        [files('sd-journal/test-audit-type.c')],
]

############################################################

tests += [
        [files('sd-bus/test-bus-address.c'),
         [],
         [threads]],

        [files('sd-bus/test-bus-marshal.c'),
         [],
         [threads,
          libglib,
          libgobject,
          libgio,
          libdbus,
          libm]],

        [files('sd-bus/test-bus-signature.c'),
         [],
         [threads]],

        [files('sd-bus/test-bus-queue-ref-cycle.c'),
         [],
         [threads]],

        [files('sd-bus/test-bus-watch-bind.c'),
         [],
         [threads],
         [], '', 'timeout=120'],

        [files('sd-bus/test-bus-chat.c'),
         [],
         [threads]],

        [files('sd-bus/test-bus-cleanup.c'),
         [],
         [threads,
          libseccomp]],

        [files('sd-bus/test-bus-track.c'),
         [],
         [libseccomp]],

        [files('sd-bus/test-bus-server.c'),
         [],
         [threads]],

        [files('sd-bus/test-bus-objects.c'),
         [],
         [threads]],

        [files('sd-bus/test-bus-vtable.c')],

        [files('sd-bus/test-bus-creds.c')],

        [files('sd-bus/test-bus-match.c')],

        [files('sd-bus/test-bus-benchmark.c'),
         [],
         [threads],
         [], '', 'manual'],

        [files('sd-bus/test-bus-introspect.c')],

        [files('sd-event/test-event.c')],

        [files('sd-netlink/test-netlink.c')],

        [files('sd-resolve/test-resolve.c'),
         [],
         [threads],
         [], '', 'timeout=120'],

        [files('sd-login/test-login.c')],

        [files('sd-device/test-sd-device.c')],

        [files('sd-device/test-device-util.c')],

        [files('sd-device/test-sd-device-monitor.c')],
]

if cxx_cmd != ''
        tests += [
                [files('sd-bus/test-bus-vtable-cc.cc')],
        ]
endif

############################################################

fuzzers += [
        [files('sd-bus/fuzz-bus-message.c')],

        [files('sd-bus/fuzz-bus-match.c')],
]
