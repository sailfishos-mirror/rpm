set(core
	gendiff.1 rpm2cpio.8 rpm2archive.8
	rpm.8 rpmbuild.8 rpmdb.8 rpmkeys.8 rpmsign.8 rpmspec.8
	rpmdeps.8 rpmgraph.8 rpmlua.8 rpm-misc.8 rpmsort.8
)
set(extra
	rpm-plugins.8 rpm-plugin-prioreset.8 rpm-plugin-syslog.8
	rpm-plugin-audit.8 rpm-plugin-dbus-announce.8
	rpm-plugin-systemd-inhibit.8 rpm-plugin-fapolicyd.8 rpm-plugin-ima.8
	rpm-plugin-selinux.8 rpm-plugin-unshare.8
)

# Build all manuals
set(manuals ${core} ${extra})
foreach(man ${manuals})
	add_custom_command(OUTPUT ${man} COMMAND ${SCDOC}
			< ${CMAKE_CURRENT_SOURCE_DIR}/${man}.scd
			> ${man} DEPENDS ${man}.scd)
endforeach()
add_custom_target(man ALL DEPENDS ${manuals})

# Install manuals for the enabled features
set(manuals ${core})
if (ENABLE_PLUGINS)
	list(APPEND manuals rpm-plugins.8
		rpm-plugin-prioreset.8 rpm-plugin-syslog.8)
	if (WITH_AUDIT)
		list(APPEND manuals rpm-plugin-audit.8)
	endif()
	if (WITH_DBUS)
		list(APPEND manuals
			rpm-plugin-dbus-announce.8 rpm-plugin-systemd-inhibit.8)
	endif()
	if (WITH_FAPOLICYD)
		list(APPEND manuals rpm-plugin-fapolicyd.8)
	endif()
	if (WITH_IMAEVM)
		list(APPEND manuals rpm-plugin-ima.8)
	endif()
	if (WITH_SELINUX)
		list(APPEND manuals rpm-plugin-selinux.8)
	endif()
	if (HAVE_UNSHARE)
		list(APPEND manuals rpm-plugin-unshare.8)
	endif()
endif()
foreach(man ${manuals})
	set(fn ${CMAKE_CURRENT_BINARY_DIR}/${man})
	get_filename_component(ext ${fn} EXT)
	string(REPLACE "." "man" section ${ext})
	install(FILES ${fn}
		DESTINATION ${CMAKE_INSTALL_MANDIR}/${section})
endforeach()
