cmake_minimum_required (VERSION 3.4)
project (rai)

<<<<<<< HEAD
set (CPACK_PACKAGE_VERSION_MAJOR "14")
set (CPACK_PACKAGE_VERSION_MINOR "1")
set (CPACK_PACKAGE_VERSION_PATCH "0")
if (DEFINED GIT_COMMIT)
set (CPACK_PACKAGE_VERSION_PATCH "GIT-${GIT_COMMIT}")
endif (DEFINED GIT_COMMIT)
set (CPACK_PACKAGE_VENDOR "Banano Currency")

set(CMAKE_INSTALL_RPATH "@executable_path/../Frameworks")

set (BANANO_GUI OFF CACHE BOOL "")
set (BANANO_TEST OFF CACHE BOOL "")
set (BANANO_SECURE_RPC OFF CACHE BOOL "")

option(BANANO_ASAN_INT "Enable ASan+UBSan+Integer overflow" OFF)
option(BANANO_ASAN "Enable ASan+UBSan" OFF)
option(BANANO_SIMD_OPTIMIZATIONS "Enable CPU-specific SIMD optimizations (SSE/AVX or NEON, e.g.)" OFF)
set (BOOST_CUSTOM OFF CACHE BOOL "")
=======
set (CPACK_PACKAGE_VERSION_MAJOR "15")
set (CPACK_PACKAGE_VERSION_MINOR "0")
set (CPACK_PACKAGE_VERSION_PATCH "0")
if (DEFINED GIT_COMMIT)
	set (CPACK_PACKAGE_VERSION_PATCH "GIT-${GIT_COMMIT}")
endif ()
set (CPACK_PACKAGE_VENDOR "Nano Currency")

set(CMAKE_INSTALL_RPATH "@executable_path/../Frameworks")
# Create all libraries and executables in the root binary dir
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})

set (RAIBLOCKS_GUI OFF CACHE BOOL "")
set (RAIBLOCKS_TEST OFF CACHE BOOL "")
set (RAIBLOCKS_SECURE_RPC OFF CACHE BOOL "")

option(RAIBLOCKS_ASAN_INT "Enable ASan+UBSan+Integer overflow" OFF)
option(RAIBLOCKS_ASAN "Enable ASan+UBSan" OFF)
option(RAIBLOCKS_SIMD_OPTIMIZATIONS "Enable CPU-specific SIMD optimizations (SSE/AVX or NEON, e.g.)" OFF)

SET (ACTIVE_NETWORK rai_live_network CACHE STRING "Selects which network parameters are used")
set_property (CACHE ACTIVE_NETWORK PROPERTY STRINGS rai_test_network rai_beta_network rai_live_network)
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c

if(NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel." FORCE)
endif()

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# TODO check if any library requires these
#find_package(Threads REQUIRED)
#set (PLATFORM_LIBS ${CMAKE_THREAD_LIBS_INIT} ${CMAKE_DL_LIBS})

if (WIN32)
<<<<<<< HEAD
	set (PLATFORM_COMPILE_FLAGS "-DBOOST_SPIRIT_THREADSAFE -D_WIN32_WINNT=0x0600 -DWINVER=0x0600 -DWIN32_LEAN_AND_MEAN -DMINIUPNP_STATICLIB")
	set (BLAKE2_IMPLEMENTATION "blake2/blake2b.c")
else (WIN32)
	set (PLATFORM_COMPILE_FLAGS "-DBOOST_SPIRIT_THREADSAFE -Werror=switch -fPIC")
	if (BANANO_ASAN)
		set (PLATFORM_COMPILE_FLAGS "${PLATFORM_COMPILE_FLAGS} -fsanitize=address,undefined -DED25519_NO_INLINE_ASM")
	elseif (BANANO_ASAN_INT)
		set (PLATFORM_COMPILE_FLAGS "${PLATFORM_COMPILE_FLAGS} -fsanitize=address,undefined,integer -DED25519_NO_INLINE_ASM")
	elseif (BANANO_TSAN)
		set (PLATFORM_COMPILE_FLAGS "${PLATFORM_COMPILE_FLAGS} -fsanitize=thread -DED25519_NO_INLINE_ASM")
	endif()

	IF (CMAKE_SYSTEM_PROCESSOR MATCHES "^(i.86|x86(_64)?)$")
		if (BANANO_SIMD_OPTIMIZATIONS)
			set (PLATFORM_COMPILE_FLAGS "${PLATFORM_COMPILE_FLAGS} -msse4")
=======
	add_definitions(-D_WIN32_WINNT=0x0600
					-DWINVER=0x0600
					-DWIN32_LEAN_AND_MEAN
					-DMINIUPNP_STATICLIB)
else ()
	add_compile_options(-Werror=switch)
	if (RAIBLOCKS_ASAN)
		add_compile_options(-fsanitize=address,undefined)
		add_definitions(-DED25519_NO_INLINE_ASM)
	elseif (RAIBLOCKS_ASAN_INT)
		add_compile_options(-fsanitize=address,undefined,integer)
		add_definitions(-DED25519_NO_INLINE_ASM)
	elseif (RAIBLOCKS_TSAN)
		add_compile_options(-fsanitize=thread)
		add_definitions(-DED25519_NO_INLINE_ASM)
	endif()

	IF (CMAKE_SYSTEM_PROCESSOR MATCHES "^(i.86|x86(_64)?)$")
		if (RAIBLOCKS_SIMD_OPTIMIZATIONS)
			add_compile_options(-msse4)
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c
		elseif(CMAKE_SYSTEM_PROCESSOR STREQUAL "x86_64")
			add_compile_options(-msse2)
		endif()

		if (ENABLE_AVX2)
			add_compile_options(-mavx2 -mbmi -mbmi2)
			if (PERMUTE_WITH_GATHER)
				add_definitions(-DPERMUTE_WITH_GATHER)
			elseif (PERMUTE_WITH_SHUFFLES)
				add_definitions(-DPERMUTE_WITH_SHUFFLES)
			endif()
		endif()
	endif()
endif ()

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
#set(CMAKE_C_EXTENSIONS OFF)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
#set(CMAKE_CXX_EXTENSIONS OFF)

if (WIN32)
	add_definitions(/bigobj)
endif ()

if (APPLE)
	set (PLATFORM_LINK_FLAGS "-framework Foundation -framework OpenCL")
elseif (WIN32)
	set (PLATFORM_LINK_FLAGS "")
else ()
	set (PLATFORM_LINK_FLAGS "-static-libgcc -static-libstdc++")
	if (BANANO_ASAN)
		set (PLATFORM_LINK_FLAGS "${PLATFORM_LINK_FLAGS} -fsanitize=address,undefined")
	elseif (BANANO_ASAN_INT)
		set (PLATFORM_LINK_FLAGS "${PLATFORM_LINK_FLAGS} -fsanitize=address,undefined,integer")
	elseif (BANANO_TSAN)
		set (PLATFORM_LINK_FLAGS "${PLATFORM_LINK_FLAGS} -fsanitize=thread")
	endif()
<<<<<<< HEAD
endif (APPLE)

if (BANANO_GUI)
	if (WIN32)
		set (PLATFORM_QT_PACKAGES WinExtras)
	else (WIN32)
		set (PLATFORM_QT_PACKAGES)
	endif (WIN32)

	find_package (Qt5 COMPONENTS Core Gui Widgets Test ${PLATFORM_QT_PACKAGES})
	include_directories (${Qt5Core_INCLUDE_DIRS} ${Qt5Gui_INCLUDE_DIRS} ${Qt5Widgets_INCLUDE_DIRS} ${Qt5Test_INCLUDE_DIRS})
endif (BANANO_GUI)

if (BANANO_SECURE_RPC)
	find_package (OpenSSL 1.0 EXACT REQUIRED)
	include_directories(${OPENSSL_INCLUDE_DIR})
	add_definitions (-DBANANO_SECURE_RPC)
=======
endif ()

SET( CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${PLATFORM_LINK_FLAGS}" )

if (RAIBLOCKS_SECURE_RPC)
	find_package (OpenSSL 1.0 REQUIRED)
	#include_directories(${OPENSSL_INCLUDE_DIR})
	add_definitions (-DRAIBLOCKS_SECURE_RPC)
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c
	message("OpenSSL include dir: ${OPENSSL_INCLUDE_DIR}")
	message("OpenSSL libraries: ${OPENSSL_LIBRARIES}")
	message("OpenSSL lib: ${OPENSSL_SSL_LIBRARY}")
	message("Crypto lib: ${OPENSSL_CRYPTO_LIBRARY}")
else ()
	set (OPENSSL_LIBRARIES "")
<<<<<<< HEAD
endif (BANANO_SECURE_RPC)
=======
endif ()
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c

include_directories (${CMAKE_SOURCE_DIR})

set(Boost_USE_STATIC_LIBS        ON)
set(Boost_USE_MULTITHREADED      ON)

if (BOOST_ROOT)
    list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/Modules")
endif ()

find_package (Boost 1.66.0 REQUIRED COMPONENTS date_time filesystem system log log_setup thread program_options regex chrono atomic)

add_subdirectory(ed25519-donna)

set (UPNPC_BUILD_SHARED OFF CACHE BOOL "")
add_subdirectory (miniupnp/miniupnpc)
# FIXME: This fixes miniupnpc include directories without modifying miniupnpc's
# CMakeLists.txt but should be set there
set_target_properties(libminiupnpc-static PROPERTIES INTERFACE_INCLUDE_DIRECTORIES
	"${CMAKE_SOURCE_DIR}/miniupnp/miniupnpc")

set (BUILD_SHARED OFF CACHE BOOL "")
set (BUILD_TESTING OFF CACHE BOOL "")
set (USE_INTERMEDIATE_OBJECTS_TARGET OFF CACHE BOOL "")
if (CRYPTOPP_CUSTOM OR NOT BANANO_SIMD_OPTIMIZATIONS)
	set (CRYPTOPP_LIBRARY cryptopp)
	add_library (cryptopp
		cryptopp/algparam.cpp
		cryptopp/asn.cpp
		cryptopp/basecode.cpp
		cryptopp/cpu.cpp
		cryptopp/cryptlib.cpp
		cryptopp/default.cpp
		cryptopp/des.cpp
		cryptopp/dessp.cpp
		cryptopp/dll.cpp
		cryptopp/ec2n.cpp
		cryptopp/ecp.cpp
		cryptopp/filters.cpp
		cryptopp/fips140.cpp
		cryptopp/gcm.cpp
		cryptopp/gf2n.cpp
		cryptopp/gfpcrypt.cpp
		cryptopp/hex.cpp
		cryptopp/hmac.cpp
		cryptopp/hrtimer.cpp
		cryptopp/integer.cpp
		cryptopp/iterhash.cpp
		cryptopp/misc.cpp
		cryptopp/modes.cpp
		cryptopp/mqueue.cpp
		cryptopp/nbtheory.cpp
		cryptopp/oaep.cpp
		cryptopp/osrng.cpp
		cryptopp/pubkey.cpp
		cryptopp/queue.cpp
		cryptopp/randpool.cpp
		cryptopp/rdtables.cpp
		cryptopp/rijndael.cpp
		cryptopp/rng.cpp
		cryptopp/sha.cpp
		cryptopp/simple.cpp
		cryptopp/winpipes.cpp)
else ()
	set (CRYPTOPP_LIBRARY cryptopp-static)
	add_subdirectory (cryptopp)
endif ()

if (CMAKE_SYSTEM_PROCESSOR STREQUAL "x86_64")
	set (ARGON_CORE phc-winner-argon2/src/opt.c)
else ()
	set (ARGON_CORE phc-winner-argon2/src/ref.c)
endif ()

add_library (argon2
	phc-winner-argon2/src/argon2.c
	phc-winner-argon2/include/argon2.h
	phc-winner-argon2/src/core.c
	phc-winner-argon2/src/thread.c
	phc-winner-argon2/src/encoding.c
	${ARGON_CORE})

target_include_directories(argon2 PUBLIC phc-winner-argon2/include)
target_include_directories(argon2 PUBLIC phc-winner-argon2/src)
target_include_directories(argon2 PUBLIC blake2)

add_library (xxhash
	xxhash/xxhash.c
	xxhash/xxhash.h)

add_library (lmdb
	lmdb/libraries/liblmdb/lmdb.h
	lmdb/libraries/liblmdb/mdb.c
	lmdb/libraries/liblmdb/midl.c)

if (WIN32)
	target_link_libraries(lmdb ntdll)
endif()

if (WIN32)
	set (BLAKE2_IMPLEMENTATION "blake2/blake2b.c")
else ()
	IF (CMAKE_SYSTEM_PROCESSOR MATCHES "^(i.86|x86(_64)?)$")
		set (BLAKE2_IMPLEMENTATION "blake2/blake2b.c")
	else()
		set (BLAKE2_IMPLEMENTATION "blake2/blake2b-ref.c")
	endif()
endif ()

add_library (blake2
	blake2/blake2-config.h
	blake2/blake2-impl.h
	blake2/blake2.h
	${BLAKE2_IMPLEMENTATION})

<<<<<<< HEAD
if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
	set (PLATFORM_LIB_SOURCE banano/plat/default/priority.cpp)
	set (PLATFORM_SECURE_SOURCE banano/plat/osx/working.mm)
	set (PLATFORM_WALLET_SOURCE banano/plat/default/icon.cpp)
elseif (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
	set (PLATFORM_LIB_SOURCE banano/plat/windows/priority.cpp)
	set (PLATFORM_SECURE_SOURCE banano/plat/windows/working.cpp)
	set (PLATFORM_NODE_SOURCE banano/plat/windows/openclapi.cpp)
	set (PLATFORM_WALLET_SOURCE banano/plat/windows/icon.cpp Banano.rc)
elseif (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
	set (PLATFORM_LIB_SOURCE banano/plat/linux/priority.cpp)
	set (PLATFORM_SECURE_SOURCE banano/plat/posix/working.cpp)
	set (PLATFORM_NODE_SOURCE banano/plat/posix/openclapi.cpp)
	set (PLATFORM_WALLET_SOURCE banano/plat/default/icon.cpp)
elseif (${CMAKE_SYSTEM_NAME} MATCHES "FreeBSD")
	set (PLATFORM_LIB_SOURCE banano/plat/default/priority.cpp)
	set (PLATFORM_SECURE_SOURCE banano/plat/posix/working.cpp)
	set (PLATFORM_NODE_SOURCE banano/plat/posix/openclapi.cpp)
	set (PLATFORM_WALLET_SOURCE banano/plat/default/icon.cpp)
else ()
	error ("Unknown platform: ${CMAKE_SYSTEM_NAME}")
endif ()

# Embed bootstrap representative weights in executable
file (READ rep_weights.bin filedata HEX)
string (REGEX REPLACE "(..)" "0x\\1," filedata ${filedata})
file (WRITE ${CMAKE_BINARY_DIR}/bootstrap_weights.cpp "#include <cstddef>\n"
	"namespace rai {\n"
	"	unsigned char banano_bootstrap_weights[] = {${filedata} 0x00};\n"
	"	size_t banano_bootstrap_weights_size = sizeof(banano_bootstrap_weights) - 1;\n"
	"}\n")
	
if (BANANO_SECURE_RPC)
	set (SECURE_RPC_SOURCE banano/node/rpc_secure.cpp banano/node/rpc_secure.hpp)
endif ()

add_library (secure
	${PLATFORM_SECURE_SOURCE}
	${CMAKE_BINARY_DIR}/bootstrap_weights.cpp
	banano/config.hpp
	banano/common.cpp
	banano/common.hpp
	banano/blockstore.cpp
	banano/blockstore.hpp
	banano/ledger.cpp
	banano/ledger.hpp
	banano/node/utility.cpp
	banano/node/utility.hpp
	banano/versioning.hpp
	banano/versioning.cpp)

SET (RAI_LIB_SOURCES
	${PLATFORM_LIB_SOURCE}
	banano/lib/blocks.cpp
	banano/lib/blocks.hpp
	banano/lib/interface.cpp
	banano/lib/interface.h
	banano/lib/numbers.cpp
	banano/lib/numbers.hpp
	banano/lib/utility.cpp
	banano/lib/utility.hpp
	banano/lib/work.hpp
	banano/lib/work.cpp)

add_library (banano_lib SHARED ${RAI_LIB_SOURCES})
add_library (banano_lib_static STATIC ${RAI_LIB_SOURCES})

add_library (node
	${PLATFORM_NODE_SOURCE}
	${SECURE_RPC_SOURCE}
	banano/node/bootstrap.cpp
	banano/node/bootstrap.hpp
	banano/node/common.cpp
	banano/node/common.hpp
	banano/node/node.hpp
	banano/node/node.cpp
	banano/node/openclwork.cpp
	banano/node/openclwork.hpp
	banano/node/rpc.hpp
	banano/node/rpc.cpp
	banano/node/testing.hpp
	banano/node/testing.cpp
	banano/node/wallet.hpp
	banano/node/wallet.cpp
	banano/node/stats.hpp
	banano/node/stats.cpp
	banano/node/working.hpp
	banano/node/xorshift.hpp)


SET (ACTIVE_NETWORK banano_live_network CACHE STRING "Selects which network parameters are used")
set_property (CACHE ACTIVE_NETWORK PROPERTY STRINGS banano_test_network banano_beta_network banano_live_network)

if (BANANO_TEST)
	add_executable (core_test
		banano/core_test/block.cpp
		banano/core_test/block_store.cpp
		banano/core_test/interface.cpp
		banano/core_test/conflicts.cpp
		banano/core_test/daemon.cpp
		banano/core_test/entry.cpp
		banano/core_test/gap_cache.cpp
		banano/core_test/ledger.cpp
		banano/core_test/network.cpp
		banano/core_test/node.cpp
		banano/core_test/message.cpp
		banano/core_test/message_parser.cpp
		banano/core_test/processor_service.cpp
		banano/core_test/peer_container.cpp
		banano/core_test/rpc.cpp
		banano/core_test/uint256_union.cpp
		banano/core_test/versioning.cpp
		banano/core_test/wallet.cpp
		banano/core_test/wallets.cpp
		banano/core_test/work_pool.cpp)

	add_executable (slow_test
		banano/slow_test/node.cpp)
	
	set_target_properties (core_test slow_test PROPERTIES COMPILE_FLAGS "${PLATFORM_CXX_FLAGS} ${PLATFORM_COMPILE_FLAGS} -DQT_NO_KEYWORDS -DACTIVE_NETWORK=${ACTIVE_NETWORK} -DBANANO_VERSION_MAJOR=${CPACK_PACKAGE_VERSION_MAJOR} -DBANANO_VERSION_MINOR=${CPACK_PACKAGE_VERSION_MINOR} -DBOOST_ASIO_HAS_STD_ARRAY=1")
	set_target_properties (core_test slow_test PROPERTIES LINK_FLAGS "${PLATFORM_LINK_FLAGS}")
endif (BANANO_TEST)

if (BANANO_GUI)
=======
target_compile_definitions(blake2 PRIVATE -D__SSE2__)

add_subdirectory(rai/secure)
add_subdirectory(rai/lib)
add_subdirectory(rai/node)
add_subdirectory(rai/rai_node)

if (RAIBLOCKS_TEST)
    if(WIN32)
        if(MSVC_VERSION)
            if(MSVC_VERSION GREATER_EQUAL 1910)
                add_definitions(-DGTEST_LANG_CXX11=1)
                add_definitions(-DGTEST_HAS_TR1_TUPLE=0)
            endif()
        endif()
        set (gtest_force_shared_crt ON)
    else ()
        set (gtest_force_shared_crt OFF)
    endif()

    add_subdirectory (gtest)
	# FIXME: This fixes gtest include directories without modifying gtest's
	# CMakeLists.txt. Ideally we should use GTest::GTest and GTest::Main as
	# dependencies but it requires building gtest differently
	set_target_properties(gtest PROPERTIES INTERFACE_INCLUDE_DIRECTORIES
		"${CMAKE_SOURCE_DIR}/gtest/include")

	add_subdirectory(rai/core_test)
	add_subdirectory(rai/slow_test)
endif ()

if (RAIBLOCKS_GUI)
	if (WIN32)
		set (PLATFORM_QT_PACKAGES WinExtras)
	else ()
		set (PLATFORM_QT_PACKAGES)
	endif ()
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c

	find_package (Qt5 COMPONENTS Core Gui Widgets Test ${PLATFORM_QT_PACKAGES})

	add_library (qt
		banano/qt/qt.cpp
		banano/qt/qt.hpp)

<<<<<<< HEAD
	add_executable (banano_wallet ${PLATFORM_GUI_TARGET_PROPERTIES}
		${PLATFORM_WALLET_SOURCE}
		banano/banano_wallet/entry.cpp
		${RES})

	add_executable (qt_test
		banano/qt_test/entry.cpp
		banano/qt_test/qt.cpp)

	add_executable (qt_system
		banano/qt_system/entry.cpp)

	set_target_properties (banano_wallet qt_test PROPERTIES LINK_FLAGS "${PLATFORM_LINK_FLAGS}")
	
	set_target_properties (qt_test qt banano_wallet qt_system PROPERTIES COMPILE_FLAGS "${PLATFORM_CXX_FLAGS} ${PLATFORM_COMPILE_FLAGS} -DQT_NO_KEYWORDS -DACTIVE_NETWORK=${ACTIVE_NETWORK} -DBANANO_VERSION_MAJOR=${CPACK_PACKAGE_VERSION_MAJOR} -DBANANO_VERSION_MINOR=${CPACK_PACKAGE_VERSION_MINOR} -DBOOST_ASIO_HAS_STD_ARRAY=1")
	set_target_properties (qt qt_system PROPERTIES LINK_FLAGS "${PLATFORM_LINK_FLAGS}")
endif (BANANO_GUI)

add_executable (bananode
	banano/bananode/daemon.cpp
	banano/bananode/daemon.hpp
	banano/bananode/entry.cpp)

set_target_properties (argon2 PROPERTIES COMPILE_FLAGS "${PLATFORM_C_FLAGS} ${PLATFORM_COMPILE_FLAGS}")
set_target_properties (blake2 PROPERTIES COMPILE_FLAGS "${PLATFORM_C_FLAGS} ${PLATFORM_COMPILE_FLAGS} -D__SSE2__")
set_target_properties (ed25519 PROPERTIES COMPILE_FLAGS "${PLATFORM_C_FLAGS} ${PLATFORM_COMPILE_FLAGS} -DED25519_CUSTOMHASH -DED25519_CUSTOMRNG")
set_target_properties (secure node bananode banano_lib banano_lib_static PROPERTIES COMPILE_FLAGS "${PLATFORM_CXX_FLAGS} ${PLATFORM_COMPILE_FLAGS} -DQT_NO_KEYWORDS -DACTIVE_NETWORK=${ACTIVE_NETWORK} -DBANANO_VERSION_MAJOR=${CPACK_PACKAGE_VERSION_MAJOR} -DBANANO_VERSION_MINOR=${CPACK_PACKAGE_VERSION_MINOR} -DBOOST_ASIO_HAS_STD_ARRAY=1")
set_target_properties (secure node bananode PROPERTIES LINK_FLAGS "${PLATFORM_LINK_FLAGS}")
=======
	target_link_libraries(qt
		secure rai_lib node libminiupnpc-static Qt5::Gui Qt5::Widgets)

	target_compile_definitions(qt
		PRIVATE
		-DRAIBLOCKS_VERSION_MAJOR=${CPACK_PACKAGE_VERSION_MAJOR}
		-DRAIBLOCKS_VERSION_MINOR=${CPACK_PACKAGE_VERSION_MINOR})

	if (WIN32)
		set (PLATFORM_GUI_TARGET_PROPERTIES WIN32)
	else ()
		set (PLATFORM_GUI_TARGET_PROPERTIES "")
	endif ()

	if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
		set (PLATFORM_WALLET_SOURCE rai/rai_wallet/plat/default/icon.cpp)
	elseif (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
		set (PLATFORM_WALLET_SOURCE rai/rai_wallet/plat/windows/icon.cpp RaiBlocks.rc)
	elseif (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
		set (PLATFORM_WALLET_SOURCE rai/rai_wallet/plat/default/icon.cpp)
	elseif (${CMAKE_SYSTEM_NAME} MATCHES "FreeBSD")
		set (PLATFORM_WALLET_SOURCE rai/rai_wallet/plat/default/icon.cpp)
	else ()
		error ("Unknown platform: ${CMAKE_SYSTEM_NAME}")
	endif ()

	qt5_add_resources(RES resources.qrc)
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c

	add_executable (nano_wallet ${PLATFORM_GUI_TARGET_PROPERTIES}
		${PLATFORM_WALLET_SOURCE}
		rai/rai_wallet/entry.cpp
		rai/rai_wallet/icon.hpp
		${RES})

	target_link_libraries (nano_wallet
		node
		qt)

<<<<<<< HEAD
if (BANANO_TEST)
	target_link_libraries (core_test node secure lmdb ed25519 banano_lib_static argon2 ${OPENSSL_LIBRARIES} ${CRYPTOPP_LIBRARY} gtest_main gtest libminiupnpc-static ${Boost_ATOMIC_LIBRARY} ${Boost_CHRONO_LIBRARY} ${Boost_REGEX_LIBRARY} ${Boost_DATE_TIME_LIBRARY} ${Boost_FILESYSTEM_LIBRARY} ${Boost_SYSTEM_LIBRARY} ${Boost_LOG_LIBRARY} ${Boost_LOG_SETUP_LIBRARY} ${Boost_PROGRAM_OPTIONS_LIBRARY} ${Boost_THREAD_LIBRARY} ${PLATFORM_LIBS})

	target_link_libraries (slow_test node secure lmdb ed25519 banano_lib_static argon2 ${OPENSSL_LIBRARIES} ${CRYPTOPP_LIBRARY} gtest_main gtest libminiupnpc-static ${Boost_ATOMIC_LIBRARY} ${Boost_CHRONO_LIBRARY} ${Boost_REGEX_LIBRARY} ${Boost_DATE_TIME_LIBRARY} ${Boost_FILESYSTEM_LIBRARY} ${Boost_SYSTEM_LIBRARY} ${Boost_LOG_LIBRARY} ${Boost_PROGRAM_OPTIONS_LIBRARY} ${Boost_LOG_SETUP_LIBRARY} ${Boost_THREAD_LIBRARY} ${PLATFORM_LIBS})
endif (BANANO_TEST)

if (BANANO_GUI)
	target_link_libraries (qt_test node secure lmdb ed25519 banano_lib_static qt argon2 ${OPENSSL_LIBRARIES} ${CRYPTOPP_LIBRARY} gtest libminiupnpc-static ${Boost_ATOMIC_LIBRARY} ${Boost_CHRONO_LIBRARY} ${Boost_REGEX_LIBRARY} ${Boost_DATE_TIME_LIBRARY} ${Boost_FILESYSTEM_LIBRARY} ${Boost_SYSTEM_LIBRARY} ${Boost_LOG_LIBRARY} ${Boost_PROGRAM_OPTIONS_LIBRARY} ${Boost_LOG_SETUP_LIBRARY} ${Boost_THREAD_LIBRARY} Qt5::Core Qt5::Gui Qt5::Widgets Qt5::Test ${QT_QTGUI_LIBRARY} ${PLATFORM_LIBS})

	target_link_libraries (qt_system node secure lmdb ed25519 banano_lib_static qt argon2 ${OPENSSL_LIBRARIES} ${CRYPTOPP_LIBRARY} gtest libminiupnpc-static ${Boost_ATOMIC_LIBRARY} ${Boost_CHRONO_LIBRARY} ${Boost_REGEX_LIBRARY} ${Boost_DATE_TIME_LIBRARY} ${Boost_FILESYSTEM_LIBRARY} ${Boost_SYSTEM_LIBRARY} ${Boost_LOG_LIBRARY} ${Boost_PROGRAM_OPTIONS_LIBRARY} ${Boost_LOG_SETUP_LIBRARY} ${Boost_THREAD_LIBRARY} Qt5::Core Qt5::Gui Qt5::Widgets ${QT_QTGUI_LIBRARY} ${PLATFORM_LIBS})

	target_link_libraries (banano_wallet node secure lmdb ed25519 banano_lib_static qt argon2 ${OPENSSL_LIBRARIES} ${CRYPTOPP_LIBRARY} libminiupnpc-static ${Boost_ATOMIC_LIBRARY} ${Boost_CHRONO_LIBRARY} ${Boost_REGEX_LIBRARY} ${Boost_DATE_TIME_LIBRARY} ${Boost_FILESYSTEM_LIBRARY} ${Boost_SYSTEM_LIBRARY} ${Boost_LOG_LIBRARY} ${Boost_PROGRAM_OPTIONS_LIBRARY} ${Boost_LOG_SETUP_LIBRARY} ${Boost_THREAD_LIBRARY} Qt5::Core Qt5::Gui Qt5::Widgets ${QT_QTGUI_LIBRARY} ${PLATFORM_LIBS} ${PLATFORM_WALLET_LIBS})
endif (BANANO_GUI)

target_link_libraries (banano_lib ed25519 xxhash blake2 ${CRYPTOPP_LIBRARY})
target_link_libraries (banano_lib_static ed25519 xxhash blake2 ${CRYPTOPP_LIBRARY})

target_link_libraries (bananode node secure lmdb ed25519 banano_lib_static argon2 ${OPENSSL_LIBRARIES} ${CRYPTOPP_LIBRARY} libminiupnpc-static ${Boost_ATOMIC_LIBRARY} ${Boost_CHRONO_LIBRARY} ${Boost_REGEX_LIBRARY} ${Boost_DATE_TIME_LIBRARY} ${Boost_FILESYSTEM_LIBRARY} ${Boost_SYSTEM_LIBRARY} ${Boost_LOG_LIBRARY} ${Boost_PROGRAM_OPTIONS_LIBRARY} ${Boost_LOG_SETUP_LIBRARY} ${Boost_THREAD_LIBRARY} ${PLATFORM_LIBS})

set (CPACK_RESOURCE_FILE_LICENSE ${CMAKE_SOURCE_DIR}/LICENSE)
if (BANANO_GUI)
	if (APPLE)
		get_filename_component (Qt5Core_framework_DIR ${Qt5_DIR}/../../QtCore.framework ABSOLUTE)
		get_filename_component (Qt5Gui_framework_DIR ${Qt5_DIR}/../../QtGui.framework ABSOLUTE)
		get_filename_component (Qt5PrintSupport_framework_DIR ${Qt5_DIR}/../../QtPrintSupport.framework ABSOLUTE)
		get_filename_component (Qt5Test_framework_DIR ${Qt5_DIR}/../../QtTest.framework ABSOLUTE)
		get_filename_component (Qt5Widgets_framework_DIR ${Qt5_DIR}/../../QtWidgets.framework ABSOLUTE)
		get_filename_component (Qt5_platforms_DIR ${Qt5_DIR}/../../../plugins/platforms ABSOLUTE)

		file (COPY ${Qt5Core_framework_DIR} DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/packaging)
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-id" "@executable_path/../Frameworks/QtCore.framework/Versions/5/QtCore" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtCore.framework/Versions/5/QtCore")

		file (COPY ${Qt5Gui_framework_DIR} DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/packaging)
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-id" "@executable_path/../Frameworks/QtGui.framework/Versions/5/QtGui" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtGui.framework/Versions/5/QtGui")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Core_framework_DIR}/Versions/5/QtCore" "@executable_path/../Frameworks/QtCore.framework/Versions/5/QtCore" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtGui.framework/Versions/5/QtGui")

		file (COPY ${Qt5PrintSupport_framework_DIR} DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/packaging)
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-id" "@executable_path/../Frameworks/QtPrintSupport.framework/Versions/5/QtPrintSupport" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtPrintSupport.framework/Versions/5/QtPrintSupport")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Core_framework_DIR}/Versions/5/QtCore" "@executable_path/../Frameworks/QtCore.framework/Versions/5/QtCore" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtPrintSupport.framework/Versions/5/QtPrintSupport")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Gui_framework_DIR}/Versions/5/QtGui" "@executable_path/../Frameworks/QtGui.framework/Versions/5/QtGui" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtPrintSupport.framework/Versions/5/QtPrintSupport")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Widgets_framework_DIR}/Versions/5/QtWidgets" "@executable_path/../Frameworks/QtWidgets.framework/Versions/5/QtWidgets" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtPrintSupport.framework/Versions/5/QtPrintSupport")

		file (COPY ${Qt5Test_framework_DIR} DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/packaging)
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-id" "@executable_path/../Frameworks/QtTest.framework/Versions/5/QtTest" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtTest.framework/Versions/5/QtTest")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Core_framework_DIR}/Versions/5/QtCore" "@executable_path/../Frameworks/QtCore.framework/Versions/5/QtCore" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtTest.framework/Versions/5/QtTest")

		file (COPY ${Qt5Widgets_framework_DIR} DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/packaging)
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-id" "@executable_path/../Frameworks/QtWidgets.framework/Versions/5/QtWidgets" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtWidgets.framework/Versions/5/QtWidgets")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Core_framework_DIR}/Versions/5/QtCore" "@executable_path/../Frameworks/QtCore.framework/Versions/5/QtCore" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtWidgets.framework/Versions/5/QtWidgets")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Gui_framework_DIR}/Versions/5/QtGui" "@executable_path/../Frameworks/QtGui.framework/Versions/5/QtGui" "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtWidgets.framework/Versions/5/QtWidgets")

		file (COPY ${Qt5_DIR}/../../../plugins/platforms/libqcocoa.dylib DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/packaging)
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Core_framework_DIR}/Versions/5/QtCore" "@executable_path/../Frameworks/QtCore.framework/Versions/5/QtCore" "${CMAKE_CURRENT_BINARY_DIR}/packaging/libqcocoa.dylib")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Gui_framework_DIR}/Versions/5/QtGui" "@executable_path/../Frameworks/QtGui.framework/Versions/5/QtGui" "${CMAKE_CURRENT_BINARY_DIR}/packaging/libqcocoa.dylib")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5PrintSupport_framework_DIR}/Versions/5/QtPrintSupport" "@executable_path/../Frameworks/QtPrintSupport.framework/Versions/5/QtPrintSupport" "${CMAKE_CURRENT_BINARY_DIR}/packaging/libqcocoa.dylib")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Widgets_framework_DIR}/Versions/5/QtWidgets" "@executable_path/../Frameworks/QtWidgets.framework/Versions/5/QtWidgets" "${CMAKE_CURRENT_BINARY_DIR}/packaging/libqcocoa.dylib")

		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:banano_wallet> ${CMAKE_CURRENT_BINARY_DIR}/packaging)
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Core_framework_DIR}/Versions/5/QtCore" "@executable_path/../Frameworks/QtCore.framework/Versions/5/QtCore" "${CMAKE_CURRENT_BINARY_DIR}/packaging/banano_wallet")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Gui_framework_DIR}/Versions/5/QtGui" "@executable_path/../Frameworks/QtGui.framework/Versions/5/QtGui" "${CMAKE_CURRENT_BINARY_DIR}/packaging/banano_wallet")
		add_custom_command (TARGET banano_wallet POST_BUILD COMMAND "install_name_tool" "-change" "${Qt5Widgets_framework_DIR}/Versions/5/QtWidgets" "@executable_path/../Frameworks/QtWidgets.framework/Versions/5/QtWidgets" "${CMAKE_CURRENT_BINARY_DIR}/packaging/banano_wallet")

		install (FILES ${CMAKE_CURRENT_BINARY_DIR}/packaging/banano_wallet DESTINATION Banano.app/Contents/MacOS PERMISSIONS OWNER_EXECUTE OWNER_READ)
		install (FILES Info.plist DESTINATION Banano.app/Contents)
		install (FILES qt.conf DESTINATION Banano.app/Contents/Resources)
		install (DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtCore.framework" DESTINATION Banano.app/Contents/Frameworks USE_SOURCE_PERMISSIONS)
		install (DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtGui.framework" DESTINATION Banano.app/Contents/Frameworks USE_SOURCE_PERMISSIONS)
		install (DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtPrintSupport.framework" DESTINATION Banano.app/Contents/Frameworks USE_SOURCE_PERMISSIONS)
		install (DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtTest.framework" DESTINATION Banano.app/Contents/Frameworks USE_SOURCE_PERMISSIONS)
		install (DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/packaging/QtWidgets.framework" DESTINATION Banano.app/Contents/Frameworks USE_SOURCE_PERMISSIONS)
		install (FILES "${CMAKE_CURRENT_BINARY_DIR}/packaging/libqcocoa.dylib" DESTINATION Banano.app/Contents/PlugIns/platforms PERMISSIONS OWNER_EXECUTE OWNER_READ)
		install (FILES banano.icns DESTINATION Banano.app/Contents/Resources)
=======
	if (WIN32)
		target_link_libraries (nano_wallet Qt5::WinExtras)
	endif()

	add_executable (qt_system
		rai/qt_system/entry.cpp)

	target_link_libraries (qt_system qt node Qt5::Gui Qt5::Widgets)

	set_target_properties (qt nano_wallet qt_system PROPERTIES COMPILE_FLAGS "-DQT_NO_KEYWORDS -DBOOST_ASIO_HAS_STD_ARRAY=1")

	if (RAIBLOCKS_TEST)
		add_executable (qt_test
			rai/qt_test/entry.cpp
			rai/qt_test/qt.cpp)

		target_link_libraries(qt_test gtest gtest_main qt Qt5::Test)

		set_target_properties (qt_test PROPERTIES COMPILE_FLAGS "-DQT_NO_KEYWORDS -DBOOST_ASIO_HAS_STD_ARRAY=1")
	endif ()

	if (APPLE)
		install (TARGETS nano_wallet DESTINATION Nano.app/Contents/MacOS)
		install (FILES Info.plist DESTINATION Nano.app/Contents)
		install (FILES qt.conf DESTINATION Nano.app/Contents/Resources)
		install (DIRECTORY ${Qt5_DIR}/../../QtCore.framework DESTINATION Nano.app/Contents/Frameworks)
		install (DIRECTORY ${Qt5_DIR}/../../QtGui.framework DESTINATION Nano.app/Contents/Frameworks)
		install (DIRECTORY ${Qt5_DIR}/../../QtPrintSupport.framework DESTINATION Nano.app/Contents/Frameworks)
		install (DIRECTORY ${Qt5_DIR}/../../QtTest.framework DESTINATION Nano.app/Contents/Frameworks)
		install (DIRECTORY ${Qt5_DIR}/../../QtWidgets.framework DESTINATION Nano.app/Contents/Frameworks)
		install (FILES "${Qt5_DIR}/../../../plugins/platforms/libqcocoa.dylib" DESTINATION Nano.app/Contents/PlugIns/platforms)
		install (FILES RaiBlocks.icns DESTINATION Nano.app/Contents/Resources)
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c
	elseif (WIN32)
		if ("${CMAKE_SIZEOF_VOID_P}" EQUAL "8")
			set (WIN_REDIST vc_redist.x64.exe)
		else ()
			set (WIN_REDIST vc_redist.x86.exe)
		endif ()
		set (CPACK_NSIS_EXTRA_INSTALL_COMMANDS "ExecWait '\\\"$INSTDIR\\\\${WIN_REDIST}\\\" /quiet /norestart'")
<<<<<<< HEAD
		set (CPACK_NSIS_MUI_ICON ${CMAKE_SOURCE_DIR}/Banano.ico)
		set (CPACK_NSIS_DISPLAY_NAME "Banano")
		set (CPACK_PACKAGE_NAME "Banano_Installer")
		set (CPACK_NSIS_PACKAGE_NAME "Banano ${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}")
		set (CPACK_NSIS_URL_INFO_ABOUT "https://banano.co.in")
		set (CPACK_NSIS_CONTACT "contact@banano.co.in")
		set (CPACK_NSIS_MENU_LINKS "banano_wallet.exe" "Banano Wallet" "https://banano.co.in" "Banano website")
=======
		set (CPACK_NSIS_MUI_ICON ${CMAKE_SOURCE_DIR}/RaiBlocks.ico)
		set (CPACK_NSIS_DISPLAY_NAME "Nano")
		set (CPACK_PACKAGE_NAME "Nano_Installer")
		set (CPACK_NSIS_PACKAGE_NAME "Nano ${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}")
		set (CPACK_NSIS_URL_INFO_ABOUT "https://nano.org")
		set (CPACK_NSIS_CONTACT "info@nano.org")
		set (CPACK_NSIS_MENU_LINKS "nano_wallet.exe" "Nano Wallet" "https://nano.org" "Nano website")
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c
		set (CPACK_NSIS_ENABLE_UNINSTALL_BEFORE_INSTALL ON)
		get_target_property (Qt5WindowsPlugin Qt5::QWindowsIntegrationPlugin LOCATION)
		get_filename_component (Qt5_bin_DIR ${Qt5_DIR}/../../../bin ABSOLUTE)
		install (TARGETS banano_wallet DESTINATION .)
		install (FILES ${CMAKE_CURRENT_BINARY_DIR}/${WIN_REDIST} DESTINATION .)
		install (FILES ${Qt5_bin_DIR}/libGLESv2.dll DESTINATION .)
		install (FILES ${Qt5_bin_DIR}/Qt5Core.dll DESTINATION .)
		install (FILES ${Qt5_bin_DIR}/Qt5Gui.dll DESTINATION .)
		install (FILES ${Qt5_bin_DIR}/Qt5Widgets.dll DESTINATION .)
		install (FILES ${Qt5_bin_DIR}/Qt5WinExtras.dll DESTINATION .)
		install (FILES ${Qt5WindowsPlugin} DESTINATION platforms)
	else ()
<<<<<<< HEAD
		install(TARGETS banano_wallet bananode banano_lib
=======
		install(TARGETS nano_wallet
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c
				RUNTIME DESTINATION bin
				LIBRARY DESTINATION lib
				ARCHIVE DESTINATION lib
		)
	endif ()
<<<<<<< HEAD
endif (BANANO_GUI)
=======
endif ()

set (CPACK_RESOURCE_FILE_LICENSE ${CMAKE_SOURCE_DIR}/LICENSE)
>>>>>>> bcc55f99bcdf5c03bd766639c1dcd14bcb6ee56c

include (CPack)
