INCLUDE(messmer/cmake/tools)

# -----------------------------------------
# Remove .c files that should not be linked
# -----------------------------------------

macro(REMOVE_ITEM_IF_EXISTS list item)
  LIST(FIND ${list} ${item} index)
  IF(${index} GREATER -1)
    LIST(REMOVE_AT ${list} ${index})
  ENDIF()
endmacro()
REMOVE_ITEM_IF_EXISTS(BII_LIB_SRC libcperciva/cpusupport/Build/cpusupport-X86-AESNI.c)
REMOVE_ITEM_IF_EXISTS(BII_LIB_SRC libcperciva/cpusupport/Build/cpusupport-X86-SSE2.c)
REMOVE_ITEM_IF_EXISTS(BII_LIB_SRC libcperciva/cpusupport/Build/cpusupport-X86-CPUID.c)
REMOVE_ITEM_IF_EXISTS(BII_test_main_SRC libcperciva/cpusupport/Build/cpusupport-X86-AESNI.c)
REMOVE_ITEM_IF_EXISTS(BII_test_main_SRC libcperciva/cpusupport/Build/cpusupport-X86-SSE2.c)
REMOVE_ITEM_IF_EXISTS(BII_test_main_SRC libcperciva/cpusupport/Build/cpusupport-X86-CPUID.c)

#-----------------------
# Biicode Initialization
#-----------------------

# Actually create targets: EXEcutables and libraries.
ADD_BII_TARGETS()

REQUIRE_GCC_VERSION(4.8)

#-------------------------------------------------------------------------
# Create cpusupport_config.h with information about supported CPU features
#-------------------------------------------------------------------------

set(ENV{CC} ${CMAKE_C_COMPILER})
EXECUTE_PROCESS(COMMAND /bin/sh ${CMAKE_CURRENT_LIST_DIR}/libcperciva/cpusupport/Build/cpusupport.sh OUTPUT_FILE ${CMAKE_CURRENT_BINARY_DIR}/cpusupport_config.h RESULT_VARIABLE result)
MESSAGE(STATUS ${result})
TARGET_INCLUDE_DIRECTORIES(${BII_BLOCK_TARGET} INTERFACE ${CMAKE_CURRENT_BINARY_DIR})

#-------------------------------------------------------------
# Add config files to include path so source files can find it
#-------------------------------------------------------------

TARGET_COMPILE_OPTIONS(${BII_BLOCK_TARGET} INTERFACE -DCONFIG_H_FILE=\"config_freebsd.h\" -DCPUSUPPORT_CONFIG_FILE=\"cpusupport_config.h\")
