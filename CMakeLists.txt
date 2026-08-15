cmake_minimum_required(VERSION 3.17)
project(shaderc_prebuilt NONE)



set(SHADERC_LIB_ROOT_PATH "${CMAKE_CURRENT_LIST_DIR}/libshaderc/prebuilt-libs")




if(ANDROID)

    if(ANDROID_ABI STREQUAL "x86_64")

        set(SHADERC_PLATFORM "android/x86_64")
	    set(SHADERC_SHARED_FILE_NAME "libshaderc_shared.so")

    elseif(ANDROID_ABI STREQUAL "arm64-v8a")

        set(SHADERC_PLATFORM "android/arm64-v8a")
	    set(SHADERC_SHARED_FILE_NAME "libshaderc_shared.so")

    else()

        message(FATAL_ERROR "Unsupported Android ABI: ${ANDROID_ABI}")

    endif()

elseif(WIN32)

    set(SHADERC_PLATFORM "win64")
	set(SHADERC_SHARED_FILE_NAME "shaderc_shared.dll")

    if(MSVC)

	    set(SHADERC_SHARED_IMPORT_FILE "import-library/msvc/shaderc_shared.lib")

    elseif(MINGW)

	    set(SHADERC_SHARED_IMPORT_FILE "import-library/mingw/shaderc_shared.a")

    else()

        message(FATAL_ERROR 
            "Unsupported compiler for prebuilt shaderc on Windows.\n"
            "Prebuilt binaries currently support MSVC, Clang** and MinGW in windows.\n"
            "If you need support for another compiler/toolchain, please open an issue at: "
            "https://github.com/aka411/the-engine-prebuilt-deps/issues"
        )

    endif()

elseif(LINUX)

    set(SHADERC_PLATFORM "linux64")
    set(SHADERC_SHARED_FILE_NAME "libshaderc_shared.so.1")

else()

    message(FATAL_ERROR "Platform not supported.")

endif()    




if(NOT TARGET shaderc::shaderc_shared)

    add_library(shaderc::shaderc_shared SHARED IMPORTED)

    set_target_properties(shaderc::shaderc_shared PROPERTIES
        IMPORTED_GLOBAL TRUE
        IMPORTED_LOCATION "${SHADERC_LIB_ROOT_PATH}/${SHADERC_PLATFORM}/${SHADERC_SHARED_FILE_NAME}"
        INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/libshaderc/include"
    )

    if(DEFINED SHADERC_SHARED_IMPORT_FILE)
        set_target_properties(shaderc::shaderc_shared PROPERTIES
            IMPORTED_IMPLIB "${SHADERC_LIB_ROOT_PATH}/${SHADERC_PLATFORM}/${SHADERC_SHARED_IMPORT_FILE}"
        )

    endif()
endif()