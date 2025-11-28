#
# project: fips-uWebSockets
# Simple, secure & standards compliant web server for the most demanding of applications
#
cmake_minimum_required(VERSION 3.21)

project(fips-uWebSockets)

get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
include("${FIPS_ROOT_DIR}/cmake/fips.cmake")

fips_setup()

# Options
option(UWEBSOCKETS_USE_LIBDEFLATE "Use libdeflate instead of zlib for compression" ON)

fips_begin_lib(uWebSockets)

    # uWebSockets is header-only, but we declare the headers for IDE support
    fips_dir(src)
    fips_files(
        App.h
        AsyncSocket.h
        AsyncSocketData.h
        BloomFilter.h
        CachingApp.h
        ChunkedEncoding.h
        ClientApp.h
        Http3App.h
        Http3Context.h
        Http3ContextData.h
        Http3Request.h
        Http3Response.h
        Http3ResponseData.h
        HttpContext.h
        HttpContextData.h
        HttpErrors.h
        HttpParser.h
        HttpResponse.h
        HttpResponseData.h
        HttpRouter.h
        LocalCluster.h
        Loop.h
        LoopData.h
        MessageParser.h
        MoveOnlyFunction.h
        Multipart.h
        PerMessageDeflate.h
        ProxyParser.h
        QueryParser.h
        TopicTree.h
        Utilities.h
        WebSocket.h
        WebSocketContext.h
        WebSocketContextData.h
        WebSocketData.h
        WebSocketExtensions.h
        WebSocketHandshake.h
        WebSocketProtocol.h
    )

fips_end_lib()

# Include directories
target_include_directories(uWebSockets PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/src)

# Dependencies
fips_deps(uSockets)

if (UWEBSOCKETS_USE_LIBDEFLATE)
    fips_deps(libdeflate)
    target_compile_definitions(uWebSockets PUBLIC UWS_USE_LIBDEFLATE)
else()
    find_package(ZLIB REQUIRED)
    target_link_libraries(uWebSockets ZLIB::ZLIB)
endif()

# C++20 standard
set_target_properties(uWebSockets PROPERTIES CXX_STANDARD 20)

# Disable warnings for third-party code
if (FIPS_MSVC)
    target_compile_options(uWebSockets PRIVATE /wd4100 /wd4127 /wd4244 /wd4267)
elseif (FIPS_CLANG OR FIPS_GCC)
    target_compile_options(uWebSockets PRIVATE -Wno-unused-parameter)
endif()
