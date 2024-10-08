cmake_minimum_required(VERSION 3.13)
project(joycond)
set(VERSION "0.1")

# default CMAKE INSTALL PREFIX so install can use relative paths
set(CMAKE_INSTALL_PREFIX ~/joycondir/install/ )

set(CMAKE_CXX_STANDARD 17)

# Generate compile_commands.json
set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

find_package(PkgConfig)
pkg_check_modules(LIBEVDEV REQUIRED libevdev)
pkg_check_modules(LIBUDEV REQUIRED libudev)

add_executable(joycond "" )
target_compile_options(joycond PRIVATE -Wall -Werror)
include_directories(
    include/
    ${LIBEVDEV_INCLUDE_DIRS}
    ${LIBUDEV_INCLUDE_DIRS}
    )
target_link_libraries(
    joycond
    ${LIBEVDEV_LIBRARIES}
    ${LIBUDEV_LIBRARIES}
    )

add_subdirectory(src)

install(TARGETS joycond DESTINATION usr/bin/
        PERMISSIONS OWNER_WRITE OWNER_READ OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
        )
install(FILES udev/89-joycond.rules udev/72-joycond.rules DESTINATION lib/udev/rules.d/
        PERMISSIONS OWNER_WRITE OWNER_READ GROUP_READ WORLD_READ 
        )
install(FILES systemd/joycond.service DESTINATION etc/systemd/system
        PERMISSIONS OWNER_WRITE OWNER_READ GROUP_READ WORLD_READ
        )
install(FILES systemd/joycond.conf DESTINATION etc/modules-load.d
        PERMISSIONS OWNER_WRITE OWNER_READ GROUP_READ WORLD_READ
        )
install(FILES doc/joycond.1 DESTINATION usr/share/man/man1
        PERMISSIONS OWNER_WRITE OWNER_READ GROUP_READ WORLD_READ
        )

add_custom_target("uninstall" COMMENT "Uninstall installed files" )
add_custom_command(
    TARGET "uninstall"
    POST_BUILD
    COMMENT "Uninstall files with install_manifest.txt"
    COMMAND xargs rm -vf < install_manifest.txt || echo Nothing in
            install_manifest.txt to be uninstalled!
)

set(CPACK_PACKAGE_VERSION ${VERSION})
set(CPACK_GENERATOR "RPM" )
set(CPACK_PACKAGE_NAME "joycond" )
set(CPACK_PACKAGE_RELEASE 1)
set(CPACK_PACKAGING_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX})
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${CPACK_PACKAGE_RELEASE}.${CMAKE_SYSTEM_PROCESSOR}")
include(CPack)
