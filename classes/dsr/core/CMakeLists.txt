cmake_minimum_required( VERSION 2.8 )
project( dsr_core )
set(PROJECT_VERSION_MAJOR 1)
set(PROJECT_VERSION_MINOR 0)
set(PROJECT_VERSION_PATCH 0)
SET(RC_COMPONENT_PATH ${CMAKE_CURRENT_SOURCE_DIR} CACHE STRING "component base path" FORCE)
SET(RC_COMPONENT_DEVEL_PATH "${CMAKE_CURRENT_SOURCE_DIR}" CACHE STRING "component binary path" FORCE)
#SET(RC_COMPONENT_DEVEL_PATH "${WORKSPACE_PATH}/devel"  CACHE STRING "component binary path")
SET(RC_COMPONENT_INSTALL_PATH "/opt/robocomp" CACHE STRING "component install path")

#commented as now devel is merged into source space
#get_filename_component( COMPONENT_NAME ${CMAKE_CURRENT_SOURCE_DIR} DIRECTORY)
#STRING(REPLACE "${COMPONENT_NAME}/" " " COMPONENT_NAME  ${RC_COMPONENT_PATH})
#STRING(STRIP ${COMPONENT_NAME} COMPONENT_NAME)
#SET(COMPONENT_NAME ${COMPONENT_NAME} CACHE STRING "component name" FORCE)


IF ( "$ENV{ROBOCOMP}" STREQUAL "")
    MESSAGE(WARNING "ROBOCOMP variable not set. Using the default value: /opt/robocomp")
    SET (ENV{ROBOCOMP} "/opt/robocomp/")
ENDIF ( "$ENV{ROBOCOMP}" STREQUAL "")

IF(NOT EXISTS $ENV{ROBOCOMP}/cmake)
    MESSAGE(FATAL_ERROR "Couldn't find RoboComp in $ENV{ROBOCOMP}! Exiting...")
ENDIF(NOT EXISTS $ENV{ROBOCOMP}/cmake)

INCLUDE( $ENV{ROBOCOMP}/cmake/robocomp.cmake )
INCLUDE( $ENV{ROBOCOMP}/cmake/modules/qt.cmake )

find_package (Eigen3 3.3 REQUIRED NO_MODULE)

# Sources set
SET ( SOURCES
        #core
        $ENV{ROBOCOMP}/classes/dsr/core/types/crdt_types.cpp
        $ENV{ROBOCOMP}/classes/dsr/core/types/user_types.cpp
        $ENV{ROBOCOMP}/classes/dsr/core/rtps/dsrpublisher.cpp
        $ENV{ROBOCOMP}/classes/dsr/core/rtps/dsrsubscriber.cpp
        $ENV{ROBOCOMP}/classes/dsr/core/rtps/dsrparticipant.cpp
        $ENV{ROBOCOMP}/classes/dsr/core/topics/IDLGraphPubSubTypes.cxx
        $ENV{ROBOCOMP}/classes/dsr/core/topics/IDLGraph.cxx
        $ENV{ROBOCOMP}/classes/dsr/core/id_generator.cpp

        )


#set_property(SOURCE DSRGetID.h PROPERTY SKIP_AUTOGEN ON)
set(CMAKE_CXX_STANDARD 20)
add_definitions(-g -fmax-errors=1 -std=c++2a )
SET(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} "-fmax-errors=5" )
SET(SPECIFIC_LIBS  fastcdr fastrtps osgDB OpenThreads Eigen3::Eigen)


#ROBOCOMP
ROBOCOMP_INITIALIZE( $ENV{ROBOCOMP}/ )

# Specify construction and link process
ADD_LIBRARY( dsr_core SHARED ${SOURCES})
# ${Ice_LIBRARIES}
TARGET_LINK_LIBRARIES( dsr_core ${LIBS} ${STATIC_LIBS} ${SPECIFIC_LIBS} ${QT_LIBRARIES} ${Ice_LIBRARIES} )
install(TARGETS dsr_core
        LIBRARY DESTINATION /opt/robocomp/lib/
        )


