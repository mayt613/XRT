# -----------------------------------------------------------------------------

set(Boost_USE_STATIC_LIBS ON)               # Only find static libraries
set(Boost_USE_MULTITHREADED ON)             # Multi-threaded libraries

find_package(Boost REQUIRED COMPONENTS system filesystem program_options)
include_directories(${Boost_INCLUDE_DIRS})

# -----------------------------------------------------------------------------

include_directories(${CMAKE_BINARY_DIR})

# -----------------------------------------------------------------------------

# Collect files outside of this directory
file(GLOB XBUTIL_V2_BASE_FILES
  "xbutil.cpp"
  "../common/XBMain.cpp"
  "../common/XBUtilities.cpp"
  "../common/SubCmd.cpp"
  "../common/OptionOptions.cpp"
  "../common/XBHelpMenus.cpp"
)

# Collect local directory files
file(GLOB XBUTIL_V2_SUBCMD_FILES
  "XBReport.cpp"
  "XBDatabase.cpp"
  "SubCmdClock.cpp"
  "SubCmdDD.cpp"
  "SubCmdDmaTest.cpp"
  "SubCmdDump.cpp"
  "SubCmdExamine.cpp"
  "SubCmdList.cpp"
  "SubCmdM2MTest.cpp"
  "SubCmdMem.cpp"
  "SubCmdP2P.cpp"
  "SubCmdProgram.cpp"
  "SubCmdQuery.cpp"
  "SubCmdReset.cpp"
  "SubCmdScan.cpp"
  "SubCmdTop.cpp"
  "SubCmdValidate.cpp"
  "SubCmdVersion.cpp"
  "SubCmdAdvanced.cpp"
  "OO_Clock.cpp"
  "OO_MemRead.cpp"
  "OO_MemWrite.cpp"
  "OO_P2P.cpp"
)

# Merge the files into one collection
set(XBUTIL_V2_FILES_SRCS ${XBUTIL_V2_BASE_FILES} ${XBUTIL_V2_SUBCMD_FILES})
set(XBUTIL_V2_SRCS ${XBUTIL_V2_FILES_SRCS})

# Determine the name of the executable
if(WIN32)
  set(XBUTIL2_NAME "xbutil")     # Yes, on windows the file name will be xbutil
  set(XRT_LOADER_SCRIPTS "xbutil" "xbutil.bat")
else()
  set(XBUTIL2_NAME "xbutil2")
  set(XRT_LOADER_SCRIPTS "xbutil")
endif()

add_executable(${XBUTIL2_NAME} ${XBUTIL_V2_SRCS})

# Determine what functionality should be added
if(WIN32)
  target_compile_definitions(${XBUTIL2_NAME} PUBLIC ENABLE_DEPRECATED_2020_1_SUBCMDS)
else()
  target_compile_definitions(${XBUTIL2_NAME} PUBLIC ENABLE_DEPRECATED_2020_1_SUBCMDS)
endif()


# Add the supporting libraries
if(WIN32)
  target_link_libraries(
    ${XBUTIL2_NAME} PRIVATE
    Boost::filesystem
    Boost::program_options
    Boost::system
    xrt_core
    xrt_core_static
    xrt_coreutil_static
  )

else()
  target_link_libraries(
    ${XBUTIL2_NAME}
    xrt_core
    xrt_core_static
    xrt_coreutil_static
    boost_filesystem
    boost_system
    boost_program_options
    pthread
    uuid
    dl
  )
endif()

# Install our built executable
install (TARGETS ${XBUTIL2_NAME} RUNTIME DESTINATION ${XRT_INSTALL_UNWRAPPED_DIR})
install (PROGRAMS ${XRT_LOADER_SCRIPTS} DESTINATION ${XRT_INSTALL_BIN_DIR})

# -----------------------------------------------------------------------------
