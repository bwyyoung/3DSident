cmake_minimum_required(VERSION 3.6)
project(3DSident)

set(CMAKE_CXX_STANDARD 11)

#	function processes each sub-directory and then adds each source file in directory
#	each function should cascade back upward in setting variables so that the bottom directory
#	adds its source first, and then the level above it adds its source and passes both up, and so on...
function(recursively_include_src which_directory return_variable )
    if( ${return_variable} STREQUAL "headers" )
        set( file_extension ".h" )
    else( ${return_variable} STREQUAL "headers" )
        set( file_extension ".c" )
    endif( ${return_variable} STREQUAL "headers" )

    #	get list of all files for this directory
    file( GLOB this_directory_all_files "${which_directory}/*" )

    #	get list of source from this directory
    file( GLOB this_directory_src "${which_directory}/*${file_extension}" )

    #	get rid of *.txt
    file( GLOB this_directory_txt "${which_directory}/*.txt" )

    #	get directories only
    if( this_directory_all_files AND this_directory_txt )
        #	remove txt from list of files to get list of directories
        list( REMOVE_ITEM this_directory_all_files ${this_directory_txt} )
    endif( this_directory_all_files AND this_directory_txt )
    if( this_directory_all_files AND this_directory_src )
        #	remove source from list of files to get list of directories
       list( REMOVE_ITEM this_directory_all_files ${this_directory_src} )
        set( this_directory_directories ${this_directory_all_files} )
        #	for each sub-directory, call self with sub-directory as arg
        foreach( this_sub_directory ${this_directory_directories} )
            recursively_include_src( ${this_sub_directory} ${return_variable} )
        endforeach( this_sub_directory ${this_directory_directories} )
   endif( this_directory_all_files AND this_directory_src )

    #	add source files to ${rb_rpdb_src} in PARENT_SCOPE
    if( ${return_variable} STREQUAL "headers" )
        set( rb_rpdb_headers ${rb_rpdb_headers} ${this_directory_src} PARENT_SCOPE )
    else( ${return_variable} STREQUAL "headers" )
        set( rb_rpdb_src ${rb_rpdb_src} ${this_directory_src} PARENT_SCOPE )
    endif( ${return_variable} STREQUAL "headers" )
    set( rb_rpdb_directories ${rb_rpdb_directories} ${which_directory})
    set( rb_rpdb_directories ${rb_rpdb_directories} ${which_directory} PARENT_SCOPE)

endfunction( recursively_include_src which_directory return_variable )
#recursively_include_src(/opt/devkitpro headers)

MACRO(HEADER_DIRECTORIES return_list thedir)
    FILE(GLOB_RECURSE new_list ${thedir}/*.h)
    SET(dir_list "")
    FOREACH(file_path ${new_list})
        GET_FILENAME_COMPONENT(dir_path ${file_path} PATH)
        SET(dir_list ${dir_list} ${dir_path})
    ENDFOREACH()
    LIST(REMOVE_DUPLICATES dir_list)
    SET(${return_list} ${dir_list})
ENDMACRO()

set(rlist "")
HEADER_DIRECTORIES(rlist /opt/devkitpro)
#MESSAGE( STATUS "rlist:         " ${rlist} )

include_directories("${rlist}")


set(SOURCE_FILES
        source/actu.c
        source/actu.h
        source/am.c
        source/am.h
        source/cfgs.c
        source/cfgs.h
        source/frd.c
        source/frd.h
        source/fs.c
        source/fs.h
        source/main.c
        source/mcu.c
        source/mcu.h
        source/screenshot.c
        source/screenshot.h
        source/utils.c
        source/utils.h)


add_executable(3DSident ${SOURCE_FILES} ${DEVKITPRO})