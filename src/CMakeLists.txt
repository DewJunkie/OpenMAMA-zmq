add_library(mamazmqimpl
            MODULE bridge.c
                   msg.c
                   msg.h
                   publisher.c
                   subscription.c
                   subscription.h
                   transport.c
                   transport.h
                   zmqbridgefunctions.h
                   zmqdefs.h)

target_compile_definitions(mamazmqimpl PRIVATE
    BRIDGE MAMA_DLL OPENMAMA_INTEGRATION
)

target_link_libraries(mamazmqimpl PRIVATE 
    libzmq 
    OpenMAMA::mama
    OpenMAMA::mamabaseimpl
)

if(WIN32)
    target_compile_definitions(mamazmqimpl
        PRIVATE
        _CRT_SECURE_NO_WARNINGS
    )
    if (CMAKE_BUILD_TYPE MATCHES "Debug")
        set(MAMA_LIB_SUFFIX "mdd")
    else()
        set(MAMA_LIB_SUFFIX "md")
    endif()
    set_target_properties(mamazmqimpl PROPERTIES PREFIX "lib" SUFFIX "${MAMA_LIB_SUFFIX}.dll")

    # Windows Targets
    install(TARGETS mamazmqimpl
            CONFIGURATIONS Release
            DESTINATION bin/dynamic)
    install(TARGETS mamazmqimpl
            CONFIGURATIONS Debug
            DESTINATION bin/dynamic-debug)
elseif(UNIX)
    install(TARGETS mamazmqimpl DESTINATION lib)
endif()

# Default to installing directly to MAMA directory unless provided already
if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set (CMAKE_INSTALL_PREFIX "${MAMA_ROOT}" CACHE PATH "default install path" FORCE)
endif()
