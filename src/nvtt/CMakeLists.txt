PROJECT(nvtt)

ADD_SUBDIRECTORY(squish)

SET(NVTT_SRCS
    nvtt.h nvtt.cpp
    nvtt_wrapper.h nvtt_wrapper.cpp
    ClusterFit.h ClusterFit.cpp
    Compressor.h
    BlockCompressor.h BlockCompressor.cpp
    CompressorDX9.h CompressorDX9.cpp
    CompressorDX10.h CompressorDX10.cpp
    CompressorDX11.h CompressorDX11.cpp
    CompressorDXT1.h CompressorDXT1.cpp
    CompressorDXT5_RGBM.h CompressorDXT5_RGBM.cpp
    CompressorRGB.h CompressorRGB.cpp
    Context.h Context.cpp
    QuickCompressDXT.h QuickCompressDXT.cpp
    OptimalCompressDXT.h OptimalCompressDXT.cpp
    SingleColorLookup.h SingleColorLookup.cpp
    CompressionOptions.h CompressionOptions.cpp
    InputOptions.h InputOptions.cpp
    OutputOptions.h OutputOptions.cpp
    TaskDispatcher.h #TaskDispatcher.cpp
    Surface.h Surface.cpp
    CubeSurface.h CubeSurface.cpp
    cuda/CudaUtils.h cuda/CudaUtils.cpp
    cuda/CudaMath.h
    cuda/BitmapTable.h
    cuda/CudaCompressorDXT.h cuda/CudaCompressorDXT.cpp)

IF (CUDA_FOUND)
    ADD_DEFINITIONS(-DHAVE_CUDA)
    CUDA_COMPILE(CUDA_SRCS cuda/CompressKernel.cu)
    SET(NVTT_SRCS ${NVTT_SRCS} ${CUDA_SRCS})
    SET(LIBS ${LIBS} ${CUDA_LIBRARIES})
    INCLUDE_DIRECTORIES(${CUDA_INCLUDE_DIRS})
ENDIF (CUDA_FOUND)

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})

ADD_DEFINITIONS(-DNVTT_EXPORTS)

IF(NVTT_SHARED)
    ADD_LIBRARY(nvtt SHARED ${NVTT_SRCS} $<TARGET_OBJECTS:squish>)
ELSE(NVTT_SHARED)
    ADD_LIBRARY(nvtt ${NVTT_SRCS} $<TARGET_OBJECTS:squish>)
ENDIF(NVTT_SHARED)

TARGET_COMPILE_OPTIONS(nvtt PUBLIC ${OpenMP_CXX_FLAGS})

TARGET_LINK_LIBRARIES(nvtt ${LIBS} nvcore nvimage nvthread nvmath)

INSTALL(TARGETS nvtt EXPORT nvtt-targets
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib/static)
INSTALL(EXPORT nvtt-targets DESTINATION share/cmake)


INSTALL(FILES nvtt.h DESTINATION include/nvtt)


ADD_SUBDIRECTORY(tools)
ADD_SUBDIRECTORY(tests)
