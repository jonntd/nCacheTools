IF (WIN32)
#	IF ( NOT CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS )
#		MESSAGE ( FATAL_ERROR "Enable CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS otherwise, module will not be loadable in Maya" )
#	ENDIF ()
ENDIF ()
ADD_DEFINITIONS ( ${MAYA_DEFINITIONS} )
ADD_LIBRARY ( nCacheTranslator SHARED
  nCacheTranslator.cpp
  )

TARGET_LINK_LIBRARIES ( nCacheTranslator
 ${MTOA_REQUIRED_LIBRARIES}
 ${NCACHE_REQUIRED_LIBRARIES}
)

IF (APPLE)
  SET_TARGET_PROPERTIES ( nCacheTranslator
    PROPERTIES
    LINK_FLAGS "-undefined dynamic_lookup"
    PREFIX ""
    )
ELSEIF (WIN32)
  SET_TARGET_PROPERTIES ( nCacheTranslator
    PROPERTIES
    LINK_FLAGS "/export:initializeExtension /export:deinitializeExtension"
    )

ELSE (APPLE)
  SET_TARGET_PROPERTIES ( nCacheTranslator
    PROPERTIES
    PREFIX ""
    SUFFIX ".so"
    )
ENDIF (APPLE)

INSTALL ( TARGETS
  nCacheTranslator
  DESTINATION
  arnold/extensions
)
