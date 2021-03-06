# Copyright © 2017-2018,
# Battelle Memorial Institute; Lawrence Livermore National Security, LLC; Alliance for Sustainable Energy, LLC
# All rights reserved. See LICENSE file and DISCLAIMER for more details.


find_program(PYTHON "python")

if (PYTHON)

    file(REMOVE "${CMAKE_CURRENT_BINARY_DIR}/helics.so")
    file(GLOB PYX_FILES "${CMAKE_CURRENT_SOURCE_DIR}/*.pyx")
    file(COPY ${PYX_FILES} DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
    file(GLOB PXD_FILES "${CMAKE_CURRENT_SOURCE_DIR}/*.pxd")
    file(COPY ${PXD_FILES} DESTINATION ${CMAKE_CURRENT_BINARY_DIR})


    set(SETUP_PY_IN "${CMAKE_CURRENT_SOURCE_DIR}/setup.py.in")
    set(SETUP_PY    "${CMAKE_CURRENT_BINARY_DIR}/setup.py")
    set(DEPS        "${CMAKE_CURRENT_SOURCE_DIR}/helics.pyx")
    set(OUTPUT      "${CMAKE_CURRENT_BINARY_DIR}/build/timestamp")

    configure_file(${SETUP_PY_IN} ${SETUP_PY})

    add_custom_command(OUTPUT ${OUTPUT}
                       COMMAND ${PYTHON} ${SETUP_PY} build_ext --inplace
                       COMMAND ${CMAKE_COMMAND} -E touch ${OUTPUT}
                       )

    add_custom_target(target ALL DEPENDS ${OUTPUT})

    execute_process(COMMAND ${CMAKE_COMMAND} -E conda install cython)

    install(CODE "execute_process(COMMAND ${PYTHON} ${SETUP_PY} build_ext install)")
endif()

