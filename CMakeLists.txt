
cmake_minimum_required(VERSION 3.11 FATAL_ERROR)
project(Lua
    VERSION 5.3
    DESCRIPTION "Lua is a powerful, efficient, lightweight, embeddable scripting language."
    LANGUAGES C)

include(CheckFunctionExists)
include(GNUInstallDirs)

# liblua.a
add_library(liblua STATIC
    include/lauxlib.h
    include/luaconf.h
    include/lua.h
    include/lua.hpp
    include/lualib.h
    src/lapi.c
    src/lapi.h
    src/lauxlib.c
    src/lbaselib.c
    src/lbitlib.c
    src/lcode.c
    src/lcode.h
    src/lcorolib.c
    src/lctype.c
    src/lctype.h
    src/ldblib.c
    src/ldebug.c
    src/ldebug.h
    src/ldo.c
    src/ldo.h
    src/ldump.c
    src/lfunc.c
    src/lfunc.h
    src/lgc.c
    src/lgc.h
    src/linit.c
    src/liolib.c
    src/llex.c
    src/llex.h
    src/llimits.h
    src/lmathlib.c
    src/lmem.c
    src/lmem.h
    src/loadlib.c
    src/lobject.c
    src/lobject.h
    src/lopcodes.c
    src/lopcodes.h
    src/loslib.c
    src/lparser.c
    src/lparser.h
    src/lprefix.h
    src/lstate.c
    src/lstate.h
    src/lstring.c
    src/lstring.h
    src/lstrlib.c
    src/ltable.c
    src/ltable.h
    src/ltablib.c
    src/ltm.c
    src/ltm.h
    # src/lua.c
    # src/luac.c
    src/lundump.c
    src/lundump.h
    src/lutf8lib.c
    src/lvm.c
    src/lvm.h
    src/lzio.c
    src/lzio.h)
set_target_properties(liblua PROPERTIES
    OUTPUT_NAME lua)
target_include_directories(liblua
    PRIVATE src
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)
target_link_libraries(liblua PRIVATE ${CMAKE_DL_LIBS})
check_function_exists(pow dont_need_libm)
if(NOT dont_need_libm)
    target_link_libraries(liblua PRIVATE m)
endif()

# OS dependent options
if(APPLE)
    target_compile_definitions(liblua PUBLIC LUA_USE_MACOSX)
    target_compile_features(liblua PUBLIC c_std_99)
elseif(UNIX)
    target_compile_definitions(liblua PUBLIC LUA_USE_LINUX)
    target_compile_features(liblua PUBLIC c_std_99)
elseif(WIN32)
    target_compile_definitions(liblua PUBLIC LUA_BUILD_AS_DLL)
    target_compile_features(liblua PUBLIC c_std_99)
else()
    target_compile_definitions(liblua PUBLIC LUA_USE_C89)
    target_compile_features(liblua PUBLIC c_std_90)
endif()

add_library(lua::liblua ALIAS liblua)

# Installation
install(TARGETS liblua EXPORT lua-export
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT Development
    )
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR} COMPONENT Development
    )
install(EXPORT lua-export
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake
    NAMESPACE lua::
    COMPONENT Development)

