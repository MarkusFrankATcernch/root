# Copyright (C) 1995-2019, Rene Brun and Fons Rademakers.
# All rights reserved.
#
# For the licensing terms see $ROOTSYS/LICENSE.
# For the list of contributors see $ROOTSYS/README/CREDITS.

############################################################################
# CMakeLists.txt file for building ROOT io/io package
############################################################################

ROOT_LINKER_LIBRARY(RIO
  src/TArchiveFile.cxx
  src/TBufferFile.cxx
  src/TBufferText.cxx
  src/TBufferIO.cxx
  src/TBufferJSON.cxx
  src/TBufferMerger.cxx
  src/TBufferMergerFile.cxx
  src/TCollectionProxyFactory.cxx
  src/TContainerConverters.cxx
  src/TEmulatedMapProxy.cxx
  src/TEmulatedCollectionProxy.cxx
  src/TDirectoryFile.cxx
  src/TFileCacheRead.cxx
  src/TFileMerger.cxx
  src/TFree.cxx
  src/TFileCacheWrite.cxx
  src/TFilePrefetch.cxx
  src/TFile.cxx
  src/TFPBlock.cxx
  src/TGenCollectionStreamer.cxx
  src/TGenCollectionProxy.cxx
  src/TKey.cxx
  src/TKeyMapFile.cxx
  src/TLockFile.cxx
  src/TMemFile.cxx
  src/TMapFile.cxx
  src/TMakeProject.cxx
  src/TStreamerInfo.cxx
  src/TStreamerInfoActions.cxx
  src/TStreamerInfoReadBuffer.cxx
  src/TStreamerInfoWriteBuffer.cxx
  src/TZIPFile.cxx
  $<TARGET_OBJECTS:RootPcmObjs>
  LIBRARIES
    ${CMAKE_DL_LIBS}
  DEPENDENCIES
    Core
    Thread
)

target_include_directories(RIO PRIVATE res ${CMAKE_SOURCE_DIR}/core/clib/res)

if(root7)
  set(RIO_EXTRA_HEADERS ROOT/RFile.hxx)
  target_sources(RIO PRIVATE v7/src/RFile.cxx)
endif()

ROOT_GENERATE_DICTIONARY(G__RIO
  ROOT/TBufferMerger.hxx
  TArchiveFile.h
  TBufferFile.h
  TBufferText.h
  TBufferIO.h
  TBufferJSON.h
  TCollectionProxyFactory.h
  TContainerConverters.h
  TEmulatedMapProxy.h
  TEmulatedCollectionProxy.h
  TDirectoryFile.h
  TFileCacheRead.h
  TFileMerger.h
  TFree.h
  TFileCacheWrite.h
  TFilePrefetch.h
  TFile.h
  TFPBlock.h
  TGenCollectionStreamer.h
  TGenCollectionProxy.h
  TKey.h
  TKeyMapFile.h
  TLockFile.h
  TMemFile.h
  TMapFile.h
  TMakeProject.h
  TStreamerInfoActions.h
  TVirtualCollectionIterators.h
  TStreamerInfo.h
  TVirtualObject.h
  TVirtualArray.h
  TZIPFile.h
  ${RIO_EXTRA_HEADERS}
  STAGE1
  NOTARGET
  MODULE
    RIO
  LINKDEF
    LinkDef.h
  OPTIONS
    -writeEmptyRootPCM
  DEPENDENCIES
    Core
    Thread
)

# TStreamerInfoReadBuffer in O0 needs 6k on the stack. It is called
# recursively, quickly exhausting the stack. Prevent that by forcing
# the many scope-local vars to share their stack space / become
# registers, thanks to the optimizer.
if(MSVC)
  set_source_files_properties(src/TStreamerInfoReadBuffer.cxx COMPILE_FLAGS "/O2")
else()
  set_source_files_properties(src/TStreamerInfoReadBuffer.cxx COMPILE_FLAGS "-O3")
endif()

ROOT_INSTALL_HEADERS()

ROOT_ADD_TEST_SUBDIRECTORY(test)
