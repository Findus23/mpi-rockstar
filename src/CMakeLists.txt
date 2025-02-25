cmake_minimum_required(VERSION 3.10)

project(rockstar_cmake)

set (CMAKE_CXX_STANDARD 11)
set (CMAKE_C_STANDARD 99)

add_compile_options(-Wall -fno-math-errno -fPIC)


add_compile_definitions(
        ENABLE_HDF5
        H5_USE_16_API
        _LARGEFILE_SOURCE
        _LARGEFILE64_SOURCE
        _FILE_OFFSET_BITS=64
        _DEFAULT_SOURCE
        _POSIX_SOURCE
        _POSIX_C_SOURCE=200809L
        _SVID_SOURCE
        _DARWIN_C_SOURCE

        OUTPUT_RVMAX
        OUTPUT_NFW_CHI2
        OUTPUT_INERTIA_TENSOR
        DO_CONFIG_MPI
)

find_package(HDF5 REQUIRED)
find_package(MPI REQUIRED)
find_package(OpenMP REQUIRED)

find_package(PkgConfig REQUIRED)
pkg_check_modules(TIRPC REQUIRED libtirpc)

set(default_build_type "RelWithDebInfo")
if (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message(STATUS "Setting build type to '${default_build_type}' as none was specified.")
    set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE
            STRING "Choose the type of build." FORCE)
    # Set the possible values of build type for cmake-gui
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
            "Debug" "Release" "RelWithDebInfo")
endif ()

add_executable(mpi-rockstar_hdf5 mpi_main.cpp rockstar.c check_syscalls.c fof.c groupies.c subhalo_metric.c potential.c nfw.c jacobi.c fun_times.c universe_time.c hubble.c integrate.c distance.c config_vars.c config.c bounds.c inthash.c io/read_config.c merger.c io/meta_io.c io/io_internal.c io/io_internal_hdf5.c io/io_ascii.c io/stringparse.c io/io_gadget.c io/io_generic.c io/io_art.c io/io_tipsy.c io/io_bgc2.c io/io_util.c io/io_arepo.c io/io_gadget4.c io/io_swift.c io/io_hdf5.c io/io_kyf.c interleaving.c)

target_include_directories(mpi-rockstar_hdf5 PRIVATE ${HDF5_INCLUDE_DIRS})
target_include_directories(mpi-rockstar_hdf5 PRIVATE ${TIRPC_INCLUDE_DIRS})

target_link_libraries(mpi-rockstar_hdf5 PRIVATE ${HDF5_LIBRARIES})
target_link_libraries(mpi-rockstar_hdf5 PRIVATE MPI::MPI_CXX)
target_link_libraries(mpi-rockstar_hdf5 PRIVATE OpenMP::OpenMP_CXX OpenMP::OpenMP_C)
target_link_libraries(mpi-rockstar_hdf5 PRIVATE ${TIRPC_LIBRARIES})
target_compile_options(mpi-rockstar_hdf5 PRIVATE -Wall -Wextra)


add_executable(find_parents find_parents.c io/stringparse.c check_syscalls.c)
target_link_libraries(find_parents m)
