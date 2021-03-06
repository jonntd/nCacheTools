CMAKE_MINIMUM_REQUIRED ( VERSION 2.8 )

PROJECT ( nCacheTools )

FIND_PACKAGE ( Threads REQUIRED )

FIND_PACKAGE ( Boost 1.52 REQUIRED COMPONENTS filesystem system )
FIND_PACKAGE ( GoogleLog REQUIRED )
IF ( BUILD_ALEMBIC_TOOLS )
  FIND_PACKAGE ( Alembic REQUIRED )
  FIND_PACKAGE ( HDF5 COMPONENTS C HL REQUIRED )
ENDIF ()
FIND_PACKAGE ( ILMBase REQUIRED )

# Mutually exclusive
IF ( BUILD_HOUDINI_TOOLS )
  FIND_PACKAGE ( HDK REQUIRED )
ELSEIF ( BUILD_MTOA_TOOLS )
  FIND_PACKAGE ( Maya REQUIRED )
  FIND_PACKAGE ( Arnold REQUIRED )
  FIND_PACKAGE ( MtoA REQUIRED )
ELSEIF ( BUILD_3DFM_TOOLS )
  FIND_PACKAGE ( Maya REQUIRED )
  FIND_PACKAGE ( Delight REQUIRED )
  FIND_PACKAGE ( DelightForMaya REQUIRED )
ENDIF ()

INCLUDE_DIRECTORIES ( ${Boost_INCLUDE_DIR} )
INCLUDE_DIRECTORIES ( ${GLOG_INCLUDE_DIR} )
IF ( BUILD_ALEMBIC_TOOLS )
  INCLUDE_DIRECTORIES ( ${ALEMBIC_INCLUDE_DIR} )
ENDIF ()
INCLUDE_DIRECTORIES ( ${ILMBASE_INCLUDE_DIRS} )

IF (WIN32)
  ADD_DEFINITIONS ( -DGOOGLE_GLOG_DLL_DECL= -DINCL_EXTRA_HTON_FUNCTIONS )
  SET ( ADDITIONAL_LIBRARIES Ws2_32 )
ENDIF ()

ADD_SUBDIRECTORY ( lib )

SET  ( NCACHE_REQUIRED_LIBRARIES
  nCacheParser
  ${Boost_LIBRARIES}
  ${GLOG_glog_LIBRARY}
  ${ADDITIONAL_LIBRARIES}
  ${CMAKE_THREAD_LIBS_INIT}
  )


INCLUDE_DIRECTORIES ( lib )
ADD_SUBDIRECTORY ( apps )

IF ( BUILD_HOUDINI_TOOLS )
  INCLUDE_DIRECTORIES ( ${HDK_INCLUDE_DIR} )
  ADD_SUBDIRECTORY ( houdini )
ELSEIF ( BUILD_MTOA_TOOLS )
  INCLUDE_DIRECTORIES ( ${MAYA_INCLUDE_DIR} )
  INCLUDE_DIRECTORIES ( ${ARNOLD_INCLUDE_DIR} )
  INCLUDE_DIRECTORIES ( ${MTOA_INCLUDE_DIR} )
  IF (WIN32)
  SET ( MTOA_REQUIRED_LIBRARIES
    ${MtoA_mtoa_api_LIBRARY}
    ${Arnold_ai_LIBRARY}
    ${MAYA_OpenMayaAnim_LIBRARY}
    ${MAYA_OpenMayaRender_LIBRARY}
    ${MAYA_OpenMaya_LIBRARY}
    ${MAYA_Foundation_LIBRARY}
    )
  ELSE (WIN32)
  SET ( MTOA_REQUIRED_LIBRARIES
    ${MtoA_mtoa_api_LIBRARY}
    # ${Arnold_ai_LIBRARY}
    ${MAYA_OpenMayaAnim_LIBRARY}
    ${MAYA_OpenMayaRender_LIBRARY}
    ${MAYA_OpenMaya_LIBRARY}
    ${MAYA_Foundation_LIBRARY}
    )
  ENDIF (WIN32)
  IF (APPLE)
    SET ( CMAKE_SKIP_BUILD_RPATH  FALSE)
    SET ( CMAKE_BUILD_WITH_INSTALL_RPATH FALSE) 
    SET ( CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
    SET ( CMAKE_INSTALL_RPATH "${ARNOLD_LOCATION}/bin:${MTOA_LOCATION}/bin" )
    MESSAGE ( "CMAKE_INSTALL_RPATH ${ARNOLD_LOCATION}/bin:${MTOA_LOCATION}/bin" )
  ELSE ()
    SET ( CMAKE_INSTALL_RPATH "${MAYA_LOCATION}/lib:${MTOA_LOCATION}/lib" )
  ENDIF()
  ADD_SUBDIRECTORY ( maya/mtoa )
ELSEIF ( BUILD_3DFM_TOOLS )
ENDIF()
