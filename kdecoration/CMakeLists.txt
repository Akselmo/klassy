add_definitions(-DTRANSLATION_DOMAIN="klassy_kwin_deco")

find_package(KF5 ${KF5_MIN_VERSION} REQUIRED COMPONENTS CoreAddons GuiAddons ConfigWidgets WindowSystem I18n IconThemes)
find_package(Qt${QT_MAJOR_VERSION} ${QT_MIN_VERSION} CONFIG REQUIRED COMPONENTS DBus)

################# includes #################
include_directories(${CMAKE_SOURCE_DIR}/libbreezecommon)
include_directories(${CMAKE_BINARY_DIR}/libbreezecommon)

################# newt target #################
### plugin classes
set(breezedecoration_SRCS
    breezebutton.cpp
    breezedecoration.cpp
    breezesettingsprovider.cpp
)

### config classes
set(breezedecoration_config_SRCS
    config/breezeconfigwidget.cpp
    config/breezedetectwidget.cpp
    config/breezeexceptiondialog.cpp
    config/breezeexceptionlistwidget.cpp
    config/breezeexceptionmodel.cpp
    config/breezeitemmodel.cpp
    config/buttonsizing.cpp
    config/windowoutlineopacity.cpp
    config/loadpreset.cpp
    config/addpreset.cpp
)

ki18n_wrap_ui(breezedecoration_config_SRCS
   config/ui/breezeconfigurationui.ui
   config/ui/breezeexceptiondialog.ui
   config/ui/breezeexceptionlistwidget.ui
   config/ui/buttonsizing.ui
   config/ui/windowoutlineopacity.ui
   config/ui/loadpreset.ui
   config/ui/addpreset.ui
)

qt_add_resources(breezedecoration_config_SRCS config/ui/klassydecoration_config.qrc)

add_library(klassydecoration_STATIC STATIC ${breezedecoration_config_SRCS})
# Needed to link this static lib to shared libs
set_property(TARGET klassydecoration_STATIC PROPERTY POSITION_INDEPENDENT_CODE ON)

#This is removed as kdecoration InternalSettings is now generated from libbreezecommon
#kconfig_add_kcfg_files(klassydecoration_STATIC breezesettings.kcfgc)
target_link_libraries(klassydecoration_STATIC
    PUBLIC
        Qt::Core
        Qt::Gui
        Qt::DBus
        KDecoration2::KDecoration
    PRIVATE
        #KF5::ConfigCore
        klassycommon5
        KF5::CoreAddons
        KF5::ConfigWidgets
        KF5::GuiAddons
        KF5::I18n
        KF5::IconThemes
        KF5::WindowSystem
)

### build library
add_library(klassydecoration MODULE
    ${breezedecoration_SRCS}
    ${breezedecoration_config_PART_FORMS_HEADERS})

target_link_libraries(klassydecoration
    PRIVATE
        klassycommon5
        klassydecoration_STATIC
        KF5::CoreAddons
        KF5::ConfigWidgets
        KF5::GuiAddons
        KF5::I18n
        KF5::IconThemes
        KF5::WindowSystem)

install(TARGETS klassydecoration DESTINATION ${KDE_INSTALL_PLUGINDIR}/org.kde.kdecoration2)

add_subdirectory(config)
