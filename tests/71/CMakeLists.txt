configure_file("${CMAKE_CURRENT_SOURCE_DIR}/input"
               "${CMAKE_CURRENT_BINARY_DIR}/input" COPYONLY )

configure_file("${CMAKE_CURRENT_SOURCE_DIR}/referenceTape33"
               "${CMAKE_CURRENT_BINARY_DIR}/referenceTape33" COPYONLY )
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/referenceTape34"
               "${CMAKE_CURRENT_BINARY_DIR}/referenceTape34" COPYONLY )
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/referenceTape35"
               "${CMAKE_CURRENT_BINARY_DIR}/referenceTape35" COPYONLY )

configure_file("${RESOURCES}/d-078_Pt_184-TENDL2021.endf"
               "${CMAKE_CURRENT_BINARY_DIR}/tape20" COPYONLY )

add_test( NAME "Test71"
          COMMAND ${Python3_EXECUTABLE} "../execute.py"
          WORKING_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}" )
