cmake_minimum_required(VERSION 2.8)

project(qoauth)

set(QOAUTH_LIB_MAJOR_VERSION "1")
set(QOAUTH_LIB_MINOR_VERSION "0")
set(QOAUTH_LIB_PATCH_VERSION "1")

#---------------------------------------------------------------------------$
#                             QT
#---------------------------------------------------------------------------$
# Find Qt4
find_package(Qt4 4.8.1 COMPONENTS QtCore QtNetwork QtCrypto REQUIRED)

# Include the cmake file needed to use qt4
include(${QT_USE_FILE})

#------------------------------------------------------------------------------
#                             INCLUDE DIRECTORIES
#------------------------------------------------------------------------------

include_directories(
	${PROJECT_SOURCE_DIR}/src
	${PROJECT_BINARY_DIR}/src
	${QT_QTCRYPTO_INCLUDE_DIR}
)

SET(SOURCES
	src/interface.cpp
)

SET(HEADERS
	src/qoauth_global.h
	src/qoauth_namespace.h
	src/interface.h
	src/interface_p.h
)

SET(MOC_HEADERS
	${SOURCES}
)

#------------------------------------------------------------------------------
#    			Qt MACROS - Small trick to generate .cpp moc files
#------------------------------------------------------------------------------
MACRO (QT4_WRAP_CPP_CUSTOM outfiles )
    # get include dirs
    QT4_GET_MOC_FLAGS(moc_flags)
    QT4_EXTRACT_OPTIONS(moc_files moc_options ${ARGN})

    FOREACH (it ${moc_files})
      GET_FILENAME_COMPONENT(it ${it} ABSOLUTE)
      QT4_MAKE_OUTPUT_FILE(${it} moc_ cpp outfile)
      QT4_CREATE_MOC_COMMAND(${it} ${outfile} "${moc_flags}" "${moc_options}")
      SET(${outfiles} ${${outfiles}} ${outfile})
    ENDFOREACH(it)

  ENDMACRO (QT4_WRAP_CPP_CUSTOM)

qt4_wrap_cpp_custom(MOC_OUTFILES ${MOC_HEADERS})

#------------------------------------------------------------------------------
#                                LIBRARY
#------------------------------------------------------------------------------
add_library(qoauth SHARED
	${SOURCES}
	${HEADERS}
	${MOC_OUTFILES}
)

set_target_properties(qoauth PROPERTIES
	VERSION ${QOAUTH_LIB_MAJOR_VERSION}.${QOAUTH_LIB_MINOR_VERSION}.${QOAUTH_LIB_PATCH_VERSION}
	SOVERSION ${QOAUTH_LIB_MAJOR_VERSION}
)

target_link_libraries(qoauth
	${QT_LIBRARIES}
	qca
)

INSTALL( TARGETS qoauth 
	RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/bin
	LIBRARY DESTINATION ${LIB_INSTALL_DIR}
    ARCHIVE DESTINATION ${LIB_INSTALL_DIR}
)
