cmake_minimum_required(VERSION 2.8.10 FATAL_ERROR)
project(at-scale-debug C)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/sysroot/include)

if(${BUILD_UT})
    enable_testing()
    add_subdirectory(tests)
else()
    add_executable(jtag_test jtag_test.c logging.c jtag_handler.c mem_helper.c)
    target_link_libraries(jtag_test -lm)
    install (TARGETS jtag_test DESTINATION bin)

    add_executable(asd asd_main.c logging.c ext_network.c authenticate.c
            session.c logging.c config.c jtag_handler.c asd_msg.c ext_tcp.c
            auth_none.c target_handler.c gpio.c dbus_helper.c ext_tls.c
            auth_pam.c i2c_msg_builder.c i2c_handler.c mem_helper.c)
    target_link_libraries(asd -lm -lsystemd -lssl -lcrypto -lpam -lgpiod)
    install (TARGETS asd DESTINATION bin)

    # Copy pam-asd to etc/pam.d
    install (
        FILES ${CMAKE_CURRENT_SOURCE_DIR}/pam-asd
        DESTINATION /etc/pam.d/
        RENAME asd
    )
endif(${BUILD_UT})

