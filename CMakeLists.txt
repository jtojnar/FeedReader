project (FeedReader C)
cmake_minimum_required (VERSION 2.6)
enable_testing()

list (APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)
include_directories(${CMAKE_SOURCE_DIR}/libVilistextum)
include_directories(${CMAKE_SOURCE_DIR}/libgtkimageview)
include_directories(${CMAKE_BINARY_DIR}/libIvy)
include_directories(${CMAKE_SOURCE_DIR})

add_definitions(-g)
add_definitions(-w)
add_definitions(-rdynamic)

include(GNUInstallDirs)
set (DATADIR "${CMAKE_INSTALL_PREFIX}/share")
set (PKGDATADIR "${DATADIR}/FeedReader")
set (PKGLIBDIR "${CMAKE_INSTALL_LIBDIR}/FeedReader")
set (GETTEXT_PACKAGE ${CMAKE_PROJECT_NAME})
set (FEEDREADER_NAME "feedreader")
set (WEBEXTENSION_NAME webextension)
set (RELEASE_NAME "FeedReader")
set (VERSION "2.2-dev")
set (GLIB_VERSION 2.50)

find_package(PkgConfig)
find_package(Vala REQUIRED)
include(ValaVersion)
include(ValaPrecompile)
include(GResource)
ensure_vala_version("0.36" MINIMUM)

pkg_check_modules(LIBUNITY QUIET unity)

add_definitions(-DGETTEXT_PACKAGE=\"${GETTEXT_PACKAGE}\")
add_subdirectory(po)
add_subdirectory(schemas)
add_subdirectory(libVilistextum)

include(GetGitRevisionDescription)
get_git_head_revision(GIT_REFSPEC GIT_SHA1)
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/Constants.vala.in" "${CMAKE_SOURCE_DIR}/Constants.vala" @ONLY)
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/data/org.gnome.FeedReader.appdata.xml.in" "${CMAKE_SOURCE_DIR}/data/org.gnome.FeedReader.appdata.xml" @ONLY)
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/data/org.gnome.FeedReader.desktop.in" "${CMAKE_SOURCE_DIR}/data/org.gnome.FeedReader.desktop" @ONLY)
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/data/org.gnome.FeedReader-autostart.desktop.in" "${CMAKE_SOURCE_DIR}/data/org.gnome.FeedReader-autostart.desktop" @ONLY)

set(MODULES
	gtk+-3.0>=3.22
	json-glib-1.0
	gee-0.8
	libsoup-2.4
	sqlite3
	libsecret-1
	libxml-2.0
	rest-0.7
	webkit2gtk-4.0>=2.18
	gstreamer-1.0
	gstreamer-pbutils-1.0
	libpeas-1.0
	libcurl
	goa-1.0)

if(LIBUNITY_FOUND)
	list(APPEND MODULES unity)
	message(STATUS "libunity: ON")
endif(LIBUNITY_FOUND)

pkg_check_modules(DEPS REQUIRED ${MODULES})

add_definitions(${DEPS_CFLAGS} -DGOA_API_IS_SUBJECT_TO_CHANGE)
link_libraries(${DEPS_LIBRARIES} -lm)
link_directories(${DEPS_LIBRARY_DIRS})

add_subdirectory(WebExtension)
add_subdirectory(libgtkimageview)
add_subdirectory(libgd)
add_subdirectory(libIvy)


################################################################################

set(UI_PACKAGES
	glib-2.0>=${GLIB_VERSION}
	gtk+-3.0
	json-glib-1.0
	libsoup-2.4
	gee-0.8
	libsecret-1
	rest-0.7
	WebKit2-4.0
	gstreamer-1.0
	gstreamer-pbutils-1.0
	libpeas-1.0
	libxml-2.0
	goa-1.0
	posix
	gio-2.0)

vala_precompile(FEEDREADER ${FEEDREADER_NAME}
	src/FavIcon.vala
	src/FeedReader.vala
	src/Widgets/AddPopover.vala
	src/Widgets/ArticleRow.vala
	src/Widgets/ArticleView.vala
	src/Widgets/ArticleViewHeader.vala
	src/Widgets/ArticleViewLoadProgress.vala
	src/Widgets/ArticleViewUrlOverlay.vala
	src/Widgets/BackendInfoPopover.vala
	src/Widgets/CategorieRow.vala
	src/Widgets/ColorCircle.vala
	src/Widgets/ColorPopover.vala
	src/Widgets/ColumnView.vala
	src/Widgets/ColumnViewHeader.vala
	src/Widgets/FeedList.vala
	src/Widgets/FeedListFooter.vala
	src/Widgets/FeedRow.vala
	src/Widgets/FullscreenButton.vala
	src/Widgets/FullscreenHeader.vala
	src/Widgets/HoverButton.vala
	src/Widgets/ImagePopup.vala
	src/Widgets/InAppNotification.vala
	src/Widgets/InfoBar.vala
	src/Widgets/LoginPage.vala
	src/Widgets/LoginRow.vala
	src/Widgets/MainWindow.vala
	src/Widgets/MediaPlayer.vala
	src/Widgets/MediaButton.vala
	src/Widgets/MediaRow.vala
	src/Widgets/ModeButton.vala
	src/Widgets/RemovePopover.vala
	src/Widgets/ResetPage.vala
	src/Widgets/ServiceSettingsPopover.vala
	src/Widgets/ShareRow.vala
	src/Widgets/ShortcutsWindow.vala
	src/Widgets/ServiceInfo.vala
	src/Widgets/Setting.vala
	src/Widgets/SettingsDialog.vala
	src/Widgets/SharePopover.vala
	src/Widgets/SimpleHeader.vala
	src/Widgets/SpringCleanPage.vala
	src/Widgets/TagRow.vala
	src/Widgets/TagPopover.vala
	src/Widgets/TagPopoverRow.vala
	src/Widgets/UpdateButton.vala
	src/Widgets/WebLoginPage.vala
	src/Widgets/ArticleList/ArticleList.vala
	src/Widgets/ArticleList/ArticleListBox.vala
	src/Widgets/ArticleList/ArticleListEmptyLabel.vala
	src/Widgets/ArticleList/ArticleListScroll.vala
	src/Share/share.vala
	src/Share/ServiceSetup.vala
	src/Share/ShareAccountInterface.vala
	src/Logger.vala
	src/Enums.vala
	src/FuncUtils.vala
	src/DataBaseReadOnly.vala
	src/ListUtils.vala
	src/Password.vala
	src/Settings.vala
	src/StringUtils.vala
	src/Structs.vala
	src/Utils.vala
	src/QueryBuilder.vala
	src/SQLite.vala
	src/ContentGrabber/grabberUtils.vala
	src/ContentGrabber/stringPair.vala
	src/Model/Feed.vala
	src/Model/Category.vala
	src/Model/Article.vala
	src/Model/Tag.vala
	src/Model/CachedAction.vala
	src/Model/InterfaceState.vala
	src/Model/ShareAccount.vala
	src/ActionCache.vala
	src/Backend/Backend.vala
	src/DataBaseWriteAccess.vala
	src/Notification.vala
	src/CachedActionManager.vala
	src/Backend/FeedServer.vala
	src/Backend/FeedServerInterface.vala
	src/Backend/OPMLparser.vala
	src/ContentGrabber/grabberConfig.vala
	src/ContentGrabber/grabber.vala
	Constants.vala

PACKAGES
	${UI_PACKAGES}

OPTIONS
	--target-glib=${GLIB_VERSION}
	--header=FeedReader.h
	--vapi=FeedReader.vapi
	--enable-experimental

GENERATE_VAPI
	FeedReader

CUSTOM_VAPIS
	${CMAKE_BINARY_DIR}/libIvy/ivy.vapi
	vapi/libvilistextum.vapi
	vapi/gtkimageview.vapi
	vapi/gd-1.0.vapi
	vapi/sqlite3.vapi
	vapi/webkit2gtk-4.0.vapi
)

GLIB_COMPILE_RESOURCES(GRESOURCES SOURCE data/org.gnome.FeedReader.gresource.xml)

add_executable(${FEEDREADER_NAME} ${FEEDREADER} ${GRESOURCES})
target_link_libraries(${FEEDREADER_NAME} vilistextum gtkimageview gd ivy)


################################################################################


# build plugins
add_subdirectory(plugins)


################################################################################


# uninstall target
configure_file(
	"${CMAKE_CURRENT_SOURCE_DIR}/cmake/uninstall.cmake"
	"${CMAKE_CURRENT_BINARY_DIR}/uninstall.cmake"
	IMMEDIATE @ONLY)

add_custom_target(uninstall COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/uninstall.cmake)

install (TARGETS ${FEEDREADER_NAME} RUNTIME DESTINATION bin)
install (FILES ${PROJECT_BINARY_DIR}/WebExtension/libwebextension.so DESTINATION ${PKGLIBDIR})
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/org.gnome.FeedReader.desktop DESTINATION ${DATADIR}/applications/)
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/org.gnome.FeedReader-autostart.desktop DESTINATION ${PKGDATADIR})
install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/org.gnome.FeedReader.appdata.xml DESTINATION ${DATADIR}/appdata/)
install (DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/data/application-icons/hicolor DESTINATION ${CMAKE_INSTALL_PREFIX}/share/icons)
install (CODE "execute_process (COMMAND gtk-update-icon-cache -t ${CMAKE_INSTALL_PREFIX}/share/icons/hicolor)")
install (DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/data/GrabberConfig DESTINATION ${PKGDATADIR})
