find_package(QT NAMES Qt6 Qt5 COMPONENTS Widgets REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Widgets REQUIRED)

set(PROJECT_SOURCES
    about_dialog.cpp
    info_dialog.cpp
    layerdelegate.cpp
    layerlistmodel.cpp
    layerwidget.cpp
    layer_info_dialog.cpp
    main.cpp
    mainwindow.cpp
    mapwidget.cpp
    styles_model.cpp
    forms/about.ui
    forms/info.ui
    forms/layer_info.ui    
    mapnik_viewer.qrc
)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(mapnik-viewer
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
else()
    if(ANDROID)
        add_library(mapnik-viewer SHARED
            ${PROJECT_SOURCES}
        )
    else()
        add_executable(mapnik-viewer
            ${PROJECT_SOURCES}
        )
    endif()
endif()

set_target_properties(mapnik-viewer PROPERTIES
    AUTOUIC_SEARCH_PATHS forms
    AUTORCC ON    
    AUTOUIC ON
    AUTOMOC ON
    LIBRARY_OUTPUT_DIRECTORY "${MAPNIK_OUTPUT_DIR}"
    RUNTIME_OUTPUT_DIRECTORY "${MAPNIK_OUTPUT_DIR}"
    ARCHIVE_OUTPUT_DIRECTORY "${MAPNIK_OUTPUT_DIR}/lib"
)

target_link_libraries(mapnik-viewer PRIVATE 
    Qt${QT_VERSION_MAJOR}::Widgets
    mapnik::agg
    mapnik::mapnik
)

file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/viewer.ini 
"[mapnik]
plugins_dir=${PLUGINS_INSTALL_DIR}
fonts/1/dir=${FONTS_INSTALL_DIR}"
)

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(mapnik-viewer)
endif()

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/viewer.ini DESTINATION bin)
mapnik_install_utility(mapnik-viewer)
